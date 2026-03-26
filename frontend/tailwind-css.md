---
tags: [frontend, tailwind, css, styling]
datum: 2026-03-26
szint: "🌱 Newcomer"
kapcsolodo:
  - "[[frontend/nextjs|Next.js]]"
  - "[[frontend/css-vs-nextjs-vs-react|CSS vs Next.js vs React]]"
  - "[[frontend/shadcn-ui|shadcn/ui]]"
  - "[[frontend/responsive-design|Responsive Design]]"
---

## Mi ez?

> [!tldr]
> Utility-first CSS framework. Ahelyett, hogy CSS fájlokban írsz stílusokat, utility class-okat raksz közvetlenül a HTML/JSX-re. Gyors, kiszámítható, és **AI-barát** - [[toolbox/claude-code-projekt-setup|Claude Code]] tökéletesen generálja, mert a class nevek önmagukban leírják a stílust.

## Miért Tailwind?

A hagyományos [[frontend/css-vs-nextjs-vs-react|CSS]]-nél gondolkodnod kell az elnevezéseken, fájlstruktúrán, specificity-n. Tailwind-dal nincs ilyen probléma - a stílus ott van, ahol a komponens.

| Szempont | Tailwind | CSS Modules | styled-components | Vanilla CSS |
|---|---|---|---|---|
| **Sebesség** | Nagyon gyors | Közepes | Lassabb (runtime) | Lassú nagy projektnél |
| **AI generálás** | Kiváló | Jó | Közepes | Közepes |
| **Bundle méret** | Kicsi (purge) | Kicsi | Nagyobb | Változó |
| **Tanulási görbe** | Class nevek | CSS + scoping | CSS + JS API | Csak CSS |

## Fő funkciók

| Funkció | Hogyan |
|---|---|
| **Responsive** | Prefix-ek: `sm:`, `md:`, `lg:`, `xl:`, `2xl:` |
| **Dark mode** | `dark:` prefix (pl. `dark:bg-gray-900`) |
| **Hover/focus** | `hover:`, `focus:`, `active:` prefix-ek |
| **Animációk** | `animate-spin`, `animate-pulse`, `transition-all` |
| **Egyedi értékek** | Arbitrary values: `w-[137px]`, `bg-[#1a1a2e]` |
| **Csoportos hover** | `group` + `group-hover:` a szülő-gyerek relációkhoz |

## Best practice

- **Használj [[frontend/shadcn-ui|shadcn/ui]]-t** - előre épített, szép komponensek Tailwind-dal, copy-paste alapon
- **Ne írj custom CSS-t** - ha Tailwind-ban nem oldható meg, valószínűleg over-engineered
- **Mobile-first**: alap stílus = mobil, majd `md:` és `lg:` a nagyobb képernyőkre
- Lásd: [[frontend/responsive-design|Responsive Design]]

## Mikor válaszd

- **Szinte mindig**, ha React/[[frontend/nextjs|Next.js]] projektet építesz - ez a default styling megoldás
- Next.js alapértelmezetten Tailwind-dal jön
- Ha Claude Code-dal dolgozol, Tailwind a leggyorsabb, mert nem kell külön CSS fájlokat kezelni

---

## AI-natív fejlesztés

A Tailwind CSS az AI coding tool-ok kedvence - a class nevek önleíróak, ezért Claude Code tökéletesen generálja a stílusokat anélkül, hogy külön CSS fájlokat kellene kezelnie. A Tailwind utility class-ok + shadcn/ui komponensek kombinációja a leggyorsabb AI-natív UI fejlesztési workflow.

> [!tip] Hogyan használd AI-val
> - *"Stílusozd ezt a komponenst Tailwind-dal: dark mode támogatás, responsive layout, hover effektek"*
> - *"Konvertáld ezt a CSS/SCSS fájlt Tailwind utility class-okra"*
> - *"Építs egy landing page hero szekciót Tailwind-dal: gradient háttér, centered szöveg, CTA gomb, responsive"*

---

## Kapcsolódó

- [[frontend/nextjs|Next.js]] - Tailwind a Next.js projektek alapértelmezett styling megoldása
- [[frontend/css-vs-nextjs-vs-react|CSS vs Next.js vs React]] - CSS megközelítések összehasonlítása
- [[frontend/shadcn-ui|shadcn/ui]] - Radix + Tailwind alapú copy-paste komponensek
- [[frontend/responsive-design|Responsive Design]] - mobile-first design Tailwind breakpoint-okkal
