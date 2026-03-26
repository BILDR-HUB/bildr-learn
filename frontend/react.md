---
tags: [frontend, react, framework]
datum: 2026-03-26
szint: "🌱 Newcomer"
kapcsolodo:
  - "[[frontend/nextjs|Next.js]]"
  - "[[frontend/react-hooks|React Hooks]]"
  - "[[frontend/react-szintaxis|React szintaxis]]"
  - "[[frontend/react-state-tipusok|React State típusok]]"
  - "[[frontend/react-hook-form|React Hook Form]]"
  - "[[frontend/framer-motion|Framer Motion]]"
  - "[[frontend/react-native-es-expo|React Native és Expo]]"
  - "[[frontend/shadcn-ui|shadcn/ui]]"
  - "[[frontend/tailwind-css|Tailwind CSS]]"
---

## Mi ez?

> [!tldr]
> Komponens-alapú UI könyvtár a Meta-tól. Nem framework - csak a **view layer**. Teljes alkalmazáshoz kell mellé [[frontend/nextjs|Next.js]], Vite vagy hasonló. A web frontend de facto standardja.

## Alapkoncepció

React mentális modellje egyszerű: **UI = f(state)**. Adott egy állapot, abból a React kiszámolja, mit kell renderelni. Ha az állapot változik, a UI automatikusan frissül.

| Fogalom | Lényege |
|---|---|
| **Component** | Újrafelhasználható UI egység (függvény, ami JSX-et ad vissza) |
| **JSX** | HTML-szerű szintaxis JS-ben - a React templating nyelve |
| **Props** | Szülőtől kapott adatok (read-only) |
| **State** | Komponens saját belső állapota (változhat) |
| **Hooks** | Állapot és side-effectek kezelése függvény komponensekben |

A részletekért lásd: [[frontend/react-hooks|React Hooks]], [[frontend/react-szintaxis|React szintaxis]], [[frontend/react-state-tipusok|React State típusok]].

## Ökoszisztéma

| Stack | Mikor használd |
|---|---|
| **Next.js** | Fullstack app, SSR, API routes - ez az alapértelmezett választás |
| **Vite + React** | SPA, belső tool, dashboard - amikor nem kell SSR |
| **[[frontend/react-native-es-expo|React Native + Expo]]** | Mobil app React tudással |

## Framework összehasonlítás

| | React | Vue | Svelte |
|---|---|---|---|
| **Ökoszisztéma** | Legnagyobb | Közepes | Kicsi |
| **Álláspiaci kereslet** | Domináns | Stabil | Növekvő |
| **Tanulási görbe** | Közepes | Alacsony | Alacsony |
| **AI kompatibilitás** | Kiváló (legtöbb training data) | Jó | Jó |

## Mikor válaszd a React-et?

- **Szinte mindig**, ha web appot építesz - ez a legbiztonságosabb választás
- A legtöbb komponens könyvtár ([[frontend/shadcn-ui|shadcn/ui]], Radix) React-re épül
- [[toolbox/claude-code-projekt-setup|Claude Code]] natívan, kontextus nélkül is kiválóan generálja

---

## AI-natív fejlesztés

A React a legjobban támogatott framework az AI coding tool-ok számára - a legtöbb training data React kód, ezért Claude Code, Cursor és Copilot is kiválóan generálják. Ha AI-natív workflow-ban dolgozol, a React a legkisebb súrlódású választás.

> [!tip] Hogyan használd AI-val
> - *"Generálj egy teljes CRUD dashboard-ot React + shadcn/ui + TanStack Query-vel"*
> - *"Refaktoráld ezt a komponenst: bontsd ki a logikát custom hook-ba és a UI-t kisebb komponensekre"*
> - *"Magyarázd el a React renderelési ciklust és hogy miért renderel újra ez a komponens feleslegesen"*

---

## Kapcsolódó

- [[frontend/nextjs|Next.js]] - React-alapú fullstack framework
- [[frontend/react-hooks|React Hooks]] - React hook-ok részletes jegyzet
- [[frontend/react-szintaxis|React szintaxis]] - React szintaxis alapok
- [[frontend/react-state-tipusok|React State típusok]] - state management megközelítések
- [[frontend/react-hook-form|React Hook Form]] - form kezelés React-ben
- [[frontend/framer-motion|Framer Motion]] - deklaratív animációk React-ben
- [[frontend/react-native-es-expo|React Native és Expo]] - React tudással mobil app fejlesztés
- [[frontend/shadcn-ui|shadcn/ui]] - Radix + [[frontend/tailwind-css|Tailwind]] alapú komponensek React-hez
