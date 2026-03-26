---
tags:
  - moc
datum: 2026-03-26
---

# MOC - React

> [!tldr]
> Ez a Map of Content összegyűjti a repo összes React-hez kapcsolódó jegyzetét. Alap szintaxistól a hook-okon át a mobil fejlesztésig.

---

## Alapok

| Jegyzet | Leírás |
|---------|--------|
| [[frontend/react\|React]] | React bevezető — mi ez, JSX, komponensek, Virtual DOM, mikor használd |
| [[frontend/react-szintaxis\|React szintaxis]] | JSX szintaxis — hogyan olvass React kódot, props, conditional rendering, listák |
| [[frontend/react-hooks\|React Hooks]] | Hook koncepció — useState, useEffect, custom hook-ok, szabályok |

## State management

| Jegyzet | Leírás |
|---------|--------|
| [[frontend/react-state-tipusok\|React State típusok]] | State kategóriák — lokális, szerver, URL, form state, és melyikhez melyik eszköz |
| [[frontend/react-adatkezeles\|React adatkezelés]] | Szerver state kezelés — miért fáj a kézi fetch, TanStack Query megoldás |

## Form-ok

| Jegyzet | Leírás |
|---------|--------|
| [[frontend/react-hook-form\|React Hook Form]] | Performáns form kezelés — uncontrolled komponensek, Zod validáció, shadcn/ui integráció |

## Mobil és desktop

| Jegyzet | Leírás |
|---------|--------|
| [[frontend/react-native-es-expo\|React Native és Expo]] | Mobil app fejlesztés React tudással — natív komponensek, Expo toolchain, OTA update |
| [[frontend/electron\|Electron]] | Desktop app web technológiákkal — Chromium + Node.js, cross-platform, React-tel is |

---

## Kapcsolódó note-ok

| Jegyzet | Kapcsolat |
|---------|-----------|
| [[frontend/nextjs\|Next.js]] | React meta-framework — SSR, routing, API routes, production-ready |
| [[frontend/shadcn-ui\|shadcn/ui]] | React komponens gyűjtemény Radix + Tailwind alapon |
| [[frontend/tailwind-css\|Tailwind CSS]] | Utility-first CSS — React-ben a legelterjedtebb styling megoldás |
| [[frontend/framer-motion\|Framer Motion]] | Deklaratív animációk React-hez |
| [[frontend/data-table-filters\|Data Table Filters]] | TanStack Table referencia — szűrhető, rendezhető adattáblák |
| [[frontend/mdx\|MDX]] | Markdown + React komponensek — docs, blog, knowledge base |
| [[frontend/progressive-web-apps\|Progressive Web Apps]] | PWA — web app telepíthető mobilra, offline support |

---

## Tanulási útvonal

Ajánlott sorrend ha nulláról kezded:
1. [[frontend/react\|React]] — mi ez, miért használjuk, alapfogalmak
2. [[frontend/react-szintaxis\|React szintaxis]] — JSX olvasás, props, listák, conditional rendering
3. [[frontend/react-hooks\|React Hooks]] — useState, useEffect, custom hook-ok
4. [[frontend/react-state-tipusok\|React State típusok]] — melyik state típushoz melyik eszköz
5. [[frontend/react-adatkezeles\|React adatkezelés]] — szerver state, TanStack Query
6. [[frontend/react-hook-form\|React Hook Form]] — form-ok kezelése validációval
7. [[frontend/react-native-es-expo\|React Native és Expo]] — ha mobilra is akarsz építeni

> [!tip] Next.js vagy sima React?
> Ha web appot építesz: szinte mindig **Next.js**-t használj, ami React-re épül de SSR-t, routing-ot és API route-okat is ad. Sima React (Vite-tal) csak SPA-khoz vagy library fejlesztéshez kell.

---

## Hézagok

- [ ] React Server Components — RSC minta, szerver és kliens komponensek közötti határ
- [ ] React Context deep dive — mikor Context, mikor Zustand, mikor URL state
- [ ] React performance — memo, useMemo, useCallback, virtualizáció, profiler
- [ ] React testing — component testing Vitest + Testing Library-vel
