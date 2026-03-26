---
tags: [frontend, tanstack-query, react, state-management]
datum: 2026-03-26
szint: "🧱 Scout"
kapcsolodo:
  - "[[frontend/react-adatkezeles|React adatkezelés]]"
  - "[[frontend/react-state-tipusok|React State típusok]]"
  - "[[frontend/react-hooks|React Hooks]]"
  - "[[frontend/nextjs|Next.js]]"
---

## Mi ez és mire jó?

A **TanStack Query** egy **szerver-állapot kezelő** (server state manager) [[frontend/react|React]]-hez (és Vue, Solid, Angular-hoz is). A lényege: **nem te kezeled kézzel** a fetch hívásokat, loading state-et, error state-et, cache-t - a TanStack Query mindezt automatizálja.

> [!tldr] Egy mondatban
> A TanStack Query a "fetch + useState + useEffect" hármast cseréli le egyetlen `useQuery()` hívásra - plusz cache-elést, automatikus újratöltést, és invalidációt ad hozzá ingyen.

### A probléma amit megold

Kézi fetch-elésnél minden API híváshoz 3 dolgot kell kezelned: loading, error, data. Ezt minden komponensben újra kell írni, és a cache-elést, újratöltést, invalidációt is neked kell megoldani.

| Kézi fetch | TanStack Query |
|------------|----------------|
| `useState` + `useEffect` + `fetch` | `useQuery()` - egy sor |
| Loading state manuálisan | `isLoading` automatikusan |
| Error handling manuálisan | `error` automatikusan |
| Cache? Magadnak kell írni | Beépített, intelligens cache |
| Újratöltés? `setInterval` | `refetchInterval`, `refetchOnWindowFocus` |
| Más komponens is kéri ugyanazt? Újra fetch | Deduplikáció - egy fetch, mindkettő kap adatot |
| Mutation után frissítés? Manuális | `invalidateQueries()` - egy sor |

---

## Miért jó AI kódoláshoz?

> [!tip] Az AI kódolás szempontjából a TanStack Query az egyik legértékesebb frontend library
> Az AI ([[toolbox/claude-code-projekt-setup|Claude Code]] is) a TanStack Query-t **nagyon jól generálja**, és a kódot olvashatóbbá, karbantarthatóbbá teszi - ami az AI-val való iterálás során kritikus.

### 1. Deklaratív, nem imperatív

A TanStack Query **deklaratív** - leírod *mit* akarsz (milyen adatot, milyen cache kulccsal), nem *hogyan* (fetch, then, catch, setState). Az AI ezt a mintát konzisztensen generálja, mert a struktúra mindig ugyanaz:

| Minta | Minden query | Minden mutation |
|-------|-------------|-----------------|
| Struktúra | `queryKey` + `queryFn` | `mutationFn` + `onSuccess` - `invalidateQueries` |

### 2. Prompt-barát API

Amikor AI-val dolgozol, a TanStack Query szinte "önleíró" promptot ad:
- *"Adj hozzá egy useQuery-t ami lekéri a felhasználók listáját, és egy useMutation-t ami új felhasználót hoz létre - sikeres mutation után invalidálja a query-t"*
- Az AI pontosan tudja mit csináljon, mert a library API-ja egy-az-egyben megfelel a természetes nyelvű leírásnak

### 3. Kevesebb state management kód = kevesebb bug

Az AI által generált kódban a leggyakoribb hibák a **state szinkronizációs** problémák (loading flag elfelejtve, stale adat, race condition). A TanStack Query ezeket **kiküszöböli** - nincs mit elrontani, mert a library kezeli.

### 4. tRPC integráció - teljes típusbiztonság

A tRPC + TanStack Query kombináció azt jelenti, hogy az AI a **backend és frontend közötti adatfolyamot end-to-end típusbiztosan** generálja. Ha a backend változik, a frontend fordítási időben jelez - nem futásidőben törik el.

---

## Szerver state vs kliens state

> [!info] Fontos mentális modell
> A TanStack Query **szerver state**-et kezel - adatot ami a szerveren él, és amit le kell kérni. A **kliens state** (form értékek, UI toggle-ök, modal nyitva/zárva) továbbra is `useState`-ben marad.

| | Szerver state | Kliens state |
|---|---|---|
| **Hol él** | Szerveren / DB-ben | A böngészőben |
| **Példák** | Felhasználók listája, rekordok, eredmények | Form input értékek, sidebar nyitva/zárva |
| **Kezelés** | TanStack Query (`useQuery`, `useMutation`) | React `useState` |
| **Cache kell?** | Igen - ugyanazt az adatot többször kéri | Nem - lokális, eldobható |

---

## Alapfogalmak

| Fogalom | Mit csinál | Mikor |
|---------|-----------|-------|
| **queryKey** | Cache azonosító - egyedi kulcs ami azonosítja az adatot | `['users']`, `['user', userId]` |
| **queryFn** | A tényleges fetch hívás - ami lekéri az adatot | `() => fetch('/api/users')` |
| **staleTime** | Mennyi ideig "friss" a cache - nem kér újra | Alapból 0 (mindig stale) |
| **gcTime** | Mennyi ideig marad a cache memóriában | Alapból 5 perc |
| **invalidateQueries** | Cache érvénytelenítés - újra lekéri az adatot | Mutation után |
| **refetchOnWindowFocus** | Ablak fókuszra automatikus újratöltés | Alapból bekapcsolva |
| **enabled** | Feltételes query - csak ha igaz, fut le | `enabled: !!userId` |

---

## Gyakori minták

### 1. Query + Mutation + Invalidáció

A leggyakoribb minta: lekéred a listát (`useQuery`), mutálsz (`useMutation`), majd invalidálsz (a lista automatikusan frissül).

### 2. Dependent query (feltételes lekérés)

Amikor egy query egy másik query eredményétől függ - pl. először lekéred a user-t, aztán a user adatai alapján a kapcsolódó rekordokat. Az `enabled` paraméterrel kezelhető.

### 3. Optimistic update

Mutation előtt **már frissíted a UI-t** (optimista), és ha a szerver hiba, visszaállítod. Dashboard-oknál, todo app-oknál jó UX.

### 4. Polling (refetch interval)

Ha egy adat folyamatosan változik (pl. státusz, dashboard metrikák), a `refetchInterval` automatikusan újratölti.

---

## Mikor használd (és mikor NE)?

| Szituáció | TanStack Query? | Alternatíva |
|-----------|----------------|-------------|
| SPA/kliens komponens + API hívások | **Igen** - a fő use case | - |
| [[frontend/nextjs|Next.js]] Server Components (RSC) | **Nem** - RSC maga fetch-el | RSC `async` components |
| Next.js kliens komponensek | **Igen** - ha RSC nem elég | - |
| Form state (input értékek) | **Nem** - ez kliens state | `useState`, react-hook-form |
| Egyszerű, egyszeri fetch | Overkill | Sima `fetch` + `useState` |
| tRPC-vel együtt | **Igen** - `@trpc/react-query` | - |

> [!warning] Next.js RSC vs TanStack Query
> Ha Next.js App Router-t használsz, a **Server Components** már beépítetten kezelik a szerver state-et (async fetch közvetlenül a komponensben). TanStack Query csak a **kliens komponensekhez** kell (`'use client'`). Ne használd feleslegesen mindkettőt ugyanarra az adatra.

---

## Buktatók

- **staleTime: 0 (alapértelmezett)** - ez azt jelenti, hogy a cache azonnal "stale" (elavult), és minden rendereléskor újra fetch-el. Ha az adat ritkán változik, állítsd magasabbra (pl. `staleTime: 60_000` = 1 perc)
- **queryKey konzisztencia** - ha két komponensben más queryKey-t adsz ugyanannak az adatnak, nem fognak közös cache-t használni. Centralizáld a queryKey-eket
- **Mutation után mindig invalidálj** - ha elfelejtesz `invalidateQueries`-t hívni, a UI stale adatot mutat
- **Ne tedd a queryClient-et komponensbe** - a `QueryClient` legyen a legfelső szinten (provider), ne hozz létre újat rendereléskor

---

## Hasznos linkek

- **Docs:** https://tanstack.com/query/latest/docs
- **GitHub:** https://github.com/TanStack/query
- **DevTools:** `@tanstack/react-query-devtools` - vizuális debug panel a böngészőben

---

## Kapcsolódó

- [[frontend/react-adatkezeles|React adatkezelés]] - a fetch + useState + useEffect minta, amit a TanStack Query kivált
- [[frontend/react-state-tipusok|React State típusok]] - szerver state vs lokális state - miért kell külön eszköz
- [[frontend/react-hooks|React Hooks]] - useState, useEffect koncepcionálisan
- [[frontend/nextjs|Next.js]] - Server Components vs kliens-oldali TanStack Query
