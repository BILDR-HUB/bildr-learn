---
tags:
  - adatbazis
  - gcp
  - postgresql
  - incident
datum: 2026-04-03
szint: "🧱 Brick"
kapcsolodo:
  - "[[database/postgresql|PostgreSQL]]"
  - "[[database/neon|Neon]]"
  - "[[database/sql-adatbazisok|SQL adatbázisok]]"
---

# PostgreSQL authentikáció és Cloud SQL

Hogyan működik a [[database/postgresql|PostgreSQL]] password authentikáció, mi történik ha a jelszó invalid, és hogyan debuggolható GCP Cloud Run + Cloud SQL kontextusban. Egy valós production incident elemzése.

---

## A PostgreSQL auth modell

A PostgreSQL authentikáció a `pg_hba.conf` fájlban van konfigurálva — ez határozza meg, hogy melyik user milyen forrásból milyen módszerrel csatlakozhat.

### Auth módszerek

| Módszer | Hogyan működik | Mikor használják |
|---------|---------------|-----------------|
| **md5** | Jelszó hash ellenőrzés | Klasszikus, legacy |
| **scram-sha-256** | Modern challenge-response | Default PG14+ óta, Cloud SQL |
| **trust** | Nincs jelszó — bárki beengedve | Lokális dev (Docker) |
| **peer/ident** | OS user = DB user egyezés | Unix socket lokálisan |
| **cert** | SSL client certificate | Enterprise, managed szolgáltatások |

> [!warning] Fontos különbség
> A `md5` és `scram-sha-256` NEM csereszabatos. Ha a szerver `scram-sha-256`-ot vár de a kliens `md5`-öt küld (vagy fordítva), auth hiba lesz — nem "wrong password", hanem "authentication method mismatch".

### Mi történik egy connection-nél?

```mermaid
sequenceDiagram
    participant App as Next.js App
    participant Driver as postgres-js
    participant PG as PostgreSQL

    App->>Driver: query("SELECT ...")
    Driver->>PG: TCP connect (host:5432)
    PG-->>Driver: SSL negotiation
    Driver->>PG: StartupMessage (user, database)
    PG-->>Driver: AuthenticationSASL (scram-sha-256)
    Driver->>PG: SASLInitialResponse (password hash)
    PG-->>Driver: AuthenticationSASLFinal
    Note over PG: Ha jelszó HIBÁS → ErrorResponse
    PG-->>Driver: "password authentication failed for user X"
    Driver-->>App: Error thrown
```

---

## Cloud SQL specifikusságok

A GCP Cloud SQL egy managed [[database/postgresql|PostgreSQL]] instance. A connection nem közvetlenül TCP-n megy, hanem **Unix socket**-en keresztül a Cloud SQL Auth Proxy-n át.

### Connection path Cloud Run-ból

```
Next.js (Cloud Run)
  → postgres-js driver
    → Unix socket: /cloudsql/PROJECT:REGION:INSTANCE
      → Cloud SQL Proxy (sidecar)
        → Cloud SQL PostgreSQL instance
```

> [!info] Miért Unix socket?
> Cloud Run-on a Cloud SQL Auth Proxy automatikusan mountol egy Unix socket-et a `/cloudsql/` path-ra. Ez gyorsabb és biztonságosabb mint TCP — nincs SSL overhead, és a IAM kezeli az instance-szintű hozzáférést. A **user-level auth** (jelszó) ettől még szükséges.

### A DATABASE_URL felépítése

```
postgresql://USER:PASSWORD@localhost/DB_NAME?host=/cloudsql/PROJECT:REGION:INSTANCE
```

Két rétegű auth:
1. **Instance-szintű** — IAM: a Cloud Run service account-nak Cloud SQL Client role kell
2. **User-szintű** — PostgreSQL: a `USER:PASSWORD` pár a DB user táblában

> [!danger] Gyakori hiba
> Ha az IAM hozzáférés rendben van, de a PostgreSQL jelszó hibás, a hibaüzenet: `password authentication failed for user "X"`. Ez **nem** IAM hiba — a proxy-n sikeresen átjutott, de a PostgreSQL belső auth bukott el.

---

## Az incident: "password authentication failed"

### Tünetek
- Cloud Run → 500 error minden route-on
- GCP logs: `password authentication failed for user "app-user"`
- A Cloud Scheduler cron job-ok is 500-at kapnak
- Lokálisan ugyanaz a connection string [[database/neon|Neon]] DB-re mutatott (más probléma)

### Root cause
A Cloud SQL `app-user` user jelszava entrópiát vesztett — valaki vagy valami (GCP Console, egy script, vagy manuális módosítás) megváltoztatta a jelszót, de a Cloud Run env var-ban a régi jelszó maradt.

### Debugging flow

| Lépés | Parancs | Mit derített ki |
|-------|---------|----------------|
| 1. Cloud Run logok | `gcloud logging read 'service_name="example-project" AND severity>=ERROR'` | 500 error, de nincs részlet |
| 2. Stderr logok | `gcloud logging read ... textPayload:("cause")` | `password authentication failed for user "app-user"` |
| 3. Env var ellenőrzés | `gcloud run services describe ... --format="yaml(spec.template.spec.containers[0].env)"` | DATABASE_URL jelszó kiolvasva |
| 4. User létezés | `gcloud sql users list --instance=example-project-db` | `app-user` user létezik |
| 5. Jelszó reset | `gcloud sql users set-password app-user --instance=example-project-db --password="..."` | Jelszó visszaállítva |

> [!success] Megoldás
> `gcloud sql users set-password` — a jelszó resetelése az env var-ban szereplő értékre azonnal megoldotta a problémát. Nem kellett újradeployolni — a postgres-js driver a következő connection-nél az új jelszóval csatlakozott.

---

## Hogyan kerüld el

- **Secret Manager használata** DATABASE_URL-hez — ne env var-ban tárold a jelszót, hanem `gcloud secrets`-ben. Így nem lehet véletlenül felülírni
- **Ha password auth fail jön** → először a jelszót ellenőrizd (`gcloud sql users set-password`), ne az IAM-ot
- **Lokális dev-hez** a Docker Compose `trust` auth-ot használ — ott nincs jelszó probléma, de a connection string más (`localhost:5432`)
- **Ne használd a GCP Console SQL felületet** jelszó változtatásra — az könnyen desync-be hozza a deploy-t

---

## Kapcsolódó

- [[database/postgresql|PostgreSQL]] — az adatbázis motor ami mögötte van
- [[database/neon|Neon]] — serverless PostgreSQL, más auth modell (connection pooler + jelszó a connection string-ben)
- [[database/sql-adatbazisok|SQL adatbázisok]] — SQL adatbázisok összehasonlítása
