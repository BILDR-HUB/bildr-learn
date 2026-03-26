---
tags:
  - ai
  - frontend
  - kodgeneralas
datum: 2026-03-26
szint: "🌱 Newcomer"
kapcsolodo:
  - "[[toolbox/claude-code-building-structure|Claude Code Building Structure]]"
  - "[[toolbox/claude-code-projekt-setup|Claude Code]]"
---

# Screenshot to Code

**URL:** https://github.com/abi/screenshot-to-code
**Stars:** ~72K

> [!tldr] Egy mondatban
> Screenshot-ot vagy design-t bedobsz, és tiszta HTML/Tailwind/React/Vue kódot kapsz vissza.

---

## Mi ez és mire jó?

Feltöltesz egy képernyőképet (Figma design, meglévő weboldal, kézzel rajzolt vázlat, bármi), és az AI legenerálja a frontend kódot. Támogatott output: HTML + Tailwind, [[frontend/react-vs-spa-vs-preact|React]], Vue.

A [[toolbox/claude-code-building-structure|Claude Code Building Structure]] pipeline-ban ez a **HTML Mock fázist** gyorsíthatja: ha van vizuális referenciád (Figma, competitor screenshot, vázlat), a screenshot-to-code kiindulópontot ad amit aztán [[toolbox/claude-code-projekt-setup|Claude Code]]-dal finomítasz.

---

## Mikor hasznos?

- **Gyors prototípus** - competitor website -> screenshot -> kód -> saját verzió
- **Figma -> kód** - ha nincs Figma dev mode, screenshot-ból indulsz
- **HTML Mock fázis gyorsítás** - a Building Structure pipeline 4. lépéséhez
- **Kézzel rajzolt vázlat** - papíron rajzolt wireframe -> fotó -> kód

### Mikor NE használd

- Pixel-perfect reprodukcióhoz - az AI közelít, de nem tökéletes
- Komplex interaktivitáshoz - a statikus layout-ot generálja, a logikát nem
- Ha már van component library-d - akkor inkább közvetlenül Claude Code-dal építs, a meglévő komponensekből

> [!tip] Workflow tipp
> A screenshot-to-code generálja a kiindulást -> Claude Code finomítja, komponensekre bontja, és hozzáadja a logikát. Példa prompt: "Itt van a screenshot-to-code által generált HTML. Bontsd shadcn/ui komponensekre és add hozzá az API hívásokat az API_spec.md alapján."

---

## AI-natív fejlesztés

Képernyőkép alapú kódgenerálás - kiegészíti a Claude Code szöveges workflow-ját vizuális input-tal. A Building Structure pipeline HTML Mock fázisában különösen hasznos: screenshot-ból generált kiindulópont, amit Claude Code finomít és komponensekre bont.

> [!tip] Hogyan használd AI-val
> - *"Itt van a screenshot-to-code által generált HTML. Bontsd shadcn/ui komponensekre és add hozzá az API hívásokat az API_spec.md alapján."*
> - *"Készíts screenshot-ot erről a competitor oldalról és generálj belőle HTML + Tailwind kiindulópontot, amit aztán Claude Code-dal alakítunk át"*

---

## Kapcsolódó

- [[toolbox/claude-code-building-structure|Claude Code Building Structure]] - a HTML Mock fázist gyorsítja
- [[toolbox/claude-code-projekt-setup|Claude Code]] - a generált kód finomítására
