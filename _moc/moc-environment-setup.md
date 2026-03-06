# MOC - Environment Setup

> [!tldr]
> Ez a Map of Content osszegyujti az osszes fejlesztoi kornyezet beallitasahoz kapcsolodo note-t. Izolacios elvektol a konkret verziokozelo eszkozokig.

---

## Izoláció elvek

| Note | Leírás |
|------|--------|
| Projekt-szintű izoláció | Modern fejlesztés alapelve: minden projekt saját Node verzió, saját csomagok, saját env változók — semmi globális |
| Csomagkezelők és CLI toolok | Nyelv → runtime → csomagkezelő rétegződés: brew, npm, pip, cargo, és hogyan kapcsolódnak egymáshoz |

## Node.js

| Note | Leírás |
|------|--------|
| [[toolbox/fnm|fnm]] | Fast Node Manager — Rust-alapú Node.js verziókezelő, `.nvmrc` alapján automatikus verzióváltás projektenként |

## Python

| Note | Leírás |
|------|--------|
| [[foundations/python-venv|Python venv]] | Python beépített izolációs eszköze: projekt-szintű virtuális környezet, saját pip és csomagok |

---

## Kapcsolódó note-ok

| Note | Kapcsolat |
|------|-----------|
| Env változók Next.js-ben | Env változók izolálása `.env.local` fájllal projektenként |
| [[cloud/docker-alapok|Docker alapok]] | A legteljesebb izolációs megoldás: az egész futtatókörnyezet konténerben |
| [[toolbox/uj-mac-setup|Uj Mac setup]] | Új gép beállítása: brew, fnm, és egyéb dev tool-ok telepítése |
| [[foundations/bash-es-linux-parancssor|Bash es Linux parancssor]] | A terminál, ahol mindez történik |

---

## Tanulási útvonal

Ajánlott sorrend ha nulláról kezded:
1. Projekt-szintű izoláció — értsd meg miért fontos: egy projekt = egy izolált környezet, semmi globális
2. Csomagkezelők és CLI toolok — tanuld meg a rétegződést: brew → runtime → csomagkezelő
3. [[toolbox/fnm|fnm]] — Node.js verziókezelés megoldva: `.nvmrc` fájl a projektben, automatikus váltás
4. [[foundations/python-venv|Python venv]] — ugyanez Python-ban: `venv` mappa a projektben, izolált pip

---

## Hézagok

- [ ] Monorepo management — Turborepo, Nx, pnpm workspaces használata
- [ ] Dev containers — VS Code / Docker-alapú fejlesztői környezetek
- [ ] Nix — deklaratív, reprodukálható fejlesztői környezetek
- [ ] direnv — automatikus env változó betöltés mappánként
- [ ] pyenv — Python verziókezelő (az fnm Python-os megfelelője)
