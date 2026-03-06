---
tags: [frontend, css, react, nextjs, osszehasonlitas]
datum: 2026-02-25
szint: "🌱 Newcomer"
kapcsolodo:
  - "[[frontend/react-vs-spa-vs-preact|React vs SPA vs Preact]]"
  - "[[frontend/nextjs|Next.js]]"
  - "[[backend/hono|Hono]]"
  - "[[cloud/cloudflare|Cloudflare]]"
  - "[[cloud/vercel|Vercel]]"
  - "[[frontend/bun-nextjs-projekt-setup|Bun - Next.js projekt setup]]"
  - "[[frontend/env-valtozok-nextjs-ben|Env valtozok Next.js-ben]]"
  - "[[frontend/landing-page-cms-types|Landing Page CMS types]]"
---

## Mi a kulonbseg?

Ez a harom fogalom **mas-mas szinten** helyezkedik el a frontend fejlesztesben — nem egymas alternativai, hanem egymasra epulnek:

```mermaid
graph TD
    CSS[CSS<br/>Stilusozas / kinezet] --> REACT[React<br/>UI komponens konyvtar]
    REACT --> NEXT[Next.js<br/>Full-stack framework]

    CSS -.-> TW[Tailwind CSS]
    CSS -.-> SHADCN[shadcn/ui]
    REACT -.-> VITE[Vite + React<br/>SPA]
    NEXT -.-> VERCEL[Vercel deploy]

    style CSS fill:#264de4,color:white
    style REACT fill:#61dafb,color:black
    style NEXT fill:#000,color:white
```

| Szint | Mi ez | Mire jo | Pelda |
|---|---|---|---|
| **CSS** | Stilusnyelv | Elemek kinezete (szin, elrendezes, animacio) | `color: red`, Tailwind, shadcn/ui |
| **React** | UI konyvtar | Komponensek, state kezeles, interaktivitas | `useState`, `onClick`, JSX |
| **[[frontend/nextjs|Next.js]]** | Framework | SSR, routing, API routes, deployment | App Router, Server Components |

---

## Mikor melyiket hasznald?

### Csak CSS kell (framework nelkul)

- **Statikus landing page** — HTML + CSS eleg, nincs dinamikus tartalom
- **Email template** — React nem fut emailben, tiszta HTML/CSS kell
- **Egyszeru brochure site** — ha nincs interaktivitas, felesleges a JS framework

### React (Next.js nelkul, pl. Vite + React)

- **SPA (Single Page Application)** — admin panel, belso dashboard, ahol nincs SEO szukseglet
- **Widget beagyazas** — ha egy meglevo oldalba kell React komponenst tenni
- **Tanulas** — ha eloszor tanulsz React-et, egyszerubb Vite-tel mint Next.js-szel

### Next.js (a legtobb projekt)

- **SEO fontos** — SSR/SSG biztositja, hogy a Google indexeli
- **Full-stack** — frontend + API routes + auth egy helyen
- **[[cloud/vercel|Vercel]]** deploy — zero-config, preview deploymentek
- **Ugyfelek szamara** — production-ready, skalazható

> [!tip] Okolszabaly
> **Ugyfelprojekt = [[frontend/nextjs|Next.js]]** (mindig). **Belso tool / admin = React + Vite** (ha egyszeru). **Email / statikus = HTML + CSS**.

---

## CSS framework-ok osszehasonlitas

| Framework | Megkozelites | Mikor hasznald | Jellemzo hasznalat |
|---|---|---|---|
| **Tailwind CSS** | Utility-first class-ok | Gyors fejlesztes, egyedi design | Elsodleges |
| **shadcn/ui** | Tailwind-alapu komponensek | Elore kesz, testreszabhato UI elemek | Elsodleges |
| **CSS Modules** | Scoped CSS fajlok | Izolalt komponens stilusok | Ritkan |
| **Styled Components** | CSS-in-JS | Runtime stilusok, themed alkalmazasok | Nem jellemzo |
| **Bootstrap** | Elore kesz komponensek | Gyors prototipus, admin template | Legacy projektek |

---

## React vs Next.js — reszletes

| Szempont | React (Vite) | [[frontend/nextjs|Next.js]] |
|---|---|---|
| **Rendering** | Csak CSR (kliens oldali) | SSR + SSG + ISR + CSR |
| **Routing** | React Router (manualis) | File-based (automatikus) |
| **API backend** | Kulon szerver kell | API routes beepitve |
| **SEO** | Rossz (JS renderezes) | Kivalo (szerver renderel) |
| **Deploy** | Statikus hosting eleg | [[cloud/vercel|Vercel]] / Node szerver kell |
| **Build meret** | Kisebb | Nagyobb (de optimalizaltabb) |
| **Tanulasi gorbe** | Alacsonyabb | Magasabb (de tobbet ad) |
| **Server Components** | Nincs | Igen (kevesebb JS a bongeszőben) |

---

## Kapcsolodo

- [[frontend/react-vs-spa-vs-preact|React vs SPA vs Preact]] — React, SPA minta es Preact kozotti kulonbsegek
- [[frontend/nextjs|Next.js]] — a fo framework, reszletes leiras
- [[backend/hono|Hono]] — backend API React SPA melle (Cloudflare-en)
- [[cloud/cloudflare|Cloudflare]] — SPA hosting (Pages) + API (Workers)
- [[cloud/vercel|Vercel]] — Next.js nativ hosting platformja
- [[frontend/bun-nextjs-projekt-setup|Bun - Next.js projekt setup]] — projekt inditas Next.js-szel
- [[frontend/env-valtozok-nextjs-ben|Env valtozok Next.js-ben]] — environment kezeles
- [[frontend/landing-page-cms-types|Landing Page CMS types]] — CMS valasztas frontend-ekhez
