---
tags: [frontend, animacio, gsap]
datum: 2026-03-26
szint: "🧱 Brick"
kapcsolodo:
  - "[[frontend/framer-motion|Framer Motion]]"
  - "[[frontend/react|React]]"
  - "[[frontend/nextjs|Next.js]]"
---

## Mi ez és mire jó?

A **GSAP** (GreenSock Animation Platform) a **legerősebb JavaScript animációs engine** - nem React-specifikus, hanem bármilyen DOM elemet, SVG-t, Canvas-t, sőt JavaScript objektumot animálhat. Ha a [[frontend/framer-motion|Framer Motion]] a "kényelmes React animáció", a GSAP a "korlátlan animációs kontroll".

> [!tldr] Egy mondatban
> A GSAP az "After Effects a böngészőben" - timeline-alapú, pixel-pontos animációk landing page-ekhez, scroll-élményekhez, és marketing site-okhoz, ahol a vizuális hatás a prioritás.

### Mikor GSAP, mikor Framer Motion?

| | Framer Motion | **GSAP** |
|---|---|---|
| **Ideális terep** | [[frontend/react|React]] SPA, app UI, page transition | Landing page, marketing site, kreatív web |
| **React integráció** | Natív (props) | Wrapper kell (`useGSAP` hook) |
| **Tanulási görbe** | Alacsony | Közepes-magas |
| **Scroll animáció** | `useScroll` (alap) | ScrollTrigger (nagyon erős) |
| **Timeline** | Variants + stagger | `gsap.timeline()` (precíz kontroll) |
| **SVG morph** | Nem | MorphSVG plugin |
| **Teljesítmény** | Jó | Kiváló (requestAnimationFrame optimalizált) |

> [!tip] Egyszerű szabály
> **App-ot építesz?** - Framer Motion. **Landing page-et / marketing site-ot?** - GSAP.

---

## Fő képességek

| Feature | Mit csinál | Plugin kell? |
|---------|-----------|-------------|
| **gsap.to/from/fromTo** | Alap tween animáció (bármi animálható) | Nem (core) |
| **gsap.timeline()** | Többlépéses, szekvenciális animáció | Nem (core) |
| **ScrollTrigger** | Scroll pozícióhoz kötött animáció, pin, scrub | Igen (ingyenes) |
| **SplitText** | Szöveg karakterenkénti / szavankénti animáció | Igen (fizetős) |
| **MorphSVG** | SVG path morphing (alakváltás) | Igen (fizetős) |
| **Draggable** | Drag-and-drop animáció | Igen (ingyenes) |
| **Flip** | Layout változás animálása (hasonló Framer layout) | Igen (ingyenes) |

---

## GSAP + React

A GSAP nem React-natív, de a `@gsap/react` csomag ad egy `useGSAP` hook-ot ami kezeli a cleanup-ot és a React lifecycle-t:

```jsx
import { useGSAP } from '@gsap/react'
import gsap from 'gsap'

function Hero() {
  const container = useRef(null)

  useGSAP(() => {
    gsap.from('.hero-title', { y: 100, opacity: 0, duration: 1 })
    gsap.from('.hero-subtitle', { y: 50, opacity: 0, duration: 1, delay: 0.3 })
  }, { scope: container })

  return <div ref={container}>...</div>
}
```

> [!warning] Fontos
> Mindig `useGSAP`-et használj, ne sima `useEffect`-et + `gsap.to()`-t. A `useGSAP` automatikusan cleanup-ol (kill-eli az animációkat) unmount-kor - `useEffect`-tel ez elmarad és memory leak lesz.

---

## ScrollTrigger - a GSAP ütőkártyája

A ScrollTrigger a GSAP legerősebb pluginje - scroll pozícióhoz köt **bármilyen** animációt:

| Mód | Mit csinál | Használat |
|-----|-----------|-----------|
| **Toggle** | Scroll pozíciónál elindul az animáció | Section reveal, fade-in |
| **Scrub** | Az animáció a scroll-hoz van kötve (1:1) | Parallax, progress |
| **Pin** | Elem "ragad" a viewport-ban amíg az animáció tart | Full-page scroll sections |
| **Snap** | Scroll pozíciók közé "pattintja" a scroll-t | Slide-based layout |

---

## Mikor használd (és mikor NE)?

| Szituáció | GSAP? | Alternatíva |
|-----------|-------|-------------|
| Landing page, hero animáció | **Igen** | - |
| Scroll-driven storytelling | **Igen** - ScrollTrigger | - |
| SVG morphing / path animáció | **Igen** - MorphSVG | - |
| React SPA UI animációk | **Nem** - overkill | Framer Motion |
| Egyszerű hover / fade | **Nem** | CSS transition |
| React page transition | **Nem** - nehézkes | Framer Motion AnimatePresence |

---

## Buktatók

- **React cleanup** - mindig `useGSAP` hook, soha ne sima `useEffect` + `gsap.to()`, különben memory leak
- **ScrollTrigger + [[frontend/nextjs|Next.js]]** - SSR-rel ScrollTrigger nem inicializálható, `useGSAP`-ben vagy `useEffect`-ben regisztráld, és `{ once: true }` a `gsap.registerPlugin(ScrollTrigger)` hívásra
- **Fizetős pluginek** - SplitText, MorphSVG, ScrollSmoother a "Club GSAP" tagsághoz kötöttek. A legtöbb feature (ScrollTrigger, Flip, Draggable) viszont ingyenes
- **Bundle** - GSAP core ~30KB, de ScrollTrigger-rel ~45KB. Csak a használt plugineket importáld

---

## AI-natív fejlesztés

A GSAP timeline és ScrollTrigger konfigurációk generálása az egyik leghasznosabb AI use case - a paraméterek trial-and-error jellegűek, és Claude Code gyorsan iterálhat rajtuk. Az AI különösen jó a boilerplate setup-ban (useGSAP hook, ScrollTrigger regisztráció, cleanup).

> [!tip] Hogyan használd AI-val
> - *"Írj egy GSAP ScrollTrigger animációt, ahol a hero szekció szövege karakterenként jelenik meg scroll-ra (SplitText + scrub)"*
> - *"Csinálj egy useGSAP-alapú timeline-t: logo fade-in, majd title slide-up, majd CTA button scale-in - szekvenciálisan"*
> - *"Konvertáld ezt a CSS animációt GSAP ScrollTrigger-re, pin-nel és scrub-bal"*

---

## Kapcsolódó

- [[frontend/framer-motion|Framer Motion]] - React-natív alternatíva, egyszerűbb de kevésbé erős
- [[frontend/react|React]] - React alapok, GSAP-et `useGSAP` hook-kal integrálod
- [[frontend/nextjs|Next.js]] - SSR kompatibilitás ScrollTrigger-rel
