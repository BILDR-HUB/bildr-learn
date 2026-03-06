# MOC - Database

> [!tldr]
> Ez a Map of Content összegyűjti az összes adatbázishoz kapcsolódó jegyzetet. SQL alapoktól az index optimalizáción át az ORM-ekig.

---

## SQL alapok

| Note | Leírás |
|------|--------|
| [[database/sql-adatbazisok|SQL adatbazisok]] | Adatbázisok összehasonlítása: SQLite, PostgreSQL, MySQL — mikor melyiket, hol futtatod, hosting opciók |
| SQL Index szabályok | Mikor tegyél indexet, composite index-ek, B-tree működés, EXPLAIN használata — teljesítmény optimalizálás |
| [[database/postgresql-specifikus|PostgreSQL specifikus tudás]] | JSONB mezők, full-text search, Row Level Security (RLS) — haladó PostgreSQL feature-ök |
| [[database/database-design-patterns|Database design patterns]] | Normalizálás, denormalizálás, soft delete — adatbázis tervezési minták |
| Next.js Data Cache és revalidáció | Server-side cache réteg DB query eredményekhez — unstable_cache, revalidatePath, mikor cache vs mikor index |

## In-memory / NoSQL

| Note | Leírás |
|------|--------|
| [[database/redis|Redis]] | In-memory adatbázis — cache, session store, rate limiter, leaderboard, pub/sub, ~100K req/sec |
| [[database/mongodb|MongoDB]] | Document-based NoSQL — flexibilis séma, JSON dokumentumok, mikor érdemes használni |

## ORM-ek

| Note | Leírás |
|------|--------|
| [[database/drizzle|Drizzle]] | TypeScript-first ORM — SQL-közeli, könnyű, edge/serverless kompatibilis, te kontrollálod a query-ket |
| [[database/prisma|Prisma]] | Magas szintű TypeScript ORM — `schema.prisma` fájl, auto-generált type-safe client, Prisma Migrate és Studio |

## Haladó témák

| Note | Leírás |
|------|--------|
| [[database/connection-pooling|Connection Pooling]] | PgBouncer, Supavisor, Prisma Accelerate — miért kell serverless-nél, hogyan állítsd be |
| [[database/database-migration-strategies|Database migration stratégiák]] | Zero-downtime migration, expand-contract minta, rollback tervezés |
| [[database/vector-adatbazisok|Vector adatbázisok]] | pgvector, Pinecone, embedding-ek tárolása RAG rendszerekhez |
| [[database/ai-generalt-sql|AI-generált SQL]] | LLM-ek használata SQL query íráshoz, natural language to SQL minták és biztonsági szabályok |
| [[database/saas-adatbazis-tervezes|SaaS adatbázis tervezés]] | Multi-tenant sémák, tenant izoláció, billing-hez kapcsolt adatmodellek |

---

## Kapcsolódó note-ok

| Note | Kapcsolat |
|------|-----------|
| [[database/supabase|Supabase]] | Managed PostgreSQL adatbázis + Auth + Storage + Realtime egy csomagban |
| [[cloud/railway|Railway]] | Saját PostgreSQL/MySQL/Redis szerver futtatása managed platformon |
| [[cloud/docker-compose|Docker Compose]] | Lokális fejlesztéshez PostgreSQL konténer a compose fájlban |

---

## Tanulási útvonal

Ajánlott sorrend ha nulláról kezded:
1. [[database/sql-adatbazisok|SQL adatbazisok]] — melyik adatbázist válaszd, mik a különbségek, hol futtatod
2. [[database/database-design-patterns|Database design patterns]] — normalizálás, denormalizálás, soft delete
3. SQL Index szabályok — hogyan gyorsítsd fel a lekérdezéseket index-ekkel, mikor kell és mikor nem
4. [[database/drizzle|Drizzle]] VAGY [[database/prisma|Prisma]] — válassz ORM-et: SQL-közelit (Drizzle) vagy magas absztrakciót (Prisma)
5. [[database/redis|Redis]] — in-memory cache és session store: mikor kell, hogyan működik, use case-ek
6. [[database/connection-pooling|Connection Pooling]] — ha serverless-re deploy-olsz, ez kötelező tudás
7. [[database/postgresql-specifikus|PostgreSQL specifikus tudás]] — JSONB, full-text search, RLS haladó feature-ök

> [!tip] Drizzle vs Prisma döntés
> Ha ismered az SQL-t és szereted kontrollálni a query-ket: **Drizzle**. Ha gyorsan akarsz haladni és nem baj a nagyobb bundle: **Prisma**. Mindkettő TypeScript-first és type-safe.

---

## Hézagok

- [x] NoSQL / In-memory — Redis → [[database/redis|Redis]]
- [x] MongoDB → [[database/mongodb|MongoDB]] — document-based NoSQL, mikor érdemes használni
- [x] Database migration strategies → [[database/database-migration-strategies|Database migration stratégiák]] — zero-downtime migration, rollback tervezés
- [x] Connection pooling → [[database/connection-pooling|Connection Pooling]] — PgBouncer, Prisma Accelerate, Supavisor, miért kell serverless-nél
- [x] Database design patterns → [[database/database-design-patterns|Database design patterns]] — normalizálás, denormalizálás, soft delete
- [x] PostgreSQL specifikus tudás → [[database/postgresql-specifikus|PostgreSQL specifikus tudás]] — JSON mezők, full-text search, RLS (Row Level Security)
- [x] Vector adatbázisok → [[database/vector-adatbazisok|Vector adatbázisok]] — pgvector, Pinecone, embedding-ek tárolása RAG rendszerekhez
- [x] AI-generált SQL → [[database/ai-generalt-sql|AI-generált SQL]] — LLM-ek használata SQL query íráshoz, natural language to SQL minták
- [x] SaaS adatbázis tervezés → [[database/saas-adatbazis-tervezes|SaaS adatbázis tervezés]] — multi-tenant sémák, tenant izoláció, billing-hez kapcsolt adatmodellek
