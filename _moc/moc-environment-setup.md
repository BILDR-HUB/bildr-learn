# MOC - Environment Setup

> [!tldr]
> Ez a Map of Content összegyűjti az összes fejlesztői környezet beállításához kapcsolódó jegyzetet. Izolációs elvektől a runtime-okon át a build tool-okig.

---

## Izoláció elvek

| Note | Leírás |
|------|--------|
| Projekt-szintű izoláció | Modern fejlesztés alapelve: minden projekt saját Node verzió, saját csomagok, saját env változók — semmi globális |
| Csomagkezelők és CLI toolok | Nyelv → runtime → csomagkezelő rétegződés: brew, npm, pip, cargo, és hogyan kapcsolódnak egymáshoz |

## Runtime-ok

| Note | Leírás |
|------|--------|
| [[foundations/nodejs\|Node.js]] | A JavaScript szerver-oldali futtatókörnyezete — V8 engine, npm ökoszisztéma, async I/O |
| [[foundations/bun\|Bun]] | All-in-one JavaScript runtime — bundler, package manager, test runner, Node.js-nél gyorsabb |
| [[toolbox/fnm\|fnm]] | Fast Node Manager — Rust-alapú Node.js verziókezelő, `.nvmrc` alapján automatikus verzióváltás projektenként |

## Csomagkezelők

| Note | Leírás |
|------|--------|
| [[foundations/pnpm\|pnpm]] | Gyors, lemezterület-hatékony Node.js package manager — hardlinkek, strict mode, monorepo-kra kiváló |
| [[foundations/yarn\|Yarn]] | Facebook-féle Node.js package manager — Plug'n'Play, workspaces, offline cache |

## Build és fejlesztői eszközök

| Note | Leírás |
|------|--------|
| [[foundations/vite\|Vite]] | Villámgyors frontend build tool — ESM dev server, HMR, Rollup-alapú production build |
| [[toolbox/eslint-es-biome\|ESLint és Biome]] | Linting és formatting — kódminőség biztosítás, ESLint (JS ökoszisztéma) vs Biome (Rust, all-in-one) |
| [[toolbox/turborepo\|Turborepo]] | Monorepo build rendszer — intelligens caching, párhuzamos task futtatás, pnpm workspaces-zel |
| [[toolbox/orbstack\|OrbStack]] | Gyors Docker és Linux VM macOS-en — Docker Desktop alternatíva, K8s support, alacsony erőforrás |

## Python

| Note | Leírás |
|------|--------|
| [[foundations/python-venv\|Python venv]] | Python beépített izolációs eszköze: projekt-szintű virtuális környezet, saját pip és csomagok |
| [[toolbox/pyenv\|pyenv]] | Python verziókezelő — az fnm Python-os megfelelője, `.python-version` alapján automatikus verzióváltás |

## Monorepo és környezet eszközök

| Note | Leírás |
|------|--------|
| [[toolbox/monorepo-management\|Monorepo management]] | Turborepo, Nx, pnpm workspaces — több projekt egyetlen repóban |
| [[toolbox/nix\|Nix]] | Deklaratív, reprodukálható fejlesztői környezetek — a legteljesebb izolációs megoldás |
| [[toolbox/direnv\|direnv]] | Automatikus env változó betöltés mappánként — `cd`-nél aktiválódik, kifelé menet törlődik |

## AI tool konfiguráció

| Note | Leírás |
|------|--------|
| [[toolbox/claude-code-projekt-setup\|Claude Code projekt setup]] | CLAUDE.md, hooks, MCP szerverek, skills konfigurálás új projekthez |
| [[toolbox/ai-first-fejlesztoi-workflow\|AI-first fejlesztői workflow]] | Hogyan strukturáld a munkádat Claude Code-dal |
| [[toolbox/cursor-es-claude-konfiguracio\|Cursor és Claude konfiguráció]] | AI coding tool-ok projektszintű beállításai (.cursor, .claude), best practice-ek |

---

## Kapcsolódó note-ok

| Note | Kapcsolat |
|------|-----------|
| Env változók Next.js-ben | Env változók izolálása `.env.local` fájllal projektenként |
| [[cloud/docker-alapok\|Docker alapok]] | A legteljesebb izolációs megoldás: az egész futtatókörnyezet konténerben |
| [[toolbox/uj-mac-setup\|Uj Mac setup]] | Új gép beállítása: brew, fnm, és egyéb dev tool-ok telepítése |
| [[foundations/bash-es-linux-parancssor\|Bash es Linux parancssor]] | A terminál, ahol mindez történik |

---

## Tanulási útvonal

Ajánlott sorrend ha nulláról kezded:
1. Projekt-szintű izoláció — értsd meg miért fontos: egy projekt = egy izolált környezet, semmi globális
2. Csomagkezelők és CLI toolok — tanuld meg a rétegződést: brew → runtime → csomagkezelő
3. [[foundations/nodejs|Node.js]] — a JavaScript runtime, amire a legtöbb projekt épül
4. [[toolbox/fnm|fnm]] — Node.js verziókezelés megoldva: `.nvmrc` fájl a projektben, automatikus váltás
5. [[foundations/pnpm|pnpm]] — a modern csomagkezelő monorepo-khoz és gyors install-hoz
6. [[foundations/vite|Vite]] — build tool: ESM dev server, HMR, gyors production build
7. [[toolbox/eslint-es-biome|ESLint és Biome]] — kódminőség automatizálás, linting és formatting
8. [[foundations/python-venv|Python venv]] — ugyanez Python-ban: `venv` mappa a projektben, izolált pip
9. [[toolbox/pyenv|pyenv]] — Python verziókezelés: `.python-version` fájl a projektben
10. [[toolbox/direnv|direnv]] — env változók automatikus kezelése mappánként
11. [[toolbox/turborepo|Turborepo]] — monorepo build és caching rendszer
12. [[toolbox/nix|Nix]] — ha a teljes környezetet deklaratívan akarod kezelni (haladó)

---

## Hézagok

- [x] Monorepo management → [[toolbox/monorepo-management|Monorepo management]] — Turborepo, Nx, pnpm workspaces használata
- [x] Dev containers → [[toolbox/dev-containers|Dev Containers]] — VS Code / Docker-alapú fejlesztői konténerek
- [x] Nix → [[toolbox/nix|Nix]] — deklaratív, reprodukálható fejlesztői környezetek
- [x] direnv → [[toolbox/direnv|direnv]] — automatikus env változó betöltés mappánként
- [x] pyenv → [[toolbox/pyenv|pyenv]] — Python verziókezelő (az fnm Python-os megfelelője)
- [x] Claude Code projekt setup → [[toolbox/claude-code-projekt-setup|Claude Code projekt setup]] — CLAUDE.md, hooks, MCP szerverek, skills konfigurálás új projekthez
- [x] AI-first fejlesztői workflow → [[toolbox/ai-first-fejlesztoi-workflow|AI-first fejlesztői workflow]] — hogyan strukturáld a munkádat Claude Code-dal (worktree-k, agent teamek, kontextus kezelés)
- [x] .cursor / .claude konfiguráció → [[toolbox/cursor-es-claude-konfiguracio|Cursor és Claude konfiguráció]] — AI coding tool-ok projektszintű beállításai, best practice-ek
- [x] Node.js → [[foundations/nodejs|Node.js]] — JavaScript runtime alapok
- [x] Bun → [[foundations/bun|Bun]] — all-in-one JS runtime
- [x] pnpm → [[foundations/pnpm|pnpm]] — modern csomagkezelő
- [x] Yarn → [[foundations/yarn|Yarn]] — Facebook csomagkezelő
- [x] Vite → [[foundations/vite|Vite]] — build tool
- [x] ESLint és Biome → [[toolbox/eslint-es-biome|ESLint és Biome]] — linting és formatting
- [x] OrbStack → [[toolbox/orbstack|OrbStack]] — Docker Desktop alternatíva macOS-en
- [x] Turborepo → [[toolbox/turborepo|Turborepo]] — monorepo build rendszer
