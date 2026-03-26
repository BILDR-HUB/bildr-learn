---
tags: [frontend, animacio, framer-motion, react]
datum: 2026-03-26
szint: "🧱 Brick"
kapcsolodo:
  - "[[frontend/gsap|GSAP]]"
  - "[[frontend/react|React]]"
  - "[[frontend/nextjs|Next.js]]"
  - "[[frontend/tailwind-css|Tailwind CSS]]"
---

## Mi ez és mire jó?

A **Framer Motion** (mostanában **Motion**) egy **deklaratív animációs library** React-hez. A lényege: `motion.div`-et írsz sima `div` helyett, és prop-okkal mondod meg mit animáljon - nem kell CSS keyframe-eket vagy JavaScript timeline-okat kézzel írni.

> [!tldr] Egy mondatban
> A Framer Motion a "CSS transition on steroids" - [[frontend/react|React]] komponensekhez deklaratív animációkat adsz (`animate`, `variants`, `layout`), ami komplexebb mint CSS, de sokkal egyszerűbb mint GSAP.

### Mikor melyik animációs megoldás?

| Komplexitás | Megoldás | Példa |
|-------------|----------|-------|
| **Egyszerű** | CSS transition / animation | Hover szín, fade-in |
| **Közepes** | **Framer Motion** | Page transition, layout animáció, gesture, scroll-reveal |
| **Komplex** | [[frontend/gsap|GSAP]] | Többlépéses timeline, SVG morph, scroll-driven parallax |

---

## Fő képességek

| Feature | Mit csinál | Mikor kell |
|---------|-----------|------------|
| **animate** | Alap animáció (opacity, x, y, scale, rotate) | Megjelenés, eltűnés |
| **variants** | Állapot-alapú animációk (hover, tap, stagger children) | Lista elemek belépése |
| **layout** | Automatikus layout animáció (pozíció/méret változás) | Filterable grid, reorder |
| **AnimatePresence** | Kilépő elemek animálása (exit animation) | Route váltás, modal bezárás |
| **useScroll** | Scroll pozícióhoz kötött animáció | Scroll progress bar, parallax |
| **gesture** | Drag, tap, hover, pan | Kártya swipe, interaktív UI |

---

## Framer Motion vs CSS vs GSAP

| Szempont | CSS | Framer Motion | GSAP |
|----------|-----|---------------|------|
| **React integráció** | Nincs (class toggle) | Natív (props) | Wrapper kell (`useGSAP`) |
| **Exit animáció** | Nagyon nehéz | `AnimatePresence` - egy sor | Manuális |
| **Layout animáció** | Lehetetlen pure CSS-ben | `layout` prop - automatikus | Manuális kalkuláció |
| **Scroll animáció** | `@scroll-timeline` (korlátozott) | `useScroll` | ScrollTrigger (legerősebb) |
| **Timeline** | Korlátozott | Variants + stagger | GSAP Timeline (legerősebb) |
| **Bundle méret** | 0 KB | ~50KB | ~30KB (core) |
| **Tanulási görbe** | Alacsony | Alacsony-közepes | Közepes-magas |

> [!tip] Döntési fa
> - **Csak hover/focus effekt kell?** - CSS transition, ne húzz be library-t
> - **React app, page transition, layout anim, gesture?** - Framer Motion
> - **Landing page, komplex scroll anim, SVG morph?** - GSAP + ScrollTrigger
> - **Mindkettő kell?** - Lehet mixelni, de inkább válassz egyet

---

## Gyakori minták

### 1. Page transition (Next.js)

Az `AnimatePresence` + `motion.div` wrapper-rel route váltáskor smooth fade/slide animációt kapsz. [[frontend/nextjs|Next.js]] App Router-rel a `template.tsx`-ben érdemes elhelyezni.

### 2. Staggered list

Variants rendszerrel a lista elemek egymás után jelennek meg (stagger), nem egyszerre. Dashboard kártyák, portfolio grid - azonnal professzionálisabb érzetet ad.

### 3. Layout animation

A `layout` prop automatikusan animálja a pozíció és méret változást - pl. filter-ezhető grid, ahol a kártyák "átúsznak" az új pozíciójukba.

### 4. Scroll-triggered reveal

A `useScroll` + `useTransform` hook-okkal scroll pozícióhoz köthetsz bármilyen animációt - parallax, progress bar, section fade-in.

---

## Buktatók

- **Bundle méret (~50KB)** - ha csak 1-2 egyszerű animáció kell, CSS transition jobb választás
- **Server Components** - Framer Motion kliens-only (`'use client'`), Server Components-ben nem használható közvetlenül
- **Túlanimálás** - könnyű elszállni, mert minden egyszerű. Kevesebb = jobb UX. Animáld a funkciót, ne a díszítést
- **layout prop performance** - nagy listákon (100+ elem) a layout animáció lassú lehet, ilyenkor FLIP manuálisan

---

## AI-natív fejlesztés

A Framer Motion deklaratív API-ja tökéletesen illeszkedik az AI-alapú fejlesztéshez - a prop-ok önleíróak (`animate`, `exit`, `layout`), ezért Claude Code pontosan érti mit akarsz elérni. Különösen a variants és stagger pattern-ek generálása gyors és megbízható.

> [!tip] Hogyan használd AI-val
> - *"Adj Framer Motion page transition-t ehhez a Next.js App Router layout-hoz - fade + slide animáció route váltáskor"*
> - *"Csinálj staggered list animációt variants-szal ehhez a dashboard card grid-hez"*
> - *"Írj egy useScroll-alapú parallax hero szekciót, ahol a háttérkép lassabban scrollozik"*

---

## Kapcsolódó

- [[frontend/gsap|GSAP]] - erősebb, de nem React-natív animációs engine
- [[frontend/react|React]] - React alapok, amire a Framer Motion épül
- [[frontend/nextjs|Next.js]] - page transition és layout animation kontextus
- [[frontend/tailwind-css|Tailwind CSS]] - stílusok mellé jönnek az animációk
