---
tags: [frontend, react, spa, preact, osszehasonlitas]
datum: 2026-03-03
szint: "🧱 Brick"
kapcsolodo:
  - "[[frontend/css-vs-nextjs-vs-react|CSS vs Next.js vs React]]"
  - "[[frontend/nextjs|Next.js]]"
  - "[[backend/hono|Hono]]"
  - "[[cloud/cloudflare|Cloudflare]]"
  - "[[backend/edge-function|Edge function]]"
  - "[[frontend/bun-nextjs-projekt-setup|Bun - Next.js projekt setup]]"
---

## A lényeg 30 masodpercben

Ez a harom fogalom **nem harom különböző technologia** — ket library + egy alkalmazás minta:

| Fogalom | Mi ez | Analogia |
|---------|-------|----------|
| **React** | UI library (felhasználói felületet építesz vele) | A LEGO kockak |
| **SPA** | Alkalmazás minta (hogyan használod a React-et) | Hogyan rakod ossze a LEGO-t |
| **Preact** | Könnyű React klon (ugyanaz, de kisebb) | Ugyanaz a LEGO, de mini változatban |

```mermaid
graph TD
    REACT[React<br/>UI library<br/>~40KB] --> SPA[React SPA<br/>Single Page Application<br/>kliens-oldali app]
    REACT --> SSR[React SSR<br/>Server-Side Rendering<br/>pl. Next.js]
    PREACT[Preact<br/>React klon<br/>~3KB] --> SPA2[Preact SPA<br/>ugyanaz, csak kisebb]

    style REACT fill:#61dafb,color:black
    style PREACT fill:#673ab8,color:white
    style SPA fill:#333,color:white
    style SSR fill:#000,color:white
    style SPA2 fill:#333,color:white
```

---

## React — a UI library

A **React** egy JavaScript library (nem framework!) amivel **felhasználói felületet** építesz. Komponenseket irsz, amik HTML-t generalnak:

```tsx
// Egy React komponens — ez EGY "kocka" a feluleten
function InvoiceCard({ vendor, amount, dueDate }) {
  return (
    <div className="card">
      <h3>{vendor}</h3>
      <p>{amount} Ft</p>
      <p>Hatarido: {dueDate}</p>
    </div>
  )
}
```

A React **onmagaban nem egy app** — csak a UI reteget adja. Ahhoz hogy egy teljes alkalmazás legyen, döntened kell:

- **Hogyan delivereled a felhasználónak?** → Ez a SPA vs SSR kerdes
- **Hogyan kezeled a routingot?** → React Router, vagy Next.js file-based routing
- **Hogyan kezeled az adatokat?** → Fetch API, TanStack Query, stb.

---

## SPA (Single Page Application) — egy alkalmazás minta

Az **SPA** nem egy technologia, hanem egy **minta (pattern)** — hogyan működik az app a bongeszőben.

### Hogyan működik egy hagyomanyos weboldal?

```mermaid
graph LR
    subgraph "Hagyomanyos (multi-page)"
        U1[Klikk: /dashboard] -->|keres a szerverre| S1[Szerver]
        S1 -->|teljes HTML oldal| U1
        U2[Klikk: /invoices] -->|keres a szerverre| S1
        S1 -->|teljes HTML oldal| U2
    end
```

Minden kattintasnal **új oldal toltodik be a szerverrol** → feher villanas, lassabb.

### Hogyan működik az SPA?

```mermaid
graph LR
    subgraph "SPA (Single Page)"
        LOAD[Elso betoltes] -->|letolti az EGESZ appot| APP[React app<br/>bongeszőben fut]
        APP -->|klikk: /dashboard| APP
        APP -->|klikk: /invoices| APP
        APP -.->|csak adat keres API-bol| API[Backend API]
    end
```

Az SPA **egyetlen HTML oldalt** tolt be, és utána **minden a bongeszőben tortenik**:

- Navigacio? → A React atrajzolja a felületet, nincs ujratoltes
- Adat kell? → API hivas a háttérben (fetch), a szerver csak JSON-t kuld
- Eredmeny: **gyors, gordulekeny, app-szeru elmeny** (mint egy mobil app)

### SPA vs SSR (Next.js)

| Szempont | SPA (React + Vite) | SSR ([[frontend/nextjs|Next.js]]) |
|----------|-------------------|-----------------|
| **Hol renderel** | Bongeszőben (kliens) | Szerveren, majd bongeszőben |
| **Első betoltes** | Lassabb (le kell tolteni az egész appot) | Gyorsabb (kesz HTML jon) |
| **SEO** | Rossz (Google nem látja a tartalmat) | Kivalo (szerver renderelt HTML) |
| **Navigacio** | Gyors (nincs ujratoltes) | Gyors (prefetch) |
| **Komplexitas** | Egyszerűbb | Bonyolultabb |
| **Backend** | Kulon kell (pl. [[backend/hono|Hono]]) | Beepitve (API Routes) |
| **Mire jó** | Belső tool, admin, dashboard | Publikus site, SEO, marketing |

> [!tip] Okolszabaly
> **Belső tool:** SPA eleg — senki nem Google-ozi a szamlakat
> **Publikus oldal:** [[frontend/nextjs|Next.js]] SSR — SEO fontos, Google indexeles kell

### React SPA setup (Vite)

```bash
# React SPA letrehozas Vite-tel
npm create vite@latest my-app -- --template react-ts

# Ez lesz az eredmeny:
# my-app/
# ├── src/
# │   ├── App.tsx        ← fo komponens
# │   ├── main.tsx       ← belepesi pont
# │   └── ...
# ├── index.html         ← az EGYETLEN HTML fajl
# └── vite.config.ts
```

A build eredmenye: **statikus fájlok** (HTML + JS + CSS), amiket bármelyik hosting kiszolgálhat — pl. [[cloud/cloudflare|Cloudflare]] Pages.

---

## Preact — a mini React

A **Preact** a React **könnyűsulyu klonja**. Ugyanazt az API-t adja (ugyanúgy irsz komponenseket), de **~3KB** vs React **~40KB**.

| Szempont | React | Preact |
|----------|-------|--------|
| **Meret** | ~40KB (gzipped) | ~3KB (gzipped) |
| **API** | Eredeti | 99% kompatibilis |
| **Sebesseg** | Gyors | Kicsit gyorsabb |
| **Okoszisztema** | Hatalmas (shadcn, react-query, stb.) | Kisebb, de React lib-ek többsege fut |
| **Fejleszto** | Meta (Facebook) | Jason Miller (egyeni) |
| **Mikor valaszd** | Alapértelmezett választas | Ha a bundle meret kritikus (edge, mobil) |

```tsx
// Preact — UGYANUGY nez ki mint a React
import { useState } from 'preact/hooks'

function Counter() {
  const [count, setCount] = useState(0)
  return <button onClick={() => setCount(count + 1)}>{count}</button>
}
```

> [!info] Mikor érdemes Preact-et használni?
> - **Edge deploy** (pl. [[cloud/cloudflare|Cloudflare]] Workers) — ahol a bundle meret szamit
> - **Mobil-first app** — ahol a felhasználók lassu hálózaton vannak
> - **Ha nem kell a React teljes okoszisztema** — nincs shadcn/ui, nincs React-specifikus library
>
> **Mikor NE használd:**
> - Ha shadcn/ui, Radix, React Query, stb. kell → maradj React-nel
> - Ha a csapat React-et ismer → ne bonyolitsd Preact-tel
> - A 37KB különbség a legtobb appnal **nem szamit** (gyors neten ez ~10ms)

---

## Melyiket valaszd? (döntési fa)

```mermaid
graph TD
    START[Frontendet akarok<br/>epiteni] --> Q1{Publikus oldal?<br/>SEO kell?}
    Q1 -->|Igen| NEXT["Next.js<br/>(SSR)"]
    Q1 -->|Nem, belso tool| Q2{Bundle meret<br/>kritikus?}
    Q2 -->|Nem| REACT_SPA[React SPA<br/>Vite + React]
    Q2 -->|Igen, edge deploy| PREACT_SPA[Preact SPA<br/>Vite + Preact]

    style NEXT fill:#000,color:white
    style REACT_SPA fill:#61dafb,color:black
    style PREACT_SPA fill:#673ab8,color:white
```

Belső tool projektekhez: **React SPA** a legjobb választas — belső tool, nincs SEO igény, a React okoszisztema (shadcn/ui, react-query) hasznos, és a 40KB meret [[cloud/cloudflare|Cloudflare]] Pages-en nem probléma.

---

## Kapcsolodo

- [[frontend/css-vs-nextjs-vs-react|CSS vs Next.js vs React]] — CSS, React, Next.js szintek kozotti különbség
- [[frontend/nextjs|Next.js]] — fullstack framework (SSR + React)
- [[backend/hono|Hono]] — backend API a React SPA melle
- [[cloud/cloudflare|Cloudflare]] — SPA hosting (Pages) + API hosting (Workers)
- [[backend/edge-function|Edge function]] — miért szamit a meret edge-en
- [[frontend/bun-nextjs-projekt-setup|Bun - Next.js projekt setup]] — ha megis Next.js-t választasz
