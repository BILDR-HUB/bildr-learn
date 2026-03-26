---
tags: [frontend, responsive, css, tailwind]
datum: 2026-03-26
szint: "🌱 Newcomer"
kapcsolodo:
  - "[[frontend/tailwind-css|Tailwind CSS]]"
  - "[[frontend/css-vs-nextjs-vs-react|CSS vs Next.js vs React]]"
  - "[[frontend/nextjs|Next.js]]"
  - "[[frontend/shadcn-ui|shadcn/ui]]"
  - "[[frontend/framer-motion|Framer Motion]]"
---

## Mi ez és mire jó?

A **Responsive Design** az a megközelítés, hogy egy weboldalnak minden képernyőméreten jól kell kinéznie - mobilon, tableten, desktopon. Nem külön "mobil verzió", hanem **egy kódbázis**, ami alkalmazkodik.

> [!tldr] Egy mondatban
> Responsive design = a layout alkalmazkodik a képernyőmérethez, jellemzően mobile-first megközelítéssel: először mobilra tervezel, aztán bővíted nagyobb kijelzőkre.

---

## Mobile-first vs Desktop-first

| Szempont | Mobile-first | Desktop-first |
|----------|-------------|---------------|
| **Alap stílus** | Mobil (legkisebb) | Desktop (legnagyobb) |
| **Breakpoint irány** | `min-width` (felfelé bővít) | `max-width` (lefelé szűkít) |
| **[[frontend/tailwind-css|Tailwind CSS]] logika** | Alap = mobil, `md:` = tablet, `lg:` = desktop | Nem támogatott natívan |
| **Előny** | Könnyebb bővíteni, jobb performance mobilon | Ismerősebb desktop fejlesztőknek |
| **Mikor válaszd** | Legtöbb projekt (ajánlott) | Legacy redesign |

> [!tip] Tailwind = mobile-first
> Tailwind CSS-ben az alap class mindig mobilra vonatkozik. A `md:`, `lg:` prefix-ek **felfelé** bővítenek. Tehát `text-sm md:text-base lg:text-lg` = mobilon kicsi, tableten normál, desktopon nagy.

---

## Tailwind breakpoint-ok

| Prefix | Min-width | Tipikus eszköz |
|--------|-----------|-----------------|
| *(nincs)* | 0px | Mobil (alap) |
| `sm:` | 640px | Nagy mobil / kis tablet |
| `md:` | 768px | Tablet |
| `lg:` | 1024px | Laptop |
| `xl:` | 1280px | Desktop |
| `2xl:` | 1536px | Nagy monitor |

---

## Gyakori responsive minták

| Pattern | Mobil | Desktop | Tailwind megoldás |
|---------|-------|---------|-------------------|
| **Navigáció** | Hamburger menu | Horizontális nav | `hidden md:flex` + mobil menu state |
| **Grid** | 1 oszlop | 2-3-4 oszlop | `grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3` |
| **Sidebar** | Rejtett / bottom sheet | Fix oldalsáv | `hidden lg:block` + mobil drawer |
| **Szöveg méret** | Kisebb | Nagyobb | `text-sm md:text-base lg:text-lg` |
| **Padding** | Tömörebb | Légiesebb | `p-4 md:p-6 lg:p-8` |
| **Kép** | Teljes szélesség | Korlátozott | `w-full max-w-2xl mx-auto` |

---

## Modern CSS technikák

### Container Queries (CSS 2023+)

A hagyományos media query a **viewport**-ot figyeli. A container query a **szülő elem** méretét - komponens-szintű responsive viselkedés.

```css
/* Ha a szülő container < 400px, stack layout */
@container (max-width: 400px) {
  .card { flex-direction: column; }
}
```

Tailwind v4-ben: `@container` support natív.

### Fluid Typography

Fix breakpoint-ok helyett **folytonos skálázás** `clamp()`-pel:

```css
/* 16px mobilon, 20px desktopon, közötte fluid */
font-size: clamp(1rem, 0.5rem + 1.5vw, 1.25rem);
```

---

## Döntési fa

```
Új projekt responsive stratégiája?
├── Ki a fő célközönség?
│   ├── Mobil-first (B2C, e-commerce, média) → Mobile-first
│   └── Desktop-first (B2B dashboard, admin) → Még mindig mobile-first ajánlott
├── Framework?
│   ├── Tailwind CSS → Beépített mobile-first breakpoint-ok
│   └── Vanilla CSS → @media (min-width: ...) kézzel
└── Komponens-szintű responsive kell?
    ├── Igen → Container Queries
    └── Nem → Media Queries elég
```

---

## AI-natív fejlesztés

A responsive layout az egyik terület ahol az AI coding tool-ok rengeteg időt spórolnak - a Tailwind breakpoint class-ok generálása, grid layout-ok és responsive navigáció boilerplate-je gyorsan készül. Claude Code-dal megadod a design-t szöveggel, és a teljes responsive implementációt megkapod.

> [!tip] Hogyan használd AI-val
> - *"Csinálj egy responsive navigációt Tailwind-dal: mobilon hamburger menu (Sheet komponens), desktopon horizontális navbar"*
> - *"Alakítsd át ezt a fix layout-ot responsive grid-re: 1 oszlop mobilon, 2 tableten, 3 desktopon"*
> - *"Adj container query-t ehhez a Card komponenshez: 400px alatt stack, felette side-by-side layout"*

---

## Kapcsolódó

- [[frontend/tailwind-css|Tailwind CSS]] - mobile-first breakpoint rendszer (`sm:`, `md:`, `lg:`)
- [[frontend/css-vs-nextjs-vs-react|CSS vs Next.js vs React]] - styling megközelítések és responsive kezelésük
- [[frontend/nextjs|Next.js]] - Image component (responsive képek), Layout system
- [[frontend/shadcn-ui|shadcn/ui]] - responsive komponens variánsok (Sheet = mobil drawer)
- [[frontend/framer-motion|Framer Motion]] - responsive animációk (AnimatePresence mobil menu-höz)
