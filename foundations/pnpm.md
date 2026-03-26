---
tags:
  - package-manager
  - eszkoz
datum: 2026-03-26
szint: "🌱 Newcomer"
kapcsolodo:
  - "[[foundations/yarn|Yarn]]"
  - "[[foundations/nodejs|Node.js]]"
  - "[[foundations/bun|Bun]]"
  - "[[toolbox/fnm|fnm]]"
---

# pnpm

**Kategória:** `package manager`
**URL:** https://pnpm.io
**Ár/Terv:** Ingyenes, open source

---

## Mi ez és mire jó?

> [!tldr] Egy mondatban
> Performant npm - gyors, lemezterület-hatékony csomagkezelő, ami globális content-addressable store-ból szimlinkel (minden csomag egyszer van tárolva a gépen).

A **pnpm** ugyanazt csinálja mint az [[foundations/npm-run-dev-vs-build|npm]] vagy [[foundations/yarn|Yarn]]: dependency-ket telepít, lockfile-t kezel, scripteket futtat. A különbség a **hogyan**: ahelyett hogy minden projektbe másolná a csomagokat, egy központi store-ból **hard link**-kel hivatkozik rájuk.

---

## pnpm vs npm vs Yarn vs Bun

| Szempont | npm | Yarn | **pnpm** | [[foundations/bun|Bun]] |
|----------|-----|------|------|-----|
| **Sebesség** | Lassú | Gyors | Gyors | Leggyorsabb |
| **Lemezhasználat** | Magas (duplikáció) | Magas / PnP | Alacsony (symlink) | Alacsony |
| **Phantom deps** | Igen (lazy) | Igen | Nincs (strict) | Igen |
| **Monorepo** | Workspaces (alap) | Workspaces | Workspaces (legjobb) | Workspaces |
| **Lockfile** | `package-lock.json` | `yarn.lock` | `pnpm-lock.yaml` | `bun.lockb` |
| **Default Node.js-szel** | Igen | Nem | Nem | Nem |

---

## Miért jó a "strict" mód?

A **phantom dependency** probléma: npm/Yarn-nál ha az `A` csomag függ `B`-től, te is importálhatod `B`-t anélkül hogy a `package.json`-odba írnád. Ez **véletlenül működik** - aztán egy frissítésnél eltörik.

A pnpm **nem engedi**: csak azt importálhatod, ami a te `package.json`-odban van. Ez extra biztonságot ad, különösen monorepo-kban.

---

## Monorepo támogatás

A pnpm + Turborepo kombináció a legelterjedtebb monorepo stack:

| Funkció | Hogyan |
|---------|--------|
| Workspace-ek | `pnpm-workspace.yaml` fájl a gyökérben |
| Belső package hivatkozás | `"@myapp/shared": "workspace:*"` |
| Telepítés | `pnpm install` (gyökérből, mindent telepít) |
| Script futtatás | `pnpm --filter @myapp/web dev` |
| Minden package-ben | `pnpm -r run build` (rekurzív) |

```yaml
# pnpm-workspace.yaml
packages:
  - "apps/*"
  - "packages/*"
```

---

## Gyors setup

```bash
# Telepítés (Node.js-szel: corepack)
corepack enable
corepack prepare pnpm@latest --activate

# Vagy standalone
npm install -g pnpm

# Használat (npm-mel azonos parancsok)
pnpm install              # dependency-k telepítése
pnpm add react            # csomag hozzáadása
pnpm dev                  # `pnpm run dev` rövidítése
pnpm dlx create-vite      # npx megfelelője
```

---

## AI-natív fejlesztés

A pnpm a legtöbb AI-asszisztált projektben ajánlott package manager, mert a strict mód megakadályozza a phantom dependency-ket - ami különösen fontos, amikor AI generálja a kódot és nem biztos hogy minden importot explicit hozzáad a `package.json`-hoz.

> [!tip] Hogyan használd AI-val
> - *"pnpm-et használunk - írd bele a CLAUDE.md-be hogy `pnpm install`, ne npm-et"*
> - *"Monorepo setup: pnpm workspaces + Turborepo"*
> - *"A CI-ben `pnpm install --frozen-lockfile`-t használj"*

---

## Kapcsolódó

- [[foundations/yarn|Yarn]] - a másik népszerű alternatív package manager
- [[foundations/bun|Bun]] - Bun mint package manager (gyorsabb, de kevésbé strict)
- [[foundations/nodejs|Node.js]] - pnpm Node.js csomagkezelője
- [[foundations/csomagkezelok-es-cli-toolok|Csomagkezelők és CLI toolok]] - package managerek áttekintése
- [[toolbox/fnm|fnm]] - Node.js verziókezelő, pnpm mellé ajánlott
