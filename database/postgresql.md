---
tags:
  - adatbazis
  - postgresql
  - sql
datum: 2026-03-26
szint: "🌱 Newcomer"
kapcsolodo:
  - "[[database/postgresql-specifikus|PostgreSQL specifikus tudás]]"
  - "[[database/supabase|Supabase]]"
  - "[[database/neon|Neon]]"
  - "[[database/drizzle|Drizzle]]"
  - "[[database/prisma|Prisma]]"
  - "[[database/sql-adatbazisok|SQL adatbázisok]]"
  - "[[database/sql-index-szabalyok|SQL Index szabályok]]"
---

# PostgreSQL

**Kategória:** `adatbázis` / `relációs`
**URL:** https://www.postgresql.org

---

## Mi ez és mire jó?

> [!tldr] Egy mondatban
> A világ legfejlettebb nyílt forráskódú relációs adatbázisa - ACID-kompatibilis, bővíthető, és gyakorlatilag az iparági standard minden komolyabb projekthez.

A **PostgreSQL** (ejtsd: "posztgresz") egy 35+ éves, battle-tested relációs adatbázis-kezelő. Nem csak [[database/sql-adatbazisok|SQL]]-t tud: JSONB támogatás (dokumentum-szerű lekérdezések), full-text search, GIS (PostGIS), és saját extension rendszer teszi alkalmassá szinte minden felhasználási esetre.

A modern web stack-ben PostgreSQL az adatbázis layer alapértelmezett választása. A legtöbb managed szolgáltatás ([[database/supabase|Supabase]], [[database/neon|Neon]], Railway, [[cloud/vercel|Vercel]] Postgres) is PostgreSQL-t futtat a háttérben - tehát ha PostgreSQL-t tanulsz, mindenhol használható tudást építesz.

---

## Mikor hasznos?

- **Bármilyen production web app** - user adatok, tranzakciók, relációk kezelése
- **Ha Supabase/Neon-t használsz** - a háttérben PostgreSQL fut, tehát az SQL tudás közvetlenül alkalmazható
- **JSON + relációs adat együtt** - JSONB oszlopokkal rugalmas schema-t kapsz SQL lekérdezésekkel
- **Full-text search** - egyszerűbb keresési feladatokra nem kell Elasticsearch, elég a beépített tsvector

### Mikor NE használd

- **Nagyon egyszerű, lokális adat** - SQLite elég (pl. Electron app, mobil, CLI tool)
- **Dokumentum-orientált, schema nélküli adat** - MongoDB rugalmasabb erre
- **Extrém write throughput (millió/s)** - Cassandra, ScyllaDB specializáltabb

---

## PostgreSQL vs MySQL vs SQLite

| Szempont | PostgreSQL | MySQL | SQLite |
|----------|-----------|-------|--------|
| **Típus** | Enterprise relációs | Web-fókuszú relációs | Embedded, fájl-alapú |
| **ACID** | Teljes | Engine-függő (InnoDB: igen) | Igen |
| **JSON** | JSONB - natív, indexelhető | JSON - alapszintű | JSON functions |
| **Full-text search** | Beépített tsvector | Beépített, de limitáltabb | FTS5 extension |
| **Extensions** | Gazdag (PostGIS, pgvector, pg_cron) | Limitált | Minimális |
| **Managed opciók** | Supabase, Neon, RDS, Railway | PlanetScale, RDS | Turso, lokális |
| **Mikor válaszd** | Legtöbb production app | Legacy WordPress, egyszerű CRUD | Embedded, lokális |

---

## AI-natív fejlesztés

PostgreSQL az egyik legjobban támogatott adatbázis az AI toolokban. Ha megmondod a Claude Code-nak hogy "PostgreSQL + Drizzle" vagy "PostgreSQL + Prisma", pontosan tudja a szintaxist, a migrációkat, és az ORM-specifikus pattern-eket. A pgvector extension-nel vektoros keresést is tudsz, ami AI/embedding alkalmazásokhoz ideális.

> [!tip] Hogyan használd AI-val
> - *"Írj egy Drizzle migrációt ami PostgreSQL-ben létrehoz egy `projects` táblát UUID primary key-jel és RLS policy-kkal"*
> - *"Optimalizáld ezt a PostgreSQL lekérdezést - adj hozzá indexeket és EXPLAIN ANALYZE-t"*
> - *"Állíts be pgvector-t embedding kereséshez - Drizzle schema + query"*

---

## Alternatívák (managed PostgreSQL)

| Szolgáltatás | Erőssége | Ár |
|-------------|----------|-----|
| **[[database/supabase|Supabase]]** | PostgreSQL + Auth + Storage + API egyben | Free / $25/hó |
| **[[database/neon|Neon]]** | Serverless, branching, scale-to-zero | Free / $19/hó |
| **Railway** | Egyszerű deploy, jó DX | $5/hó + usage |
| **AWS RDS** | Enterprise, full kontroll | Drágább, komplexebb |

> [!info] Haladó PostgreSQL funkciók
> A JSONB, full-text search és Row Level Security (RLS) részletes bemutatójához lásd: [[database/postgresql-specifikus|PostgreSQL specifikus tudás]].

---

## Kapcsolódó

- [[database/postgresql-specifikus|PostgreSQL specifikus tudás]] - JSONB, full-text search, RLS
- [[database/supabase|Supabase]] - managed PostgreSQL + Auth + API
- [[database/neon|Neon]] - serverless PostgreSQL branching-gel
- [[database/drizzle|Drizzle]] - TypeScript ORM, natív PostgreSQL támogatás
- [[database/prisma|Prisma]] - ORM alternatíva PostgreSQL-hez
- [[database/sql-adatbazisok|SQL adatbázisok]] - SQL alapok és összehasonlítás
- [[database/sql-index-szabalyok|SQL Index szabályok]] - PostgreSQL index stratégiák
