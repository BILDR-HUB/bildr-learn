---
tags:
  - github
  - ai
  - workflow
  - skill
datum: 2026-03-26
szint: "🏗️ Builder"
url: https://github.com/obra/superpowers
kapcsolodo:
  - "[[toolbox/claude-code|Claude Code]]"
  - "[[toolbox/claude-code-building-structure|Claude Code Building Structure]]"
  - "[[toolbox/claude-code-skills-es-plugins|Claude Code Skills]]"
  - "[[_moc/moc-ai-tooling|MOC - AI Tooling]]"
---

# superpowers

**Kategória:** `fejlesztési metodológia` / `skill framework`
**URL:** https://github.com/obra/superpowers

---

## Mi ez és mire jó?

> [!tldr] Egy mondatban
> Strukturált szoftverfejlesztési metodológia AI agent-ekhez - TDD, szisztematikus tervezés, és automatikus review, skill-ként telepítve.

A superpowers filozófiája: **ne hagyd az AI-t rögtön kódolni**. Helyette egy fegyelmezett pipeline-on vezeti végig: Socratic kérdések, tervezés (2-5 perces task-ok), TDD (RED-GREEN-REFACTOR), review, debugging (4 fázisú root cause analysis).

Ez a [[toolbox/claude-code-building-structure|Claude Code Building Structure]] note-ban leírt módszertannak a **kód-szintű párja**: míg a Building Structure a projekt-szintű pipeline-ról szól (PRD, Architecture, Mock, API, Schema, Subagents), a superpowers a napi kódolás fegyelmezettségét biztosítja.

---

## Mikor hasznos?

- **Komplex feature implementáció** - a tervezési fázis megakadályozza hogy az AI rossz irányba induljon
- **Bugfix** - a 4 fázisú root cause analysis szisztematikusabb mint az ad-hoc debugging
- **Kód minőség** - TDD kikényszerítése, automatikus review
- **Új projekteken** - ahol még nincs kialakult workflow

### Mikor NE használd

- Egyszerű, gyors feladatokra - túl sok ceremony egy egysoros fix-hez
- Ha már van saját jól működő workflow-d

---

## AI-natív fejlesztés

Skill-ként települ [[toolbox/claude-code|Claude Code]]-ba, automatikusan aktiválódik a megfelelő pillanatokban. A TDD kikényszerítés az egyik legerősebb feature-je - megakadályozza, hogy az AI agent teszt nélkül szállítson kódot.

> [!tip] Hogyan használd AI-val
> "Használd a superpowers design phase-t - kérdezz vissza mielőtt elkezdesz kódolni, aztán TDD-vel implementáld." - az AI agent először megérti a feladatot, aztán tervez, aztán implementál tesztekkel.

---

## Kapcsolódó

- [[toolbox/claude-code-building-structure|Claude Code Building Structure]] - projekt-szintű pipeline (superpowers a kód-szintű fegyelem)
- [[toolbox/claude-code|Claude Code]] - az eszköz amire épül
- [[toolbox/claude-code-skills-es-plugins|Claude Code Skills]] - skill rendszer amibe integrálódik
