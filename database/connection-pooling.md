---
tags:
  - adatbazis
  - teljesitmeny
  - serverless
datum: 2026-03-06
szint: "🏗️ Builder"
kapcsolodo:
  - "[[database/supabase|Supabase]]"
  - "[[database/prisma|Prisma]]"
  - "[[database/drizzle|Drizzle]]"
  - "[[database/sql-adatbazisok|SQL adatbázisok]]"
  - "[[backend/edge-function|Edge Function]]"
  - "[[cloud/vercel|Vercel]]"
  - "[[_moc/moc-database|MOC - Database]]"
---

# Connection Pooling

## Összefoglaló

Minden PostgreSQL kapcsolat **egy külön process-t** igényel a szerveren (~10MB RAM). Ha serverless function-ökből vagy [[backend/edge-function|edge function]]-ökből közvetlenül csatlakozol, minden request új kapcsolatot nyit — és a DB gyorsan elfogyasztja az összes elérhető connection-t. A **connection pooling** ezt oldja meg: egy köztes réteg tartja a nyitott kapcsolatokat, és a kérések ezeket újrahasznosítják.

## A probléma

```mermaid
graph TD
    subgraph "Pooling NÉLKÜL"
        F1[Function 1] --> DB1[(PostgreSQL<br/>max_connections: 100)]
        F2[Function 2] --> DB1
        F3[Function 3] --> DB1
        F4[...Function N] --> DB1
        F5[💥 Connection<br/>limit elérve] --> DB1
    end

    subgraph "Pooling-GAL"
        S1[Function 1] --> POOL[Connection Pool<br/>pl. PgBouncer]
        S2[Function 2] --> POOL
        S3[Function 3] --> POOL
        S4[...Function N] --> POOL
        POOL -->|"5-20 állandó<br/>connection"| DB2[(PostgreSQL)]
    end

    style F5 fill:#e74c3c,color:white
    style POOL fill:#3ecf8e,color:white
```

**Miért fontos ez serverless-nél?**

- Hagyományos szervernél 1 process = 1 DB connection, ez stabil
- Serverless-nél (Vercel, Cloudflare Workers) **minden kérés egy új izolált function** — mindegyik új connection-t akar
- [[database/supabase|Supabase]] free tier: **max 60 kapcsolat**, Pro: 200
- 100 párhuzamos request = 100 connection = 💥 `too many connections`

## Pooling megoldások

### PgBouncer

A legrégebbi és legstabilabb connection pooler. Egy könnyű proxy ami a kliensek és a PostgreSQL között ül.

```
Alkalmazás → PgBouncer (port 6543) → PostgreSQL (port 5432)
```

**Három mód:**

| Mód | Mikor ad vissza connection-t | Mikor használd |
|-----|------------------------------|----------------|
| **Transaction** | Tranzakció végén | Serverless, a legtöbb eset |
| **Session** | Session lezáráskor | Prepared statement-ek kellenek |
| **Statement** | Minden statement után | Egyszerű query-k, max throughput |

> [!tip] Supabase beépített PgBouncer-t ad
> A [[database/supabase|Supabase]] dashboardon a Settings → Database → Connection Pooling résznél találod a pooler URL-t. Port **6543** a pooled connection, port **5432** a direct.

### Supavisor

A Supabase saját, Elixir-alapú connection poolere (a PgBouncer utódja Supabase-ben). Tenant-aware, vagyis multi-tenant környezetben is jól skálázódik.

```bash
# Supabase connection string-ek:
# Direct (migration-höz, admin műveletek)
postgresql://postgres:PASS@db.REF.supabase.co:5432/postgres

# Pooled (alkalmazás kód, serverless)
postgresql://postgres.REF:PASS@aws-0-eu-central-1.pooler.supabase.com:6543/postgres
```

### Prisma Accelerate

A [[database/prisma|Prisma]] saját managed connection pooling szolgáltatása. Edge-ről is működik.

```typescript
// prisma/schema.prisma
datasource db {
  provider  = "postgresql"
  url       = env("DATABASE_URL")        // direct
  directUrl = env("DIRECT_DATABASE_URL") // migration-höz
}
```

```bash
# .env
DATABASE_URL="prisma://accelerate.prisma-data.net/?api_key=..."
DIRECT_DATABASE_URL="postgresql://user:pass@host:5432/db"
```

Előnye: global edge cache + connection pooling egyben. Hátránya: fizetős, vendor lock-in.

### Neon pooler

Ha Neon-t használsz DB-nek, beépített pooler van:

```bash
# Pooled (alkalmazáshoz)
postgresql://user:pass@ep-xxx.eu-central-1.aws.neon.tech/dbname?sslmode=require&pgbouncer=true

# Direct (migration-höz)
postgresql://user:pass@ep-xxx.eu-central-1.aws.neon.tech/dbname?sslmode=require
```

## Mikor kell connection pooling?

| Környezet | Kell-e pooling? | Miért |
|-----------|-----------------|-------|
| [[cloud/vercel|Vercel]] Serverless | **Igen** | Minden request új function, új connection |
| Edge Function | **Igen** | Rövid életű, sok párhuzamos |
| Docker / VPS (1 process) | **Nem feltétlen** | Egy process, stabil connection-ök |
| Next.js dev (lokális) | **Nem** | Egy process, kevés connection |
| Railway / Fly.io | **Ajánlott** | Több replica = több connection |

## Drizzle + Pooling setup

A [[database/drizzle|Drizzle]]-ben a driver szintjén állítod:

```typescript
import { drizzle } from 'drizzle-orm/postgres-js'
import postgres from 'postgres'

// Serverless: rövid életű connection
const client = postgres(process.env.DATABASE_URL!, {
  max: 1,              // serverless function-ben 1 elég
  idle_timeout: 20,
  connect_timeout: 10,
})

export const db = drizzle(client)
```

```typescript
// Hagyományos szerver: pool kezelés
import postgres from 'postgres'

const client = postgres(process.env.DATABASE_URL!, {
  max: 10,             // max 10 connection a pool-ban
  idle_timeout: 30,    // 30 sec után zárja az idle connection-t
})
```

## Prisma + Supabase pooling

```bash
# .env — a pooled URL-t használd az alkalmazásban
DATABASE_URL="postgresql://postgres:PASS@db.REF.supabase.co:6543/postgres?pgbouncer=true"

# Direct URL migration-höz (prisma migrate deploy)
DIRECT_DATABASE_URL="postgresql://postgres:PASS@db.REF.supabase.co:5432/postgres"
```

```prisma
// schema.prisma
datasource db {
  provider  = "postgresql"
  url       = env("DATABASE_URL")
  directUrl = env("DIRECT_DATABASE_URL")
}
```

> [!warning] PgBouncer + Prisma: transaction mode
> Ha PgBouncer `transaction` módban van (Supabase default), a Prisma interactive transaction-ök (`$transaction`) működnek, de a `prepared statement`-ek nem. Add hozzá `?pgbouncer=true` a connection string-hez, hogy a Prisma tudja ezt kezelni.

## Monitoring: honnan tudod, hogy elfogytak a connection-ök?

```sql
-- Aktív connection-ök száma
SELECT count(*) FROM pg_stat_activity;

-- Connection-ök részletezve (ki, mit csinál)
SELECT pid, usename, application_name, state, query_start
FROM pg_stat_activity
WHERE datname = 'postgres'
ORDER BY query_start DESC;

-- Max connections beállítás
SHOW max_connections;
```

Ha `remaining connection slots are reserved for superuser` hibát látsz, elfogytak a connection-ök — pooling kell.

## Kapcsolódó

- [[database/supabase|Supabase]] — beépített Supavisor/PgBouncer connection pooling
- [[database/prisma|Prisma]] — Prisma Accelerate connection pooling + edge cache
- [[database/drizzle|Drizzle]] — postgres.js driver szintű pooling
- [[database/sql-adatbazisok|SQL adatbázisok]] — PostgreSQL alapok
- [[backend/edge-function|Edge Function]] — edge runtime ahol pooling kritikus
- [[cloud/vercel|Vercel]] — serverless platform ahol pooling nélkül elfogynak a connection-ök
- [[_moc/moc-database|MOC - Database]]
