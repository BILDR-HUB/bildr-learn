---
tags:
  - moc
datum: 2026-03-26
---

# MOC - Frontend UI

> [!tldr]
> Ez a Map of Content összegyűjti a repo összes frontend UI-hoz kapcsolódó jegyzetét. CSS framework-öktől a komponens könyvtárakon át az animációs engine-ekig.

---

## Styling alapok

| Jegyzet | Leírás |
|---------|--------|
| [[frontend/tailwind-css\|Tailwind CSS]] | Utility-first CSS framework — class-ok közvetlenül a HTML/JSX-en, AI-barát, gyors fejlesztés |
| [[frontend/responsive-design\|Responsive Design]] | Reszponzív megközelítés — egy kódbázis, minden képernyőméret, mobile-first, Tailwind breakpoint-ok |
| [[frontend/css-vs-nextjs-vs-react\|CSS vs Next.js vs React]] | Styling megközelítések összehasonlítása — mikor melyik technológiát használd |

## Komponens könyvtárak

| Jegyzet | Leírás |
|---------|--------|
| [[frontend/shadcn-ui\|shadcn/ui]] | Copy-paste komponens gyűjtemény — Radix + Tailwind, a kód a tiéd, módosítható, kiterjeszthető |
| [[frontend/base-ui-vs-radix\|Base UI vs Radix]] | Headless UI könyvtárak — viselkedés és a11y stílus nélkül, shadcn/ui ezekre épül |

## Animáció

| Jegyzet | Leírás |
|---------|--------|
| [[frontend/framer-motion\|Framer Motion]] | Deklaratív React animáció — `motion.div`, prop-alapú, layout animáció, gesture support |
| [[frontend/gsap\|GSAP]] | Professzionális animációs engine — timeline, scroll trigger, SVG, Canvas, nem React-specifikus |

## 3D és vizualizáció

| Jegyzet | Leírás |
|---------|--------|
| [[frontend/threejs-es-3d-a-weben\|Three.js és 3D a weben]] | 3D grafika böngészőben — WebGL, React Three Fiber, interaktív 3D scene-ek |

---

## Kapcsolódó note-ok

| Jegyzet | Kapcsolat |
|---------|-----------|
| [[frontend/react\|React]] | UI könyvtár — a legtöbb frontend UI eszköz React-re épül |
| [[frontend/nextjs\|Next.js]] | React meta-framework — SSR, routing, production-ready |
| [[frontend/react-hook-form\|React Hook Form]] | Form kezelés — shadcn/ui form komponensekkel integrálva |
| [[frontend/data-table-filters\|Data Table Filters]] | TanStack Table referencia — UI-intenzív adattábla szűrőkkel |
| [[frontend/mdx\|MDX]] | Markdown + React — tartalomközpontú UI-hoz |
| [[toolbox/figma\|Figma]] | Design tool — UI tervezés, design token-ek, fejlesztő handoff |
| [[toolbox/screenshot-to-code\|Screenshot to Code]] | Design → kód — AI-val generált Tailwind/React kód |
| [[_moc/moc-react\|MOC - React]] | React ökoszisztéma — hooks, state, adatkezelés |

---

## Tanulási útvonal

Ajánlott sorrend ha nulláról kezded:
1. [[frontend/tailwind-css\|Tailwind CSS]] — utility-first CSS, a modern frontend alapja
2. [[frontend/responsive-design\|Responsive Design]] — reszponzív layout, mobile-first szemlélet
3. [[frontend/shadcn-ui\|shadcn/ui]] — production-ready komponensek, copy-paste, testreszabható
4. [[frontend/base-ui-vs-radix\|Base UI vs Radix]] — értsd meg mire épül a shadcn/ui
5. [[frontend/framer-motion\|Framer Motion]] — ha animációkra van szükséged React-ben
6. [[frontend/gsap\|GSAP]] — ha korlátlan animációs kontroll kell (landing page, marketing site)
7. [[frontend/threejs-es-3d-a-weben\|Three.js]] — 3D és vizualizáció a weben (haladó)

> [!tip] Styling döntés
> **Tailwind + shadcn/ui** a bildr.hub ajánlott stack. Gyors, AI-barát (Claude Code tökéletesen generálja), és a kód a tiéd. Ha fancy animáció kell: Framer Motion (egyszerű) vagy GSAP (komplex).

---

## Hézagok

- [ ] Design token-ek — Tailwind theme, CSS variables, dark mode stratégia
- [ ] Accessibility (a11y) — ARIA, screen reader, keyboard navigation best practice-ek
- [ ] CSS Grid deep dive — komplex layout minták, Tailwind grid utility-k
- [ ] Icon rendszerek — Lucide, Heroicons, SVG sprite, icon library választás
