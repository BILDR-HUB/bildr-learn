---
tags:
  - package-manager
  - eszkoz
datum: 2026-03-26
szint: "🌱 Newcomer"
kapcsolodo:
  - "[[foundations/csomagkezelok-es-cli-toolok|Csomagkezelők és CLI toolok]]"
  - "[[foundations/runtime|Runtime]]"
  - "[[foundations/pnpm|pnpm]]"
  - "[[foundations/bun|Bun]]"
  - "[[toolbox/fnm|fnm]]"
---

# Yarn

**Kategória:** `dev tool` / `csomagkezelő`
**URL:** https://yarnpkg.com
**Ár/Terv:** Ingyenes, open source

---

## Mi ez és mire jó?

A **Yarn** egy **[[foundations/nodejs|Node.js]] csomagkezelő** - ugyanazt csinálja mint az npm (csomagokat telepít a `package.json`-ből), de a Meta (Facebook) fejlesztette gyorsabb és megbízhatóbb alternatívaként.

> [!tldr] Egy mondatban
> A Yarn az npm alternatívája - ugyanazt a `package.json`-t olvassa, ugyanazokat a csomagokat telepíti az npm registry-ből, de más telepítési stratégiával és lock fájl formátummal.

### Rövid történet

1. **2016 - Yarn Classic (v1)** - a Meta megcsinálja, mert az npm akkoriban lassú és nem determinisztikus volt. Yarn hozza a lock fájlt (`yarn.lock`), párhuzamos letöltést, offline cache-t
2. **2020 - Yarn Berry (v2-4+)** - teljes újraírás, radikálisan más megközelítés (Plug'n'Play, zero-installs). Sok projekt visszatért npm-re mert túl sok breaking change volt
3. **2024+ - npm felzárkózott** - az npm v7+ átvette a legtöbb Yarn innovációt (lock fájl, workspaces, gyorsabb telepítés). A Yarn előnye ma már nem akkora

---

## Yarn a Node.js csomagkezelő térképen

| Szempont | npm | **Yarn Classic** | **Yarn Berry** | [[foundations/pnpm|pnpm]] | [[foundations/bun|Bun]] |
|----------|-----|-------------------|----------------|------|-----|
| **Lock fájl** | `package-lock.json` | `yarn.lock` | `yarn.lock` | `pnpm-lock.yaml` | `bun.lockb` |
| **node_modules** | Flat | Flat | **Nincs** (PnP) | Symlink-ek | Flat |
| **Sebesség** | Alap | Gyorsabb | Gyorsabb | Leggyorsabb (Node) | 10-25x |
| **Workspaces** | v7+ | Igen | Igen | Igen | Igen |
| **Zero-install** | Nem | Nem | Igen (.yarn/cache) | Nem | Nem |
| **Érettség** | 15+ év | Stabil, de legacy | Aktív fejlesztés | Aktív | Fiatal |

---

## Yarn Classic vs Yarn Berry

| Szempont | Yarn Classic (v1) | Yarn Berry (v2-4+) |
|----------|-------------------|---------------------|
| **node_modules** | Hagyományos, flat | **PnP (Plug'n'Play)** - nincs node_modules |
| **Telepítés** | `npm install -g yarn` | `corepack enable` + `yarn set version berry` |
| **Kompatibilitás** | Szinte 100% npm-mel | Sok csomag nem kompatibilis PnP-vel |
| **Repo méret** | Normál | `.yarn/cache` commitolva = nagy repo |
| **Lock fájl** | `yarn.lock` (YAML-szerű) | `yarn.lock` (más formátum) |
| **Státusz** | Maintenance mode (nem fejlesztik) | Aktív fejlesztés |

> [!info] Mi az a Plug'n'Play (PnP)?
> A Yarn Berry radikális ötlete: nem hoz létre `node_modules` mappát. Ehelyett egy `.pnp.cjs` fájl tartalmazza az összes csomag elérési útját, és a csomagok a `.yarn/cache`-ben vannak zip-ként. **Előny:** gyorsabb telepítés, nincs node_modules duplikáció. **Hátrány:** sok eszköz (TypeScript, ESLint, Jest) extra konfigurációt igényel.

---

## Mikor találkozol Yarn-nal?

Új projektben valószínűleg nem fogsz Yarn-t választani (Bun vagy npm elég). De **meglévő projekteknél** gyakran előfordul:

| Szituáció | Teendő |
|-----------|--------|
| Klónolsz egy repót és van benne `yarn.lock` | `yarn install`-t futtass, ne `npm install`-t |
| Meglévő Yarn Classic projekt | Maradj Yarn-nál, ne migráld npm-re (felesleges) |
| Meglévő Yarn Berry projekt (PnP) | Tanulási görbe van - olvasd el a `.yarnrc.yml`-t |
| Új projekt, saját döntés | Bun vagy npm - nem Yarn |
| Monorepo workspaces | pnpm workspaces jobb választás |

---

## Alapvető parancsok

| npm | Yarn (Classic) | Mit csinál |
|-----|----------------|-----------|
| `npm install` | `yarn` vagy `yarn install` | Telepítés lock fájlból |
| `npm install react` | `yarn add react` | Csomag hozzáadása |
| `npm install -D vitest` | `yarn add -D vitest` | Dev dependency |
| `npm run dev` | `yarn dev` | Script futtatás (nem kell `run`) |
| `npm run build` | `yarn build` | Build script |
| `npx create-next-app` | `yarn dlx create-next-app` | Egyszeri csomag futtatás |

> [!tip] `yarn dev` vs `npm run dev`
> Yarn-nál elhagyhatod a `run`-t - `yarn dev` = `yarn run dev`. Npm-nél a `run` kötelező (kivétel `npm start` és `npm test`).

---

## Buktatók és hibák amiket elkerülj

- **Ne keverd a lock fájlokat** - ha a projektben `yarn.lock` van, mindig `yarn install`-t futtass. Ha `npm install`-t futtatsz mellé, `package-lock.json` is létrejön - konfliktus
- **Yarn Berry PnP kompatibilitás** - sok Node.js tool (VS Code TypeScript, ESLint, [[database/prisma|Prisma]]) extra konfigurációt igényel PnP-vel. Ha nem megy valami, keress rá a csomag + PnP kompatibilitásra
- **Yarn Classic - nem fejlesztik** - ha hosszú távú projekted van Yarn Classic-on, fontold meg a pnpm-re migrálást
- **`corepack`** - a Node.js 16+ tartalmazza a `corepack`-ot ami Yarn-t is kezeli. De alapból disabled - `corepack enable` kell

---

## Hasznos linkek

- **Docs (Berry):** https://yarnpkg.com
- **Docs (Classic):** https://classic.yarnpkg.com
- **GitHub:** https://github.com/yarnpkg/berry
- **Migration guide (npm - Yarn):** https://yarnpkg.com/migration/overview

---

## AI-natív fejlesztés

Ha Yarn-os projekttel dolgozol, jelezd az AI tool-nak melyik verziót használod (Classic vs Berry) - a kettő teljesen más. A Claude Code a `yarn.lock` jelenlétéből felismeri a Yarn-t, de a verzió megadása pontosabb válaszokat eredményez.

> [!tip] Hogyan használd AI-val
> - *"A projekt Yarn Classic-ot használ (yarn.lock van) - adj hozzá egy új dependency-t és frissítsd a lock fájlt"*
> - *"Yarn Berry PnP: a TypeScript nem találja a modulokat - hogyan konfiguráljam a .yarnrc.yml-t?"*
> - *"Migráljuk ezt a projektet Yarn Classic-ról pnpm-re - mik a lépések és mire kell figyelni?"*

---

## Kapcsolódó

- [[foundations/csomagkezelok-es-cli-toolok|Csomagkezelők és CLI toolok]] - Yarn helye a csomagkezelő ökoszisztémában (npm, pnpm, Bun mellett)
- [[foundations/runtime|Runtime]] - Node.js runtime ami a Yarn-t futtatja
- [[foundations/projekt-szintu-izolacio|Projekt-szintű izoláció]] - lock fájlok és izoláció elve
- [[toolbox/fnm|fnm]] - Node.js verziókezelő (Yarn is a Node.js verziótól függ)
- [[foundations/pnpm|pnpm]] - alternatív package manager (szigorúbb, gyorsabb)
- [[foundations/bun|Bun]] - beépített package manager alternatíva
