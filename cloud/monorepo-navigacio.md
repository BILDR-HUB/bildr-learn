---
tags:
  - monorepo
  - architektura
  - workflow
datum: 2026-03-26
szint: "🌱 Newcomer"
kapcsolodo:
  - "[[cloud/multi-app-monorepo-architektura|Multi-app Monorepo architektúra]]"
  - "[[cloud/monorepo-package-struktura|Monorepo package struktúra]]"
  - "[[cloud/ci-cd-pipelines|CI/CD]]"
---

# Monorepo navigáció

## Miért nehéz egy monorepoban navigálni?

Amikor megnyitsz egy multi-app [[toolbox/monorepo-management|Monorepo]]-t, **rengeteg mappa és fájl** néz vissza rád. Az első reakció: "hol kezdjem?"

A trükk: **nem az egészet kell értened egyszerre**. Egy monorepo logikus rétegekre van bontva, és ha tudod melyik réteg mit csinál, mindig tudni fogod hova nyúlj.

---

## A két fő mappa: apps/ és packages/

Ez a legfontosabb mentális modell:

| Mappa | Kérdés | Analógia |
|-------|--------|----------|
| `apps/` | **"Mit lát a felhasználó?"** | A kirakatüveg - minden app egy önálló "bolt" |
| `packages/` | **"Mi a közös alap?"** | A raktár - amit a boltok közösen használnak |

> [!tip] Fő szabály
> Ha vizuális dolgot keresel (oldal, route, UI layout) -> `apps/`
> Ha logikát, közös kódot keresel (auth, DB, shared komponens) -> `packages/`

---

## Az apps/ mappa - itt élnek az alkalmazások

Minden mappa az `apps/`-ban egy **önálló, deployolható alkalmazás**. Úgy gondolj rájuk, mint külön weboldalakra, amik véletlenül egy repóban élnek.

### Mit találsz egy app mappában?

Egy tipikus [[frontend/nextjs|Next.js]] app felépítése az `apps/` alatt:

| Hely | Mit tartalmaz |
|------|--------------|
| `apps/webapp/app/` | Az oldalak (route-ok) - amit a böngészőben látsz |
| `apps/webapp/app/layout.tsx` | Az app fő elrendezése (sidebar, header) |
| `apps/webapp/app/page.tsx` | A főoldal |
| `apps/webapp/app/clients/` | `/clients` route - az ügyfélkezelő oldalak |
| `apps/webapp/components/` | App-specifikus komponensek (csak ez az app használja) |
| `apps/webapp/lib/` | App-specifikus logika, helper-ek |
| `apps/webapp/package.json` | Az app függőségei |
| `apps/webapp/wrangler.jsonc` | Deploy konfig (Cloudflare-nél) |

### Mikor nyúlj az apps/-ba?

- **Új oldalt** akarsz hozzáadni -> `apps/[app-név]/app/`
- **Egy konkrét app UI-ját** módosítod -> `apps/[app-név]/components/`
- **Route logikát** (server action, API) keresel -> `apps/[app-név]/app/`
- **Deploy konfigot** módosítasz -> `apps/[app-név]/wrangler.jsonc` vagy `next.config.ts`

---

## A packages/ mappa - a közös kódtár

A `packages/` mappa **nem önálló alkalmazás** - ezeket nem nyitod meg böngészőben. Ehelyett az appok importálják őket, mint belső npm csomagokat.

### Tipikus packages és hol keress bennük

| Package | Mikor nyúlj hozzá | Tipikus tartalom |
|---------|-------------------|-----------------|
| `packages/ui` | **Közös UI komponenst** keresel vagy módosítasz | Button, Card, Table, Modal, Sidebar, Header |
| `packages/auth` | **Bejelentkezés, jogosultság** probléma van | Login flow, session kezelés, middleware |
| `packages/db` | **Adatbázis query** kell vagy hiba van | Tábla definíciók, query függvények, migráció |
| `packages/types` | **TypeScript típust** keresel vagy módosítasz | Interface-ek, enum-ok, közös típusok |
| `packages/config` | **ESLint/TS konfig** módosítás kell | tsconfig.json, eslint konfig, Tailwind preset |

### Honnan tudom, hogy packages/ vagy apps/ kell?

```
"Az appban rossz helyen van a gomb"
  → apps/webapp (UI elrendezés, oldal-specifikus)

"Minden appban rossz a gomb stílusa"
  → packages/ui (közös komponens)

"A login nem működik a portálon"
  → packages/auth (közös auth logika)
  → VAGY apps/portal (ha portál-specifikus a hiba)

"Új mezőt kell adni az ügyfél táblához"
  → packages/db (adatbázis séma)
  → ÉS apps/webapp (az új mező megjelenítése)
```

---

## A gyökér mappa - repo-szintű konfig

A repo gyökerében (se nem `apps/`, se nem `packages/`) a **repo-szintű konfigurációs fájlok** élnek:

| Fájl | Mi ez | Mikor érdekel |
|------|-------|---------------|
| `turbo.json` | Turborepo konfig - taskok, cache | Build/pipeline probléma |
| `package.json` | Workspace root - script-ek, workspaces definíció | Új package hozzáadása |
| `pnpm-workspace.yaml` | pnpm workspace definíció | Workspace problémák |
| `.github/workflows/` | [[cloud/ci-cd-pipelines|CI/CD]] pipeline-ok | Deploy problémák |
| `tsconfig.json` | Root TypeScript konfig | TS compile hibák |

---

## Navigációs cheat sheet

> [!tldr] Gyors kereső
> **"Hol van az oldal/route?"** -> `apps/[app]/app/`
> **"Hol van a közös komponens?"** -> `packages/ui/`
> **"Hol van az auth logika?"** -> `packages/auth/`
> **"Hol van az adatbázis?"** -> `packages/db/`
> **"Hol van a deploy konfig?"** -> `apps/[app]/wrangler.jsonc` vagy `.github/workflows/`
> **"Hol van a build konfig?"** -> gyökér `turbo.json`
> **"Miért nem fordul?"** -> `packages/config/` (TS/ESLint konfig)

---

## Tippek editorokhoz

- **Nyisd meg a repo gyökerét**, ne egy almappát - így az editor a teljes monorepo-t látja
- **Keress fájlnévre** (Cmd+P / Ctrl+P) - hatékonyabb, mint a fa böngészése
- Ha nem tudod hova kerüljön valami, kérdezd meg az AI-t a teljes kontextussal: *"Ez egy pnpm monorepo apps/ + packages/ struktúrával. Hova tegyem az email küldő logikát?"*
- **Package import** kereséssel is megtalálod a forrást - ha `@projekt/ui`-t importál valami, az a `packages/ui` mappában van

---

## AI-natív fejlesztés

A monorepo navigáció az egyik terület ahol az AI a legnagyobb értéket adja - Claude Code látja az egész repót és tudja, melyik package-ben melyik fájl van. De ehhez neked kell a kontextust megadnod: melyik app, melyik package a célpont.

> [!tip] Hogyan használd AI-val
> - *"A packages/ui/src/components/ mappában keress rá a Table komponensre"*
> - *"Az apps/webapp/app/clients/ route-ban van hiba, nézd meg a page.tsx-et"*
> - *"Monorepoban mindig add meg a mappát. Anélkül az AI nem tudja, hogy az apps/webapp komponensét, a packages/ui közös komponensét, vagy a packages/db adatbázis tábláját akarod-e módosítani."*

---

## Kapcsolódó

- [[cloud/multi-app-monorepo-architektura|Multi-app Monorepo architektúra]] - Az apps/ + packages/ minta teljes áttekintése
- [[cloud/monorepo-package-struktura|Monorepo package struktúra]] - A package-ek belső felépítése (core/shared/functions/frontend)
