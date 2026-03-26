---
tags: [backend, api, query-language]
datum: 2026-03-26
szint: "🧱 Scout"
kapcsolodo:
  - "[[backend/trpc|tRPC]]"
  - "[[backend/express|Express]]"
  - "[[backend/hono|Hono]]"
  - "[[frontend/nextjs|Next.js]]"
  - "[[database/postgresql-specifikus|PostgreSQL]]"
  - "[[database/prisma|Prisma]]"
  - "[[backend/websocket|WebSocket]]"
---

# GraphQL

**Kategória:** `API` (query language / API architektúra)
**Típus:** Specifikáció (nem library - implementáció kell hozzá)

---

## Mi ez és mire jó?

A **GraphQL** egy query nyelv API-khoz. A REST-tel ellentétben a **kliens határozza meg, milyen adatot akar** - nem a szerver diktálja az endpoint struktúrát. Egy végponton (`/graphql`) keresztül bármilyen lekérdezést küldhetsz, és pontosan azt kapod vissza, amit kértél.

> [!tldr] Egy mondatban
> REST = fix menü (minden endpoint fix adatot ad). GraphQL = a la carte (te mondod meg, mit kérsz, és pontosan azt kapod).

---

## REST vs GraphQL vs tRPC

| Szempont | REST | **GraphQL** | [[backend/trpc|tRPC]] |
|----------|------|-------------|----------|
| **Paradigma** | Endpoint-alapú | Query-alapú | Procedure-alapú |
| **Típusbiztonság** | Nincs (OpenAPI kell) | Schema-alapú | End-to-end [[foundations/typescript-vs-python|TypeScript]] |
| **Over-fetching** | Gyakori (fix response) | Nincs (kliens választ) | Nincs (TS inference) |
| **Under-fetching** | Gyakori (N+1 endpoint) | Nincs (nested query) | Nincs |
| **Tanulási görbe** | Alacsony | Közepes-magas | Alacsony (ha TS) |
| **Tooling** | Postman, curl | Apollo DevTools, GraphiQL | tRPC panel |
| **Több kliens (web + mobile)** | OK | Kiváló | Csak TypeScript kliensek |
| **Ökoszisztéma** | Hatalmas | Nagy | Növekvő |

> [!tip] Döntési fa
> **Fullstack TypeScript (egy csapat, egy repo)?** - [[backend/trpc|tRPC]] (egyszerűbb, type-safe, nincs code-gen)
> **Több kliens (web + mobile + 3rd party)?** - GraphQL (mindenki a saját query-jét írja)
> **Egyszerű CRUD, kevés endpoint?** - REST (KISS)
> **Komplex adat relációk, nested query-k?** - GraphQL

---

## Fő eszközök

| Eszköz | Típus | Mikor válaszd |
|--------|-------|---------------|
| **Apollo Server** | Szerver | Full-featured, nagy ökoszisztéma, caching |
| **Apollo Client** | Kliens | React integráció, cache management |
| **Yoga** | Szerver | Lightweight, Hono/Express-be integrálható |
| **Relay** | Kliens | Facebook-féle, strict conventions, pagination-optimized |
| **Pothos** | Schema builder | Code-first schema TypeScript-ben (Prisma integrációval) |
| **GraphQL Code Generator** | Tooling | Schema-ból TypeScript típusok generálása |

---

## A GraphQL problémái - mielőtt belevágsz

Nem minden esetben jobb a REST-nél. Ismerd a hátrányait:

| Probléma | Részletek |
|----------|-----------|
| **N+1 query probléma** | Nested resolver-ek - sok DB query. DataLoader kell a megoldáshoz |
| **Caching nehezebb** | Nincs URL-alapú HTTP cache (egyetlen endpoint van). Apollo Client cache segít |
| **Over-engineering** | Egyszerű CRUD-hoz túlzás - REST vagy tRPC egyszerűbb |
| **File upload** | Nem natívan támogatott - multipart spec vagy presigned URL kell |
| **Security** | Arbitrary query-k - rate limiting, query depth limiting, query complexity analysis kell |
| **Tanulási görbe** | Schema design, resolver-ek, type system - több upfront munka |

---

## Mikor használd / Mikor NE

**Mikor jó a GraphQL:**
- Több platform (web + iOS + Android) ugyanazt az API-t használja
- Komplex adat relációk (user - posts - comments - likes)
- Public API harmadik feleknek
- Micro-frontend architektúra (több csapat, különböző adat-igények)

**Mikor NE használd:**
- Fullstack TypeScript, egy csapat - [[backend/trpc|tRPC]] egyszerűbb
- Egyszerű CRUD, kevés endpoint - REST
- File-heavy app (sok upload/download) - REST + presigned URL
- Belső tool, nincs mobil kliens - tRPC vagy REST

---

## AI-natív fejlesztés

A GraphQL schema és resolver generálás az egyik legerősebb AI use case - a séma leírásából Claude Code vagy Cursor képes teljes resolver-eket, type definition-öket és DataLoader-eket generálni. A Pothos code-first megközelítés különösen jól működik AI-val.

> [!tip] Hogyan használd AI-val
> - *"Generálj GraphQL schemát Pothos-szal: User, Post, Comment típusok relációkkal, Prisma integrációval"*
> - *"GraphQL vs tRPC - jelezd melyiket akarod. Default-ból az AI REST-et vagy tRPC-t generál (egyszerűbb). Ha GraphQL-t akarsz, explicit kérd és add meg az eszközt is (Apollo, Yoga, Pothos)"*
