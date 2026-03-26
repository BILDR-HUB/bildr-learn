---
tags:
  - runtime
  - package-manager
datum: 2026-03-26
szint: "🧱 Brick"
kapcsolodo:
  - "[[foundations/nodejs|Node.js]]"
  - "[[foundations/runtime|Runtime]]"
  - "[[foundations/yarn|Yarn]]"
  - "[[foundations/pnpm|pnpm]]"
  - "[[toolbox/fnm|fnm]]"
---

# Bun

**Kategória:** `runtime` (all-in-one: runtime + package manager + bundler + test runner)
**URL:** https://bun.sh
**Ár/Terv:** Ingyenes, open source (Oven)

---

## Mi ez és mire jó?

> [!tldr] Egy mondatban
> All-in-one JavaScript/TypeScript runtime - a [[foundations/nodejs|Node.js]] drop-in cseréje, de 3-10x gyorsabb telepítéssel, indulással és futtatással. Zig-ben írva.

A **Bun** nem csak runtime: egyben **package manager** (`bun install`), **bundler** (`bun build`), **test runner** (`bun test`) és **script runner** (`bun run`). Egy eszköz, ami kiváltja a Node.js + npm + Jest + esbuild kombinációt.

---

## Bun vs Node.js vs Deno

| Szempont | Node.js | Deno | **Bun** |
|----------|---------|------|---------|
| **Érettség** | 15+ év, stabil | 6+ év, stabil | Fiatal (v1: 2023) |
| **Sebesség** | Alap | Gyorsabb | Leggyorsabb |
| **TypeScript** | ts-node/tsx kell | Natív | Natív |
| **Package manager** | npm/yarn/pnpm | deno add | `bun install` (3-10x gyorsabb) |
| **npm kompatibilitás** | 100% | Jó (npm: prefix) | ~95%+ |
| **Ökoszisztéma** | Hatalmas | Növekvő | Node.js-t használja |
| **Produkciós érettség** | Bevált | Jó | Javuló, de fiatal |

---

## Mikor használd (és mikor NE)?

| Szituáció | Ajánlás |
|-----------|---------|
| Új projekt, ahol sebesség számít | Bun (runtime + PM) |
| CI/CD pipeline gyorsítás | `bun install` (akár Node.js projekt mellé is) |
| Gyors script futtatás | `bun run script.ts` - natív TS, zero config |
| Production szerver (kritikus) | Maradj Node.js-nél (érettebb) |
| Edge runtime ([[cloud/cloudflare|Cloudflare]]) | Nem releváns - Cloudflare saját runtime-ot használ |
| Meglévő Node.js projekt | Próbáld `bun install`-lal - ha minden jó, terjeszd ki |

---

## A gyakorlatban: mit cserél ki?

| Régi | Bun megfelelő | Gyorsulás |
|------|---------------|-----------|
| `npm install` | `bun install` | 3-10x |
| `npm run dev` | `bun run dev` | 1.5-2x |
| `npx ts-node script.ts` | `bun script.ts` | 5x+ (natív TS) |
| `jest` | `bun test` | 2-5x |
| `npm create vite` | `bun create vite` | Hasonló |

```bash
# Node.js projekt -> Bun-ra váltás (drop-in)
bun install          # node_modules létrehozás (bun.lockb)
bun run dev          # package.json scripts futtatása
bun test             # tesztek futtatása
bun script.ts        # TypeScript fájl közvetlen futtatása
```

---

## AI-natív fejlesztés

Ha Bun-t használsz, jelezd a Claude Code-nak a CLAUDE.md-ben - különben npm-et fog használni alapból. A `bun install` 3-10x gyorsabb, ami AI-asszisztált fejlesztésnél is érezhető: gyorsabb dependency telepítés = gyorsabb iteráció.

> [!tip] Hogyan használd AI-val
> - *"Használj Bun-t package managerként és runtime-ként - írd bele a CLAUDE.md-be"*
> - *"A CI-ben `bun install`-t használj npm helyett a gyorsabb build-ért"*
> - *"`bun test`-tel futtasd a teszteket Jest helyett"*

---

## Kapcsolódó

- [[foundations/nodejs|Node.js]] - az érett alternatíva, amit Bun kiegészít/kiválthat
- [[foundations/runtime|Runtime]] - mi a runtime és miért számít
- [[foundations/yarn|Yarn]] - Yarn vs Bun mint package manager (Bun gyorsabb)
- [[foundations/pnpm|pnpm]] - pnpm vs Bun mint package manager
- [[foundations/vite|Vite]] - ha Vite projekted van, Vitest > bun test (jobb integráció)
- [[toolbox/fnm|fnm]] - Node.js verzió kezelő (Bun-nak saját verzió kezelés van)
