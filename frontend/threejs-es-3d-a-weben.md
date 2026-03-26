---
tags:
  - frontend
  - 3d
  - vizualizacio
datum: 2026-03-26
szint: "🧱 Scout"
kapcsolodo:
  - "[[frontend/react-vs-spa-vs-preact|React]]"
  - "[[frontend/nextjs|Next.js]]"
  - "[[frontend/css-vs-nextjs-vs-react|CSS vs Next.js vs React]]"
---

# Three.js és 3D a weben

> [!tldr] Miért releváns
> A Three.js a WebGL-re épülő 3D library, amivel interaktív vizuális élményeket építhetsz a böngészőben - portfolio, landing page, product showcase. Nem játékfejlesztés, hanem **vizuális differenciálás**.

---

## Kulcs tanulságok

- A Three.js **nem játékmotor** - a fő use case a weben: interaktív vizualizáció, 3D landing page-ek, product configurator-ok
- Három alapelem: **Scene** (a világ), **Camera** (honnan nézzük), **Renderer** (hogyan rajzolja ki a böngésző)
- A **react-three-fiber** (R3F) a [[frontend/react-vs-spa-vs-preact|React]] ökoszisztémába hozza be a Three.js-t - deklaratív JSX szintaxis, state management, komponens újrafelhasználás
- Ha sima 2D layout elég, a Three.js felesleges komplexitás - a "wow faktor" nem ér annyit, ha a betöltési idő 3x-ra nő
- A 3D asset-ek (modellek, textúrák) mérete a legnagyobb buktató - egy GLTF modell könnyedén 5-10MB

---

## Mikor használd (és mikor ne)

| Szituáció | Three.js? | Alternatíva |
|-----------|-----------|-------------|
| Portfolio / landing page "wow" effekttel | **Igen** - ez a klasszikus use case | Spline (no-code 3D) |
| Product configurator (3D-ben forgatható termék) | **Igen** | model-viewer web component |
| Adatvizualizáció 3D-ben (globe, network graph) | **Igen** | D3.js ha 2D elég |
| Normál weboldal, blog, SaaS dashboard | **Nem** - overkill | CSS animációk, Framer Motion |
| Játékfejlesztés | Részben | Unity WebGL, PlayCanvas |
| Mobil-first site lassú hálózaton | **Nem** - túl nagy payload | Lightweight CSS/SVG animáció |

---

## A Three.js ökoszisztéma React-tel

A legtöbb modern Three.js projekt **react-three-fiber**-t (R3F) használ, nem vanilla Three.js-t. Ez azért fontos, mert:

- JSX-ben írsz scene-t, nem imperatív kóddal
- React state és lifecycle hook-ok működnek a 3D scene-ben
- Az **@react-three/drei** library rengeteg kész helper-t ad (OrbitControls, Environment, Text3D, stb.)
- A **@react-three/postprocessing** vizuális effekteket ad (bloom, depth of field)

| Library | Mire jó |
|---------|---------|
| **react-three-fiber** | React renderer Three.js-hez - a fő library |
| **@react-three/drei** | Helpers: kamera kontrollok, loading, text, environment |
| **@react-three/gltfjsx** | GLTF 3D modellek -> React komponensek |
| **@react-three/postprocessing** | Vizuális effektek (bloom, blur, stb.) |

---

## Alternatívák

| Eszköz | Jelleg | Mikor válaszd |
|--------|--------|---------------|
| **Spline** | No-code 3D editor, embed-elhető | Gyors prototípus, designer-barát |
| **Babylon.js** | Full 3D engine (Microsoft) | Komplexebb jelenetek, játékok |
| **Vanilla WebGL** | Alacsony szintű API | Csak ha maximális kontroll kell |
| **CSS 3D transforms** | CSS-only 3D | Egyszerű kártya-flip, parallax effekt |

> [!tip] Praktikus tanács
> Three.js fejlesztésnél a boilerplate kód (scene setup, lighting, camera) repetitív, és a shader/material paraméterezés trial-and-error jellegű. AI coding agentek jól generálják az R3F komponenseket - különösen ha megadod a kontextust (pl. "rotating 3D product showcase, GLTF modell betöltés, orbit controls, environment lighting").

---

## [[frontend/nextjs|Next.js]] + Three.js

Three.js + Next.js esetén fontos: a 3D scene-t **dynamic import**-tal lazy-loadold, mert SSR-ben nem renderelhető (a Three.js a `window` és `document` objektumokra épül). A `next/dynamic` a `{ ssr: false }` opcióval a megoldás.

---

## AI-natív fejlesztés

A Three.js / react-three-fiber fejlesztés különösen jól párosul AI tool-okkal, mert a 3D scene setup, lighting, material konfiguráció és kamera beállítások trial-and-error jellegűek. Claude Code gyorsan generálja az R3F boilerplate-et és a drei helper-ek használatát - te a kreatív részre koncentrálhatsz.

> [!tip] Hogyan használd AI-val
> - *"Építs egy react-three-fiber scene-t: GLTF modell betöltés, OrbitControls, environment lighting, és post-processing bloom effekt"*
> - *"Csinálj egy 3D product showcase-t R3F-fel: forgatható termék, auto-rotation, kattintásra zoom, Next.js dynamic import-tal"*
> - *"Írj egy interaktív 3D globe komponenst react-three-fiber-rel, ahol a pontok kattinthatók és tooltip-et mutatnak"*

---

## Kapcsolódó

- [[frontend/react-vs-spa-vs-preact|React]] - R3F a React rendererre épül
- [[frontend/nextjs|Next.js]] - Three.js + Next.js: dynamic import-tal lazy-loadold a 3D scene-t
- [[frontend/css-vs-nextjs-vs-react|CSS vs Next.js vs React]] - mikor elég CSS animáció a 3D helyett
