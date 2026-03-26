---
tags:
  - ai
  - claude-code
  - modszertan
  - workflow
datum: 2026-03-26
szint: "🏗️ Builder"
kapcsolodo:
  - "[[github/bmad-method|BMAD-METHOD]]"
  - "[[toolbox/claude-code-projekt-setup|Claude Code]]"
  - "[[github/claude-code-best-practice|Claude Code best practice]]"
  - "[[github/everything-claude-code|Everything Claude Code]]"
---

# Get Shit Done (GSD)

**URL:** https://github.com/gsd-build/get-shit-done
**Stars:** ~39K

> [!tldr] Egy mondatban
> Lightweight spec-driven fejlesztési rendszer ami a **context rot problémát** oldja meg - minden végrehajtási lépés friss 200K context window-ban fut, így a minőség nem degradálódik ahogy a projekt nő.

---

## Mi ez és mire jó?

A GSD abból a felismerésből indul ki, hogy a [[toolbox/claude-code-projekt-setup|Claude Code]] output minősége **romlik** ahogy telik a context window (context rot). A megoldás: a tervezést és végrehajtást szétválasztani, és minden implementációs lépést **friss session-ben** futtatni, atom-méretű tervek alapján.

A workflow 5 lépés - semmi enterprise ceremony:

| Lépés | Parancs | Mit csinál |
|---|---|---|
| **1. Projekt init** | `/gsd:new-project` | Interjúzik -> kutat -> requirements -> roadmap |
| **2. Discuss** | `/gsd:discuss-phase N` | Szürke zónák felderítése, preferenciáid rögzítése |
| **3. Plan** | `/gsd:plan-phase N` | Research -> 2-3 atomi terv XML-ben -> verifikáció |
| **4. Execute** | `/gsd:execute-phase N` | Hullám-végrehajtás: parallel ahol lehet, friss context/terv |
| **5. Verify** | `/gsd:verify-work N` | UAT - te teszteled, ha nem jó -> auto debug + fix plan |

A lényeg: **te leírod mit akarsz, a rendszer kitalálja hogyan kell megépíteni, te jóváhagyod, és elmehetssz kávézni.**

---

## A core innováció: wave execution

```text
WAVE 1 (parallel)       WAVE 2 (parallel)       WAVE 3
┌────────┐ ┌────────┐   ┌────────┐ ┌────────┐   ┌────────┐
│ Plan 1 │ │ Plan 2 │ → │ Plan 3 │ │ Plan 4 │ → │ Plan 5 │
│ User   │ │Product │   │ Orders │ │ Cart   │   │Checkout│
│ Model  │ │ Model  │   │ API    │ │ API    │   │ UI     │
└────────┘ └────────┘   └────────┘ └────────┘   └────────┘
```

Független tervek párhuzamosan futnak (wave), függő tervek szekvenciálisan. Minden terv saját context window-ban - **zero kontextus szennyezés**.

---

## BMAD vs GSD - részletes összehasonlítás

> [!info] Két agentic framework
> A [[github/bmad-method|BMAD-METHOD]] és a GSD a két legnépszerűbb agentic fejlesztési framework. Mindkettőnek megvan a saját helye.

### Filozófia

| | [[github/bmad-method|BMAD-METHOD]] | GSD |
|---|---|---|
| **Alap kérdés** | "Hogyan építsünk szoftvert strukturáltan?" | "Hogyan tartjuk fenn a minőséget nagy projekteknél?" |
| **Megközelítés** | Agent personák (PM, Architect, Dev, UX...) | Context engineering (friss window = jobb output) |
| **Metafora** | Szoftvercég szimulálása | Solo dev turbo-módban |
| **Komplexitás** | A struktúrában van (42 skill, fázis-kapuk) | A rendszerben van (te csak 5 parancsot látsz) |

### Mikor melyiket

| Szituáció | Válaszd |
|---|---|
| Solo dev, "tudom mit akarok, csak építsd meg" | **GSD** |
| Team, több ember AI-val, közös struktúra kell | **BMAD** |
| Greenfield, nem tudom még mit akarok | **BMAD** (Analyst + PM fázis erősebb) |
| Meglévő kódbázis, új feature hozzáadás | **GSD** (`/gsd:map-codebase` + `/gsd:quick`) |
| Komplex architektúra döntések | **BMAD** (Architect persona strukturált) |
| Gyors iteráció, "ne akadályozz" | **GSD** (5 parancs, nincs ceremony) |
| Context rot probléma (nagy projektnél romlik a minőség) | **GSD** (erre lett tervezve) |
| Discovery fázis, requirements gathering | **BMAD** |

> [!warning] 1M context window hatása
> Az 1M context megjelenésével a GSD core innovációja (friss 200K context / terv) kevésbé kritikus - egy nagy context window-ban is fut a projekt quality degradation nélkül. A wave execution viszont továbbra is értékes a párhuzamos végrehajtás miatt.

---

## Telepítés

Multi-platform: Claude Code mellett OpenCode, Gemini CLI, Codex, Copilot, Cursor és más [[toolbox/ai-coding-agentek-osszehasonlitasa|AI coding agentek]] is támogatottak.

```bash
npx get-shit-done-cc@latest
```

A telepítés commands-öt tesz a `.claude/commands/` mappába. A `/gsd:help` megmutatja mi a következő lépés.

> [!tip] Hogyan használd promptban
> "Ez egy GSD-managed projekt. A STATE.md tartalmazza az aktuális állapotot, a ROADMAP.md a tervezett fázisokat. Jelenleg Phase 2 execute." - így Claude a GSD kontextusban marad.

---

## Kapcsolódó

- [[github/bmad-method|BMAD-METHOD]] - strukturáltabb alternatíva, agent personákkal és teljes SDLC lifecycle-kal
- [[toolbox/claude-code-projekt-setup|Claude Code]] - az elsődleges IDE amihez a GSD integrálódik
- [[github/claude-code-best-practice|Claude Code best practice]] - framework összehasonlítás (BMAD, GSD, Superpowers, stb.)
- [[github/everything-claude-code|Everything Claude Code]] - másik nagy referencia repó
