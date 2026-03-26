---
tags:
  - alapfogalom
  - eszkoz
datum: 2026-03-26
szint: "🌱 Newcomer"
kapcsolodo:
  - "[[foundations/csomagkezelok-es-cli-toolok|Csomagkezelők és CLI toolok]]"
  - "[[foundations/npm-run-dev-vs-build|npm run dev vs build]]"
---

# Scaffold

## Mi az a scaffold?

Az építőiparból jön a metafora: **scaffold = állványzat**. Ahogy egy épületnél felállítják az állványzatot, mielőtt elkezdenék az érdemi munkát, a szoftverfejlesztésben is **egy projekt vagy feature kiindulási vázát generálod le automatikusan**.

A scaffold tehát: mappastruktúra, config fájlok, boilerplate kód - amire aztán ráépíted a tényleges logikát.

---

## Miért jó?

- Nem kézzel hozol létre 15-20 fájlt
- Az adott framework **best practice** struktúráját kapod
- Egységes projektstruktúrát biztosít a csapaton belül
- A "hogyan kezdjek hozzá?" kérdést megoldja

---

## Példák framework-önként

| Framework | Scaffold parancs | Mit generál |
|---|---|---|
| **[[frontend/nextjs|Next.js]]** | `npx create-next-app` | `app/`, `package.json`, `tsconfig.json`, layout, stb. |
| **Vite** | `npm create vite@latest` | Projekt váz a választott framework-hoz |
| **Rails** | `rails generate scaffold Post title body` | Model, controller, views, migration, routes - teljes CRUD |
| **Angular** | `ng generate component Header` | `.ts`, `.html`, `.css`, `.spec.ts` fájlok |
| **Django** | `django-admin startproject mysite` | `settings.py`, `urls.py`, `wsgi.py` struktúra |
| **Cargo (Rust)** | `cargo new my-project` | `src/main.rs`, `Cargo.toml` |

> [!tip] Tipp
> Az npx pont erre való - scaffold parancsokat futtatsz vele telepítés nélkül: `npx create-next-app`, `npx create-vite`, stb.

---

## Frontend vs Backend scaffold

### Frontend

Amit tipikusan generál:
- Komponens fájlok (`.tsx`, `.css`, `.test.ts`)
- Routing struktúra
- Bundler config (`vite.config.ts`, `next.config.ts`)
- CSS/Tailwind setup
- Linting és formatting config (`.eslintrc`, `.prettierrc`)

### Backend

Amit tipikusan generál:
- Route-ok és controller-ek
- Model-ek és DB migration-ök
- Middleware boilerplate
- API endpoint struktúra
- Teszt fájl vázak

### Full-stack scaffold

- Mindkettő egyben + monorepo struktúra
- [[cloud/docker-alapok|Docker]] config
- CI/CD pipeline vázak
- Environment változó sablonok (`.env.example`)

---

## Scaffold szintek

Nem csak projektinduláskor létezik - három szinten is megjelenik:

```text
Projekt szint     -> npx create-next-app (teljes projekt váz)
Feature szint     -> rails generate scaffold Post (egy CRUD feature)
Komponens szint   -> ng generate component Header (egyetlen fájl/fájlcsoport)
```

A **Rails** ebben a legerősebb - a `rails generate scaffold` egy parancsból teljes CRUD-ot ad: model, controller, views, migration, routes, tesztek.

---

## Mikor használd?

- **Új projekt indítása** - mindig a framework hivatalos scaffold-jával kezdj
- **Új feature hozzáadása** - ha a framework támogatja (Rails, Angular, Nest.js)
- **Gyors prototípus** - scaffold - minimális módosítás - működő demo

## Mikor NE?

- **Meglévő projektbe új framework** - ne scaffold-olj bele, hanem manuálisan integráld
- **Ha nem érted mi generálódott** - először értsd meg a struktúrát, aztán használd
- **Mikroprojekt** - 1-2 fájlhoz nem kell scaffold, overkill

---

## [[toolbox/claude-code-projekt-setup|Claude Code]] és scaffold

Claude Code-ban is gyakran scaffold-olsz: kéred hogy hozzon létre egy projekt vázat, vagy egy feature struktúráját. A különbség:

- **Hagyományos scaffold:** fix sablon, mindig ugyanazt generálja
- **Claude Code scaffold:** kontextus-alapú, a meglévő kódbázishoz igazodik

```bash
# Hagyományos scaffold
npx create-next-app my-app

# Claude Code "scaffold" - rugalmasabb
# "Hozz létre egy Next.js API route-ot auth middleware-rel"
# -> a meglévő projektstruktúrát figyelembe véve generál
```

---

## AI-natív fejlesztés

Az AI-asszisztált fejlesztésben a scaffold fogalma kitágul: a Claude Code nem fix sablonból generál, hanem a projekt kontextusából. Ezért érdemes ismerni a hagyományos scaffold-okat is - ha tudod milyen struktúrát vársz, pontosabb promptot írsz.

> [!tip] Hogyan használd AI-val
> - *"Scaffold-olj egy Next.js projektet App Router-rel, Tailwind-del és Drizzle ORM-mel"*
> - *"Generálj egy CRUD feature struktúrát a meglévő pattern alapján: route, schema, component"*
> - *"Mi a különbség a create-next-app és a kézi setup között? Melyiket válasszam?"*

---

## Kapcsolódó

- [[foundations/csomagkezelok-es-cli-toolok|Csomagkezelők és CLI toolok]] - npx és a scaffold parancsok futtatása
- [[foundations/npm-run-dev-vs-build|npm run dev vs build]] - mi történik a scaffold után
