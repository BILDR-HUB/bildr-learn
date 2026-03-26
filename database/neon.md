---
tags:
  - adatbazis
  - postgresql
  - serverless
datum: 2026-03-26
szint: "🧱 Brick"
kapcsolodo:
  - "[[database/postgresql-specifikus|PostgreSQL]]"
  - "[[database/supabase|Supabase]]"
  - "[[database/drizzle|Drizzle]]"
  - "[[database/sql-adatbazisok|SQL adatbázisok]]"
  - "[[cloud/cloudflare|Cloudflare]]"
---

# Neon

**Kategória:** `adatbázis` / `serverless PostgreSQL`
**URL:** https://neon.tech

---

## Mi ez és mire jó?

> [!tldr] Egy mondatban
> Serverless [[database/postgresql-specifikus|PostgreSQL]] - scale-to-zero (nem fizetsz ha nem használod), database branching (mint [[foundations/git-es-github|Git]] branch, de adatbázisra), és instant cold start.

A **Neon** egy managed PostgreSQL szolgáltatás, ami a hagyományos "mindig fut egy szerver" modell helyett serverless architektúrát használ. A compute és a storage szét van választva: ha nincs query, a compute leáll (scale-to-zero), és csak akkor indul újra, amikor kérés érkezik.

A killer feature a **database branching**: egy branch a teljes adatbázis pillanatfelvétele (copy-on-write), amit másodpercek alatt létrehozhatsz. Ez dev/staging környezetekhez, migrációk teszteléséhez, és PR preview-khoz ideális - minden PR kap saját adatbázis branch-et.

---

## Mikor hasznos?

- **Side project / MVP** - scale-to-zero miatt nincs költség ha nem használják, Free tier bőséges
- **PR preview environment** - minden PR kap saját DB branch-et, izoláltan tesztelhető
- **Migráció tesztelés** - branch-en futtatod a migrációt, ha OK, merge-ölöd production-be
- **[[cloud/vercel|Vercel]]/[[cloud/cloudflare|Cloudflare]] edge** - Neon serverless drivere edge function-ökből is elérhető
- **Dev/staging szeparáció** - branch-elés olcsóbb mint külön DB instance

### Mikor NE használd

- **Auth + Storage + API is kell** - [[database/supabase|Supabase]] egyben adja, Neon csak adatbázis
- **Magas és folyamatos terhelés** - ha 24/7 fut a DB, dedicated instance (RDS) olcsóbb lehet
- **Cold start érzékeny app** - scale-to-zero után az első query lassabb (~500ms)

---

## Árazás

| Csomag | Ár | Mit ad |
|--------|-----|--------|
| **Free** | $0 | 0.5 GiB storage, 1 branch, scale-to-zero |
| **Launch** | $19/hó | 10 GiB, 10 branch, point-in-time recovery |
| **Scale** | $69/hó | 50 GiB, korlátlan branch, read replica |
| **Enterprise** | Egyedi | SLA, dedicated support |

---

## Neon vs [[database/supabase|Supabase]]

| Szempont | Neon | Supabase |
|----------|------|-----------|
| **Fókusz** | Csak adatbázis (PostgreSQL) | Adatbázis + Auth + Storage + API |
| **Branching** | Natív, instant | Nincs |
| **Scale-to-zero** | Igen | Pauzálás (Free tier, de lassú wake-up) |
| **Edge support** | Serverless driver, websocket | Nincs natív edge driver |
| **Auth** | Nincs - külön auth megoldás kell | Beépítve |
| **Ár** | DB-only, olcsóbb ha csak DB kell | Teljes platform ár |
| **Mikor válaszd** | Ha csak DB kell + branching fontos | Ha mindent egyben akarsz |

---

## AI-natív fejlesztés

Neon MCP tool-ként is elérhető Claude Code-ban - közvetlenül tudsz [[database/sql-adatbazisok|SQL]]-t futtatni, branch-et létrehozni, és schema-t vizsgálni a terminálból. Ez különösen hasznos migrációk írásakor és tesztelésekor: az AI létrehoz egy branch-et, lefuttatja a migrációt, és megmutatja a schema diff-et - mindezt anélkül hogy a production DB-t érintenéd.

> [!tip] Hogyan használd AI-val
> - *"Hozz létre egy Neon branch-et a production DB-ből, futtasd rajta ezt a migrációt, és mutasd meg a schema diff-et"*
> - *"Állíts be Drizzle-t Neon serverless driver-rel edge function-höz"*
> - *"Listázd a Neon projekt branch-eit és töröld a régi PR preview branch-eket"*

---

## Kapcsolódó

- [[database/postgresql-specifikus|PostgreSQL]] - Neon PostgreSQL-t futtat a háttérben
- [[database/supabase|Supabase]] - alternatíva ha auth + storage is kell
- [[database/drizzle|Drizzle]] - ORM, natív Neon driver támogatás
- [[database/sql-adatbazisok|SQL adatbázisok]] - SQL alapok
- [[cloud/cloudflare|Cloudflare]] - Neon a Cloudflare stack-ben
