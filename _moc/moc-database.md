# MOC - Database

> [!tldr]
> Ez a Map of Content összegyűjti az összes adatbázishoz kapcsolódó jegyzetet. SQL alapoktól az index optimalizáción át az ORM-ekig.

---

## SQL alapok

| Note | Leírás |
|------|--------|
| [[database/sql-adatbazisok|SQL adatbazisok]] | Adatbázisok összehasonlítása: SQLite, PostgreSQL, MySQL — mikor melyiket, hol futtatod, hosting opciók |
| SQL Index szabályok | Mikor tegyél indexet, composite index-ek, B-tree működés, EXPLAIN használata — teljesítmény optimalizálás |
| Next.js Data Cache és revalidáció | Server-side cache réteg DB query eredményekhez — unstable_cache, revalidatePath, mikor cache vs mikor index |

## In-memory / NoSQL

| Note | Leírás |
|------|--------|
| [[database/redis|Redis]] | In-memory adatbázis — cache, session store, rate limiter, leaderboard, pub/sub, ~100K req/sec |

## ORM-ek

| Note | Leírás |
|------|--------|
| [[database/drizzle|Drizzle]] | TypeScript-first ORM — SQL-közeli, könnyű, edge/serverless kompatibilis, te kontrollálod a query-ket |
| [[database/prisma|Prisma]] | Magas szintű TypeScript ORM — `schema.prisma` fájl, auto-generált type-safe client, Prisma Migrate és Studio |

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
2. SQL Index szabályok — hogyan gyorsítsd fel a lekérdezéseket index-ekkel, mikor kell és mikor nem
3. [[database/drizzle|Drizzle]] VAGY [[database/prisma|Prisma]] — válassz ORM-et: SQL-közelit (Drizzle) vagy magas absztrakciót (Prisma)
4. [[database/redis|Redis]] — in-memory cache és session store: mikor kell, hogyan működik, use case-ek

> [!tip] Drizzle vs Prisma döntés
> Ha ismered az SQL-t és szereted kontrollálni a query-ket: **Drizzle**. Ha gyorsan akarsz haladni és nem baj a nagyobb bundle: **Prisma**. Mindkettő TypeScript-first és type-safe.

---

## Hézagok

- [x] NoSQL / In-memory — Redis → [[database/redis|Redis]]
- [ ] MongoDB — document-based NoSQL, mikor érdemes használni
- [ ] Database migration strategies — zero-downtime migration, rollback tervezés
- [ ] Connection pooling — PgBouncer, Prisma Accelerate, miért kell serverless-nél
- [ ] Database design patterns — normalizálás, denormalizálás, soft delete
- [ ] PostgreSQL specifikus tudás — JSON mezők, full-text search, RLS (Row Level Security)
- [ ] Vector adatbázisok — pgvector, Pinecone, embedding-ek tárolása RAG rendszerekhez
- [ ] AI-generált SQL — LLM-ek használata SQL query íráshoz, natural language to SQL minták
- [ ] SaaS adatbázis tervezés — multi-tenant sémák, tenant izoláció, billing-hez kapcsolt adatmodellek
