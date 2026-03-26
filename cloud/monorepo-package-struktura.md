---
tags:
  - monorepo
  - architektura
  - best-practice
datum: 2026-03-26
szint: "🏗️ Builder"
kapcsolodo:
  - "[[cloud/multi-app-monorepo-architektura|Multi-app Monorepo architektúra]]"
  - "[[cloud/monorepo-navigacio|Monorepo navigáció]]"
  - "[[database/drizzle|Drizzle]]"
  - "[[backend/hono|Hono]]"
---

# Monorepo package struktúra

## Mi az a monorepo package struktúra?

Egy **[[toolbox/monorepo-management|Monorepo]]** = egy git repo, több önálló package benne. Ahelyett, hogy az egész kód egy nagy kupacban lenne (`src/` alatt minden), szétválasztod logikai egységekre - és ezek egymást importálják, mintha külső npm csomagok lennének.

```
my-project/
├── packages/
│   ├── core/          ← backend-only logika
│   ├── shared/        ← mindkét oldal használja
│   ├── functions/     ← API route-ok / Workers
│   └── frontend/      ← SPA (React, stb.)
├── package.json       ← workspace root
└── turbo.json         ← build orchestrator (opcionális)
```

A lényeg: **nem egy mappa struktúra, hanem önálló npm package-ek**, amiket a workspace root köt össze.

---

## A négy package és mit csinálnak

### `packages/core` - Backend-only logika

Ez az agy. Ide kerül minden, ami **csak szerveren fut**:

- **DB séma** - [[database/drizzle|Drizzle]] tábla definíciók (`.sql.ts` fájlok)
- **Business logika** - query-k, validáció, számítások
- **DB kapcsolat** - a `drizzle()` kliens, connection config
- **Migrációk** - `drizzle-kit` által generált SQL fájlok

A domain-ek saját mappában élnek, mindegyikben:

| Fájl | Szerepe |
|---|---|
| `application.sql.ts` | Drizzle tábla definíció (`pgTable`) |
| `index.ts` | Business logika (query-k, validáció) + re-export a séma |

**Minta:** A `.sql.ts` fájl a domain mappa része - nem egy külön `schema/` mappa. A séma és a logika együtt él.

> [!tip] Terminal.shop minta
> A Terminal.shop (nyílt forráskódú SST/AWS monorepo) ezt a mintát használja: minden domain-nek saját mappa a `core/`-on belül, és a `.sql.ts` fájlok közvetlenül a domain mappa részei - nem egy külön `schema/` mappa. Ez átláthatóbb, mert a séma és a logika együtt él.

---

### `packages/shared` - Mindkét oldal importálja

Ez a **híd** a backend és a frontend között. Ide kerül minden, ami **runtime-független** - azaz böngészőben ÉS szerveren is működik:

- **Zod sémák** - validáció, amit a frontend (form) ÉS a backend (API) is használ
- **API típusok** - request/response típusok
- **Enum-ok** - pl. `ApplicationStatus`, `UserRole`
- **Bindings típus** - Cloudflare Worker environment típus (D1, KV, R2 binding-ok)

| Fájl | Mit tartalmaz | Ki importálja |
|---|---|---|
| `types.ts` | Zod sémák (pl. `applicationSchema`) + `z.infer` típusok | Frontend (form validáció) + Backend (API validáció) |
| `enums.ts` | `as const` objektumok (pl. `APPLICATION_STATUS`) + típusok | Mindkét oldal |

---

### `packages/functions` - API route-ok

Az API réteg, ami kívülről elérhető. [[cloud/cloudflare|Cloudflare]] Workers esetén ezek a Worker handler-ek (pl. [[backend/hono|Hono]] route-ok). [[frontend/nextjs|Next.js]] esetén ezek a Server Actions / Route Handlers.

**Minta:** A functions réteg a `core`-ból hívja a business logikát, a `shared`-ből a Zod sémát. Hono route-ban: `safeParse(body)` -> ha hiba: 400, ha OK: core function hívás.

---

### `packages/frontend` - SPA / kliens oldal

A böngészőben futó kód. Ez **csak a `shared`-ből importálhat**, soha nem a `core`-ból.

**Szabály:** A frontend **csak a `shared`-ből** importálhat. A `core`-ból importálni TILOS (DB kód nem futhat böngészőben).

---

## Miért kell szétválasztani?

A fő ok: **a frontend (böngésző) nem importálhat backend kódot**.

```
                    ┌─────────────┐
                    │   shared    │
                    │  (típusok,  │
                    │  Zod sémák) │
                    └──────┬──────┘
                           │
              importálja   │   importálja
            ┌──────────────┼──────────────┐
            ▼              │              ▼
     ┌─────────────┐       │       ┌─────────────┐
     │   frontend   │       │       │  functions   │
     │  (böngésző)  │       │       │  (API route) │
     └─────────────┘       │       └──────┬───────┘
                           │              │
                           │         importálja
                           │              ▼
                           │       ┌─────────────┐
                           │       │    core      │
                           │       │  (DB, logika)│
                           └───────┴─────────────┘
```

| Mi történik ha nem választod szét? | Probléma |
|---|---|
| Frontend importálja a `core`-t | A bundler behúzza a Drizzle-t, a postgres drivert - a böngészőben nem futnak |
| Egy nagy `src/` minden együtt | Nem tudod, mi szerver-only és mi nem - véletlen leak |
| Tree-shaking kellene | Elméletileg kiszűri a nem használt kódot, de nem megbízható - főleg Drizzle-nél |

> [!warning] A szeparáció nem opcionális
> Ha a frontend SPA (nem server-rendered), a DB kód fizikailag nem tud futni a böngészőben. A monorepo package határ ezt **build time-ban** kikényszeríti - nem kell tree-shaking-re bízni.

---

## Mikor kell monorepo és mikor nem?

### Kell monorepo (packages szétválasztás):
- **SPA frontend + külön API** - a frontend és a backend külön deploy-olódik
- **Több frontend** - pl. web app + admin panel + landing page, közös típusokkal
- **Cloudflare Workers / Hono** - a Worker és a frontend külön entry point

### Nem kell monorepo:
- **Next.js App Router (SSR)** - a Server Components/Actions szerveren futnak, a kliens kód automatikusan szeparált a `"use client"` / `"use server"` direktívákkal
- **Astro** - hasonlóan server-first, a build elintézi a szeparációt
- **Kis projekt** - ha egy ember fejleszti és nincs több deploy target

---

## Workspace setup

A monorepo workspace-eket a root `package.json`-ban definiálod (`"workspaces": ["packages/*"]`). Minden package-nek saját `package.json`-ja van:

| Package | `name` | Fő dependency-k |
|---|---|---|
| Root | `projekt` (`private: true`) | workspace definíció |
| Core | `@projekt/core` | `drizzle-orm` |
| Shared | `@projekt/shared` | `zod` |
| Frontend | `@projekt/frontend` | `@projekt/shared: "workspace:*"` |

A `workspace:*` azt jelenti: "ebből a monorepo-ból vedd, ne npm-ről töltsd le."

---

## Inspiráció: Terminal.shop

A [Terminal.shop](https://github.com/terminaldotshop/terminal) (nyílt forráskódú SST/AWS monorepo) struktúrája:

```
packages/
├── core/
│   ├── src/
│   │   ├── user/
│   │   │   ├── user.sql.ts     ← Drizzle séma
│   │   │   └── index.ts        ← business logika + export
│   │   ├── order/
│   │   │   ├── order.sql.ts
│   │   │   └── index.ts
│   │   └── drizzle/
│   │       └── index.ts        ← DB kliens
├── functions/
│   └── src/
│       └── api/                ← API route-ok, core-t importálják
└── www/
    └── src/                    ← Astro frontend
```

Náluk nincs külön `shared`, mert az Astro server-rendered - közvetlenül importálja a `core`-t. **SPA-nál viszont kell a shared réteg.**

---

## AI-natív fejlesztés

A monorepo package struktúra tervezése és refaktorálása komplex feladat, de a pattern jól definiált - Claude Code-dal gyorsan felépítheted a 4-package mintát, és a megfelelő import szabályokat az AI kikényszeríti (pl. frontend soha ne importáljon core-ból).

> [!tip] Hogyan használd AI-val
> - *"Készíts monorepo struktúrát 4 package-dzsel: core (Drizzle DB + business logika), shared (Zod sémák + típusok), functions (Hono API), frontend (React SPA)"*
> - *"A Zod sémákat tedd a shared package-be, hogy a frontend form validáció és a backend API validáció ugyanazt használja"*
> - *"Mikor hivatkozz erre: ha SPA + külön API architektúrát építesz (pl. Cloudflare Workers + React). Next.js App Router-nél NEM kell monorepo."*

## Kapcsolódó

- [[database/drizzle|Drizzle]] - az ORM ami a core-ban él
- [[cloud/multi-app-monorepo-architektura|Multi-app Monorepo architektúra]] - Amikor több app van egy repo-ban (apps/ + packages/ minta)
- [[cloud/monorepo-navigacio|Monorepo navigáció]] - Hogyan navigálj egy monorepoban, mi hol van
