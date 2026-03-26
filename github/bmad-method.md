---
tags:
  - ai
  - claude-code
  - modszertan
  - workflow
datum: 2026-03-26
szint: "🏗️ Builder"
kapcsolodo:
  - "[[toolbox/claude-code-projekt-setup|Claude Code]]"
  - "[[github/get-shit-done|Get Shit Done (GSD)]]"
  - "[[github/claude-code-best-practice|Claude Code best practice]]"
  - "[[github/everything-claude-code|Everything Claude Code]]"
---

# BMAD-METHOD

**URL:** https://github.com/bmad-code-org/BMAD-METHOD
**Stars:** ~42K
**Docs:** https://docs.bmad-method.org

> [!tldr] Egy mondatban
> Teljes szoftverfejlesztési életciklust lefedő AI-vezérelt agilis keretrendszer - 12+ specializált agent persona (PM, Architect, Developer, UX, Scrum Master) és 34+ workflow brainstorming-tól deployment-ig.

---

## Mi ez és mire jó?

A **BMAD** (Build More Architect Dreams) abban különbözik a legtöbb [[toolbox/claude-code-projekt-setup|Claude Code]] workflow-tól, hogy **nem csak a kódolásra fókuszál**, hanem a teljes SDLC-re: ötletelés -> követelmény -> architektúra -> implementáció -> tesztelés -> deployment. Minden fázishoz van specializált agent persona, aki "interjúzik" veled és strukturáltan viszi végig a folyamatot.

| Fázis | Agent persona | Mit csinál |
|---|---|---|
| **Ötletelés** | Analyst | Brainstorming, üzleti elemzés, piackutatás |
| **Tervezés** | PM | PRD készítés, user story-k, prioritizálás |
| **Architektúra** | Architect | Tech stack döntések, rendszerterv, adatmodell |
| **Implementáció** | Developer | Kódolás a terv alapján, fázisokra bontva |
| **Tesztelés** | Test Architect | Kockázat-alapú tesztstratégia, automatizálás |
| **UX** | UX Designer | UI/UX review, felhasználói élmény |

**Fő különbség a [[github/get-shit-done|GSD]] stílustól:** a BMAD _agent persona_-kat használ (a PM nem kódol, az Architect nem tesztel), míg a GSD egyetlen Claude session-ben oldja meg a feladatot szint-alapú eszkalációval.

---

## Mikor hasznos?

- **Greenfield projekt** amit nulláról építesz - a teljes lifecycle struktúra segít nem kihagyni lépéseket
- **Discovery fázis** - az Analyst és PM persona jól használható requirements gathering-re
- **Komplex architektúra döntések** - az Architect persona strukturáltan végigviszi a tech stack és rendszerterv kérdéseket
- **Több ember dolgozik az AI-val** - a BMAD struktúra egységesíti, hogy ki mit csinál

### Mikor NE használd

- **Kisebb feladatok** - bugfix, egyszerű feature -> vanilla Claude Code gyorsabb
- **Meglévő, jól strukturált projekthez** - ha a kódbázis kialakult, a BMAD teljes SDLC overhead felesleges
- Ha a saját workflow-d elegendő - ne cseréld le bevált rendszert csak mert ez népszerű

---

## Telepítés és használat

A BMAD telepíthető bármilyen AI IDE-be (Claude Code, Cursor, stb.) az npx installer-rel:

```bash
npx bmad-method install
```

Ez a `.claude/` mappába skill-eket és agent konfigokat tesz. A `bmad-help` parancs bármikor megmondja mi a következő lépés.

**Scale-adaptive intelligence:** a BMAD automatikusan kalibrálja a tervezés mélységét - egy bugfix-hez nem kell teljes PRD, de egy enterprise rendszerhez igen. Ez a legértékesebb tulajdonsága: nem te döntöd el melyik workflow kell, hanem a framework javasol a komplexitás alapján.

> [!tip] Hogyan használd promptban
> "Ez egy BMAD-managed projekt, az architektúra az `architecture.md`-ben van, a PRD a `prd.md`-ben. A jelenlegi fázis: implementáció, dev-loop szerint dolgozz." - így Claude a BMAD kontextusban marad.

---

## Összehasonlítás - agentic framework-ök

| | BMAD-METHOD | [[github/get-shit-done|GSD]] | Superpowers |
|---|---|---|---|
| **Megközelítés** | Agent personák + teljes SDLC | Context engineering + wave execution | TDD-first + Iron Laws |
| **Erősség** | Átfogó, strukturált | Gyors, pragmatikus, multi-platform | Tesztelés-központú |
| **Skill-ek** | 42 | 0 (commands) | 14 |
| **Mikor válaszd** | Greenfield, komplex projekt, team | Solo dev, gyors iteráció | Ha a tesztelés a prioritás |

Részletesebb összehasonlítás: [[github/get-shit-done|Get Shit Done (GSD)]]

---

## Kapcsolódó

- [[toolbox/claude-code-projekt-setup|Claude Code]] - az elsődleges IDE amihez a BMAD integrálódik
- [[github/get-shit-done|Get Shit Done (GSD)]] - alternatív, pragmatikusabb workflow
- [[github/claude-code-best-practice|Claude Code best practice]] - tartalmazza a BMAD összehasonlítását más framework-ökkel
- [[github/everything-claude-code|Everything Claude Code]] - másik nagy referencia repó, más megközelítés
