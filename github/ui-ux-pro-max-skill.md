---
tags:
  - github
  - ai
  - design
  - skill
datum: 2026-03-26
szint: "🧱 Scout"
url: https://github.com/nextlevelbuilder/ui-ux-pro-max-skill
kapcsolodo:
  - "[[github/impeccable|Impeccable]]"
  - "[[toolbox/claude-code|Claude Code]]"
  - "[[frontend/nextjs|Next.js]]"
  - "[[frontend/shadcn-ui|shadcn/ui]]"
  - "[[frontend/tailwind-css|Tailwind CSS]]"
  - "[[_moc/moc-frontend-ui|MOC - Frontend UI]]"
---

# UI UX Pro Max Skill

**Kategória:** `Claude Code skill` / `design system`
**URL:** https://github.com/nextlevelbuilder/ui-ux-pro-max-skill

---

## Mi ez és mire jó?

> [!tldr] Egy mondatban
> AI skill ami komplett design rendszereket generál - szín, tipográfia, UI stílus, UX guideline - az iparág és a projekt kontextusa alapján.

Egy [[toolbox/claude-code|Claude Code]] (és más AI coding tool) skill, ami **design intelligenciát** ad a fejlesztési folyamathoz. Nem egy UI könyvtár - hanem egy **döntéstámogató rendszer** ami a "milyen legyen a design?" kérdésre ad azonnali, iparág-specifikus választ.

A lényeg: 161 iparági szabály, 67 UI stílus, 161 szín paletta, 57 font párosítás - mindet automatikusan javasol a projekt típusa alapján.

---

## Mikor hasznos?

- **Greenfield projekt indulásnál** - amikor még nincs design system, de kell szín, font, spacing döntés
- **Rapid prototyping** - MVP-hez gyorsan kell konzisztens vizuális nyelv
- **Dashboard / admin felület** - 25 chart típus ajánlás, data viz guideline

### Mikor NE használd

- Ha már van design system (Figma file, brand guide) - akkor az a forrás, nem ez
- Pixel-perfect design munkához - ez stratégiai szint, nem implementáció
- Ha a vizuális identitás már rögzített

---

## Stack támogatás

[[frontend/react|React]], [[frontend/nextjs|Next.js]], Vue, Svelte, SwiftUI, Flutter, React Native - 13 framework. A [[frontend/nextjs|Next.js]]-szel és a [[frontend/shadcn-ui|shadcn/ui]]-val különösen jól működik, mert a [[frontend/tailwind-css|Tailwind CSS]] color token-eket közvetlenül generálja.

A "Master + Overrides" pattern azt jelenti, hogy van egy alap design system (master), és komponens-szinten felülírhatod - ez jól illik a [[frontend/tailwind-css|Tailwind CSS]] + [[frontend/shadcn-ui|shadcn/ui]] workflow-hoz.

---

## AI-natív fejlesztés

A skill [[toolbox/claude-code|Claude Code]]-ban közvetlenül használható - a `.claude/skills/` mappába telepítve slash command-ként hívható.

Két fő use case:
1. **Projekt indításnál** - generáltat egy design system-et, amit a CLAUDE.md-be és a Tailwind config-ba ír
2. **Komponens fejlesztésnél** - ha bizonytalan vagy milyen stílust használj, a skill iparág-specifikus javaslatot ad

> [!tip] Hogyan használd AI-val
> "Generálj egy design system-et ehhez a SaaS projekthez az UI UX Pro Max skill-lel: fintech dashboard, dark mode preferált, Bento Grid layout." - a skill automatikusan szín palettát, tipográfiát és spacing rendszert generál.

---

## Kapcsolódó

- [[github/impeccable|Impeccable]] - design minőség-ellenőrzés (UI UX Pro Max a generálás, Impeccable az audit)
- [[toolbox/claude-code|Claude Code]] - az eszköz amibe a skill integrálódik
- [[frontend/nextjs|Next.js]] - primary frontend framework amihez a design system generálás releváns
- [[frontend/shadcn-ui|shadcn/ui]] - UI component library
