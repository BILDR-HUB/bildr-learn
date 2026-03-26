---
tags:
  - runtime
  - javascript
datum: 2026-03-26
szint: "🌱 Newcomer"
kapcsolodo:
  - "[[foundations/runtime|Runtime]]"
  - "[[foundations/bun|Bun]]"
  - "[[foundations/pnpm|pnpm]]"
  - "[[foundations/vite|Vite]]"
  - "[[toolbox/fnm|fnm]]"
  - "[[frontend/nextjs|Next.js]]"
---

# Node.js

> [!tldr] Mi ez és mire jó?
> JavaScript runtime a Chrome V8 motorjára építve. Lehetővé teszi, hogy JS-t futtass a szerveren, ne csak a böngésződön. A legtöbb modern webfejlesztési toolchain (bundler, linter, dev server) Node.js-en fut.

## Hogyan működik

Node.js lényege a **single-threaded event loop** non-blocking I/O-val. Nem csinál minden kérésre új szálat, hanem aszinkron callback-ekkel kezeli a párhuzamos műveleteket. Ezért kiválóan skálázódik I/O-intenzív feladatokra (API-k, real-time appok), de nem ideális CPU-intenzív számításokra.

## NPM ökoszisztéma

Az NPM a világ legnagyobb package registry-je (~2M+ csomag). A Node.js projektek alapja a `package.json`, ami a függőségeket és scripteket definiálja.

| Package manager | Jellemző |
|---|---|
| **[[foundations/npm-run-dev-vs-build|npm]]** | Beépített, mindig elérhető |
| **[[foundations/pnpm|pnpm]]** | Gyorsabb, hatékonyabb disk-használat - ez az ajánlott |
| **[[foundations/yarn|Yarn]]** | Alternatíva, ma már kevésbé releváns |

## Runtime összehasonlítás

| | Node.js | [[foundations/bun|Bun]] | Deno |
|---|---|---|---|
| **Érettség** | Legérettebb (2009~) | Fiatal, gyorsan fejlődik | Közepes |
| **Sebesség** | Alap | 2-3x gyorsabb | Gyorsabb |
| **Ökoszisztéma** | A legnagyobb | Node-kompatibilis | Node-kompatibilis |
| **Beépített tooling** | Minimális | Bundler, test runner, stb. | Formatter, linter, stb. |

**Fő szabály:** Node.js a biztonságos default. Bun-t próbáld ki, ha sebesség kell és nem baj, ha néha compatibility issue van.

## Verziókezelés

Használj **[[toolbox/fnm|fnm]]**-et (Fast Node Manager) a Node.js verziók kezeléséhez. Projektenként `.node-version` fájllal automatikusan vált verziót.

## Mikor használd

- **Szinte minden JS/TS projekt** Node.js-en fut - backend, frontend build, CLI tool
- [[toolbox/claude-code-projekt-setup|Claude Code]]-dal épített projektek alapértelmezetten Node.js-re épülnek
- Ha Docker-rel deployolsz, a Docker image-ek is Node.js-t használnak

---

## AI-natív fejlesztés

A Claude Code maga is Node.js-en fut - ha érted a runtime-ot, jobban tudod debug-olni az AI tool-jaidat is. A Node.js az AI-asszisztált fejlesztés alapértelmezett runtime-ja: a legtöbb AI tool, SDK és code generator erre épít.

> [!tip] Hogyan használd AI-val
> - *"Node 20 LTS-t használunk pnpm-mel - írd bele a CLAUDE.md-be"*
> - *"Debug-old ezt a Node.js memory leak-et - magyarázd el az event loop kontextusban"*
> - *"Állíts be egy Node.js projektet TypeScript-tel, ESLint-tel és Vitest-tel"*

---

## Kapcsolódó

- [[foundations/runtime|Runtime]] - Node.js mint JavaScript runtime
- [[foundations/bun|Bun]] - alternatív JavaScript runtime (drop-in Node.js replacement)
- [[toolbox/fnm|fnm]] - Node.js verziókezelő
- [[foundations/pnpm|pnpm]] - performáns package manager Node.js projektekhez
- [[foundations/vite|Vite]] - modern build tool Node.js-re
- [[frontend/nextjs|Next.js]] - fullstack framework Node.js-re építve
