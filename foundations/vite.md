---
tags:
  - build-tool
  - eszkoz
datum: 2026-03-26
szint: "🌱 Newcomer"
kapcsolodo:
  - "[[frontend/react-vs-spa-vs-preact|React]]"
  - "[[foundations/nodejs|Node.js]]"
  - "[[foundations/npm-run-dev-vs-build|npm run dev vs build]]"
  - "[[backend/hono|Hono]]"
---

# Vite

**Kategória:** `build tool` (frontend)
**URL:** https://vite.dev
**Ár/Terv:** Ingyenes, open source (Evan You - Vue.js creator)

---

## Mi ez és mire jó?

> [!tldr] Egy mondatban
> Next-gen frontend build tool - a dev szerver ES modulokat használ natívan (nincs bundling - instant indulás), a production build Rollup-pal történik.

A **Vite** (franciául: "gyors") az a tool, ami a [[frontend/react-vs-spa-vs-preact|React]]/Vue/Svelte projekted mögött a fájlokat transzformálja, a dev szervert futtatja, és a production build-et készíti. Ugyanaz a szerep, mint amit a **Webpack** tölt be - csak 10-100x gyorsabb.

---

## Vite vs Webpack vs Turbopack

| Szempont | Webpack | **Vite** | Turbopack |
|----------|---------|---------|-----------|
| **Dev szerver indulás** | 10-30s (bundle-öl) | < 1s (natív ESM) | < 1s (Rust) |
| **HMR** | Lassú (újra-bundle) | Instant (modul-szintű) | Instant |
| **Production build** | Webpack bundler | Rollup | N/A ([[frontend/nextjs|Next.js]] only) |
| **Konfiguráció** | Komplex (`webpack.config.js`) | Egyszerű (`vite.config.ts`) | Nincs (Next.js beépített) |
| **Ökoszisztéma** | Hatalmas, legacy | Gyorsan növekvő | Next.js-specifikus |
| **Framework support** | Minden | React, Vue, Svelte, Solid | Csak Next.js |

> [!tip] Döntési fa
> - **Next.js projekt -** Turbopack (beépített), nem kell Vite
> - **React SPA / Vue / Svelte -** Vite (de facto standard)
> - **Legacy projekt -** Webpack (ne migráld ha működik)

---

## Miért gyors?

A trükk a **dev szervereknél** van:

- **Webpack**: minden fájlt összecsomagol egy bundle-be - AZTÁN szolgálja ki - lassú indulás
- **Vite**: a böngésző natív ES modulokat (`import/export`) kér - Vite csak az adott fájlt transzformálja - azonnali

Production build-nél a Vite **Rollup**-ot használ (tree-shaking, code splitting, minification) - ez lassabb mint a dev szerver, de egyszer kell futtatni.

---

## Mire használod a gyakorlatban?

| Parancs | Mit csinál | Lásd még |
|---------|-----------|----------|
| `npm create vite@latest` | Új projekt (React/Vue/Svelte) | [[foundations/scaffold|Scaffold]] |
| `npm run dev` | Dev szerver (instant HMR) | [[foundations/npm-run-dev-vs-build|npm run dev vs build]] |
| `npm run build` | Production build (Rollup) | [[foundations/npm-run-dev-vs-build|npm run dev vs build]] |
| `npm run preview` | Build eredmény lokális preview | - |

A `vite.config.ts` az egyetlen konfig fájl - ide kerül alias, plugin, proxy beállítás.

---

## AI-natív fejlesztés

A Vite gyors HMR-je különösen hasznos AI-asszisztált fejlesztésnél: amikor a Claude Code módosít egy fájlt, azonnal látod az eredményt a böngészőben. A Vite + React kombináció a legtöbb AI-generált frontend projekt alapja.

> [!tip] Hogyan használd AI-val
> - *"Hozz létre egy React + TypeScript projektet Vite-tel"*
> - *"A vite.config.ts-ben adj hozzá path alias-t: `@/` - `src/`"*
> - *"Vite proxy-t akarok a dev szerveren: /api - localhost:8787 (Hono backend)"*

---

## Kapcsolódó

- [[frontend/react-vs-spa-vs-preact|React]] - a legtöbb új React projekt Vite-tel indul (Create React App halott)
- [[foundations/npm-run-dev-vs-build|npm run dev vs build]] - mit csinál a `dev` vs `build` parancs
- [[foundations/nodejs|Node.js]] - Vite Node.js-en fut
- [[backend/hono|Hono]] - Vite dev server + Hono backend a tipikus SPA architektúra
