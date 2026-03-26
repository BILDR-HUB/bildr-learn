---
tags: [frontend, react, hooks]
datum: 2026-03-26
szint: "🌱 Newcomer"
kapcsolodo:
  - "[[frontend/react-szintaxis|React szintaxis]]"
  - "[[frontend/react-state-tipusok|React State típusok]]"
  - "[[frontend/react-adatkezeles|React adatkezelés]]"
  - "[[frontend/tanstack-query|TanStack Query]]"
---

## Miről szól ez a jegyzet?

A [[frontend/react-szintaxis|React szintaxis]] jegyzet megmutatta **hogyan néz ki** egy hook (`const [x, setX] = useState(0)`). Ez a jegyzet elmagyarázza **mi az a hook koncepcionálisan**, miért létezik, hogyan gondolkodj róluk, és milyen szabályokat kell betartanod.

> [!tldr]
> A hookok a React módja arra, hogy egy komponensnek **memóriát** (state) és **képességeket** (side effect, context, ref) adj - anélkül, hogy class-okat kellene írni. Minden hook egy `use`-szal kezdődő függvény ami "bekapcsol" valamit a React rendszerbe.

---

## Mi az a hook?

A React komponensek **függvények** - meghívódnak, visszaadnak JSX-et, és kész. De egy függvénynek alapból nincs memóriája: ha újra meghívod, mindent elölről kezd.

A **hook** az a mechanizmus, amivel egy függvény-komponens mégis "emlékezhet" dolgokra és "reagálhat" változásokra.

| Kérdés | Hook nélkül | Hook-kal |
|--------|-------------|----------|
| "Emlékszik-e a komponens az előző állapotára?" | Nem - minden renderelés friss | **useState** - megőrzi az értéket renderelések között |
| "Tud-e reagálni változásokra?" | Nem - passzívan renderel | **useEffect** - "csináld ezt, ha ez változik" |
| "Hozzáfér-e a DOM elemhez?" | Nem - JSX absztrakció | **useRef** - közvetlen referencia egy DOM elemre |
| "Hozzáfér-e globális adathoz?" | Nem - csak a props-ot kapja | **useContext** - bármely szülőtől kap adatot |

### Az analógia

Gondolj egy komponensre mint egy **színészre** aki egy jelenetet játszik. A hook-ok a "képességek" amiket a rendező ([[frontend/react|React]]) ad neki:

- **useState** = "emlékezz a szövegre" (memória)
- **useEffect** = "a jelenet végén csinálj valamit" (mellékhatás)
- **useRef** = "itt a kellék, fogd meg" (DOM referencia)
- **useContext** = "olvasd a rendezői utasítást" (globális adat)

---

## Hook-ok és a komponens életciklus

Egy React komponens **három fázison** megy keresztül, és a hook-ok ezekhez a fázisokhoz kapcsolódnak:

```mermaid
graph LR
    MOUNT["Mount - megjelenik"] --> UPDATE["Update - újrarenderel"] --> UNMOUNT["Unmount - eltűnik"]

    style MOUNT fill:#10b981,color:white
    style UPDATE fill:#f59e0b,color:white
    style UNMOUNT fill:#ef4444,color:white
```

| Fázis | Mi történik | Releváns hook |
|-------|------------|---------------|
| **Mount** | A komponens először jelenik meg a képernyőn | `useState` inicializálja a kezdőértéket, `useEffect(() => {...}, [])` egyszer lefut |
| **Update** | Valami változott (state, props) - újrarenderel | `useState` setter triggereli, `useEffect` dependency-k alapján újrafut |
| **Unmount** | A komponens eltűnik (navigáció, feltételes renderelés) | `useEffect` cleanup függvénye fut le |

---

## A fő hook-ok koncepcionálisan

### useState - "emlékező doboz"

A **useState** egy dobozt ad a komponensnek, amibe beletesz egy értéket, és az megmarad renderelések között. Amikor a setter-t hívod (`setCount(5)`), a React **újrarendereli** a komponenst az új értékkel.

**Mentális modell:** Minden `useState` hívás = egy fiók a komponens szekrényében. A React nyilvántartja, melyik fiókban mi van.

| Részlet | Jelentés |
|---------|---------|
| `const [count, setCount]` | `count` = az aktuális érték, `setCount` = a módosító |
| `useState(0)` | 0 a kezdőérték (csak mount-kor számít) |
| `setCount(5)` | "tedd a dobozba az 5-öt és rajzold újra a komponenst" |
| `setCount(prev => prev + 1)` | "az előző érték alapján számold ki az újat" (biztonságosabb) |

> [!warning] useState nem azonnali
> A `setCount(5)` után a `count` még a régi értéket mutatja abban a renderelésben! Az új érték csak a **következő** renderelésben jelenik meg. Ez a leggyakoribb React "gotcha".

### useEffect - "mellékhatás figyelő"

A **useEffect** arra való, hogy a komponens **csináljon valamit** a renderelés mellett: API hívás, timer indítás, DOM manipuláció, subscription. Ezek "mellékhatások" (side effects) - nem a UI rajzolásáról szólnak.

**Mentális modell:** "React, miután kirajzoltad a komponenst, csináld még EZT is. És figyeld EZEKET - ha változnak, csináld újra."

| Dependency array | Jelentés | Mikor fut |
|-----------------|---------|-----------|
| `useEffect(() => {...}, [])` | "Egyszer, mount-kor" | Komponens megjelenik |
| `useEffect(() => {...}, [userId])` | "Ha userId változik" | Mount + minden userId változás |
| `useEffect(() => {...})` | "Minden rendereléskor" | **Általában rossz** - végtelen loop veszély |

**Cleanup:** Ha a useEffect-ből egy függvényt returnolsz, az az "eltakarítás" - unmount-kor vagy a következő effect előtt fut:

| Minta | Példa | Cleanup mire kell |
|-------|-------|-------------------|
| Timer | `setInterval` | `clearInterval` - ne fusson tovább ha a komponens eltűnik |
| Subscription | WebSocket, event listener | `unsubscribe` - ne hallgasson tovább |
| Fetch | API hívás | AbortController - ne állítson state-et egy már eltűnt komponensen |

### useRef - "láthatatlan zseb"

A **useRef** két dolgot csinál:
1. **DOM elemet fog meg** - pl. input-ra fókuszálni
2. **Értéket tárol renderelés nélkül** - az érték megmarad, de a módosítása NEM okoz újrarenderelést (ez a fő különbség a useState-tel)

| | useState | useRef |
|---|---|---|
| **Triggerel renderelést?** | Igen - `setState()` újrarajzolja a komponenst | Nem - `.current` módosítása csendben történik |
| **Mire jó?** | UI-t befolyásoló adat (szám, szöveg, boolean) | DOM referencia, timer ID, előző érték, bármi ami nem UI |

### useContext - "globális üzenőfal"

A **useContext** lehetővé teszi, hogy egy komponens hozzáférjen egy szülő által megosztott adathoz, **anélkül hogy props-on keresztül kellene leadni** minden szinten (ez a "prop drilling" probléma).

Tipikus use case-ek: **téma** (dark/light mode), **auth** (bejelentkezett user), **nyelv** (lokalizáció).

---

## Hook szabályok (Rules of Hooks)

| Szabály | Miért |
|---------|-------|
| **Csak komponens belsejében** hívd | A React csak komponens kontextusban tud hook-okat kezelni |
| **Mindig a komponens elején** hívd | A React a hívási sorrend alapján azonosítja melyik hook melyik - ha if/for blokkban lenne, a sorrend változhatna |
| **Nem lehet feltételes** | `if (x) useState()` - TILOS. A hook-ok száma és sorrendje fix kell legyen |

> [!tip] Egyszerű hüvelykujjszabály
> Minden `use`-szal kezdődő hívás a komponens függvény első szintjén legyen, a return előtt. Soha ne tedd if-be, for-ba, vagy callback-be.

---

## Egyedi hook-ok (Custom Hooks)

Amikor egy **mintát többször használsz** (pl. fetch + loading + error), kiemelheted egy saját hook-ba:

| Beépített hook-ok | Egyedi hook-ok |
|-------------------|----------------|
| useState, useEffect, useRef | useAuth, useFetch, useDebounce |
| A React adja | Te írod (beépítetteket kombinálva) |
| Általános célú | Specifikus logika újrafelhasználása |

Egy egyedi hook = egy `use`-szal kezdődő függvény ami más hook-okat hív. Ez a **React módja a logika újrafelhasználásának** - nem komponenst osztasz meg, hanem viselkedést.

---

## AI-natív fejlesztés

A hook-ok generálása és refaktorálása az AI coding tool-ok egyik legerősebb területe. Claude Code-dal egyedi hook-okat írhatsz a konkrét use case-edre - kérd, hogy írjon useDebounce, useLocalStorage, vagy useMediaQuery hook-ot, és a teljes implementációt (TypeScript típusokkal, cleanup-pal, edge case kezeléssel) megkapod.

> [!tip] Hogyan használd AI-val
> - *"Írj egy useDebounce custom hook-ot TypeScript-ben, generikus típussal és cleanup-pal"*
> - *"Refaktoráld ezt a komponenst: emeld ki a fetch + loading + error logikát egy useFetch custom hook-ba"*
> - *"Magyarázd el mit csinál ez a useEffect és kell-e cleanup függvény hozzá"*

---

## Kapcsolódó

- [[frontend/react-szintaxis|React szintaxis]] - hook-ok szintaxisa (hogyan néz ki a kód)
- [[frontend/react-state-tipusok|React State típusok]] - milyen állapot-típusok vannak és melyikhez milyen hook kell
- [[frontend/react-adatkezeles|React adatkezelés]] - useState + useEffect - fetch minta, és hogyan váltja ki a TanStack Query
- [[frontend/tanstack-query|TanStack Query]] - a useEffect + useState + fetch hármas kiváltása
