---
tags:
  - github
  - ai
  - vizualizacio
  - skill
datum: 2026-03-26
szint: "🌱 Newcomer"
url: https://github.com/nicobailon/visual-explainer
kapcsolodo:
  - "[[toolbox/claude-code|Claude Code]]"
  - "[[toolbox/claude-code-skills-es-plugins|Claude Code Skills]]"
  - "[[_moc/moc-ai-tooling|MOC - AI Tooling]]"
---

# visual-explainer

**Kategória:** `vizualizáció` / `AI skill`
**URL:** https://github.com/nicobailon/visual-explainer

---

## Mi ez és mire jó?

> [!tldr] Egy mondatban
> Agent skill ami a terminál szöveges outputját szép, interaktív HTML oldalakká alakítja - diagramok, táblázatok, diff review-k, slide deck-ek.

A terminálban minden szöveg. A visual-explainer a [[toolbox/claude-code|Claude Code]] output-ot (architektúra, diff, plan, adatok) **böngészőben megnyitható HTML oldalakká** konvertálja: Mermaid diagramok zoom/pan-nel, színes táblázatok, dark/light mode, slide deck formátum.

---

## Mikor hasznos?

- **Architektúra bemutatás** - `/generate-web-diagram` interaktív diagram a rendszerről
- **Code review vizualizáció** - `/diff-review` színes, olvasható diff
- **Prezentáció készítés** - `/generate-slides` magazine-quality slide deck
- **Projekt átadás** - `/project-recap` összefoglaló oldal context-switching-hez

### Mikor NE használd

- Gyors, egyszerű kérdésekre - a HTML generálás overhead ha csak egy választ vársz
- Ha a terminál output elég - nem minden output-ot kell vizualizálni

---

## AI-natív fejlesztés

Plugin marketplace-ről telepíthető [[toolbox/claude-code|Claude Code]]-ba. A parancsok (`/generate-web-diagram`, `/diff-review`, stb.) a session-ben közvetlenül használhatók. Az output egy HTML fájl amit a böngésző automatikusan megnyit.

Különösen hasznos, ha nem-technikai stakeholder-eknek kell bemutatni az AI agent munkáját - a terminál output helyett vizuális, böngészőben megtekinthető formátumot kapsz.

> [!tip] Hogyan használd AI-val
> "Generálj egy vizuális architektúra diagramot a projektről a visual-explainer-rel." - az agent interaktív HTML oldalt készít Mermaid diagramokkal, ami böngészőben megnyitható.

---

## Kapcsolódó

- [[toolbox/claude-code|Claude Code]] - plugin-ként integrálódik
- [[toolbox/claude-code-skills-es-plugins|Claude Code Skills]] - a skill/plugin rendszer amibe tartozik
