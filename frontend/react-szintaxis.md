---
tags: [frontend, react, szintaxis, jsx]
datum: 2026-03-26
szint: "🌱 Newcomer"
kapcsolodo:
  - "[[frontend/react|React]]"
  - "[[frontend/react-hooks|React Hooks]]"
  - "[[frontend/react-state-tipusok|React State típusok]]"
  - "[[frontend/react-adatkezeles|React adatkezelés]]"
  - "[[frontend/css-vs-nextjs-vs-react|CSS vs Next.js vs React]]"
---

## Miről szól ez a jegyzet?

A [[frontend/react|React]] nem egy új nyelv - JavaScript/[[foundations/typescript-vs-python|TypeScript]] kód, ami HTML-szerű szintaxist (JSX) használ a felhasználói felület leírására. Ez a jegyzet arról szól, hogyan olvass React kódot.

> [!tldr]
> A React nem egy új nyelv - JavaScript/TypeScript kód, ami HTML-szerű szintaxist (JSX) használ a felhasználói felület leírására. Minden React kód JS/TS tudással olvasható, ha ismered a JSX-et, a komponenseket és a hookokat.

---

## JSX - "HTML a JavaScript-ben"

A **JSX** a React legszembetűnőbb jellemzője. Úgy néz ki mint HTML, de valójában JavaScript:

```tsx
// Ez JSX - HTML-szerű szintaxis a JS-ben
function Udvozles() {
  const nev = "Világ"
  return (
    <div className="card">
      <h1>Szia, {nev}!</h1>
      <p>Ma {new Date().toLocaleDateString()} van.</p>
    </div>
  )
}
```

### JSX szabályok

| JSX | HTML | Miért különbözik |
|-----|------|-----------------|
| `className` | `class` | `class` foglalt szó JS-ben |
| `htmlFor` | `for` | `for` foglalt szó JS-ben |
| `onClick` | `onclick` | camelCase a standard JSX-ben |
| `style={{ color: 'red' }}` | `style="color: red"` | Objektum, nem string |
| `{változó}` | nincs | JS kifejezés beágyazás |
| Mindig egy gyökér elem | mindegy | React megköveteli |

### JS kifejezések JSX-ben

A `{ }` kapcsos zárójelek között **bármilyen JS kifejezés** állhat:

```tsx
return (
  <div>
    {/* Ez egy komment JSX-ben */}
    <p>{2 + 2}</p>                        {/* → 4 */}
    <p>{nev.toUpperCase()}</p>             {/* → "VILÁG" */}
    <p>{aktiv ? "igen" : "nem"}</p>        {/* → ternary operátor */}
    <p>{felhasznalok.length} fő</p>        {/* → "5 fő" */}
  </div>
)
```

> [!tip] Felismerés
> Ha `<div>`, `<h1>`, `<p>` stb. tageket látsz **JS fájlban** - az JSX (React kód). A `{ }` kapcsos zárójelben JS kifejezés van - változó, számítás, függvényhívás, bármi.

---

## Komponensek - a LEGO kockák

A React lényege: az UI-t **komponensekre** bontod. Minden komponens egy függvény, ami JSX-et ad vissza:

```tsx
// ↓ Komponens = nagybetűvel kezdődő függvény ami JSX-et return-öl
function InvoiceCard({ vendor, amount, isPaid }) {
//                    ↑ destructured props (bemeneti adatok)
  return (
    <div className={isPaid ? "card paid" : "card"}>
      <h3>{vendor}</h3>
      <p>{amount.toLocaleString()} Ft</p>
      {isPaid && <span>Fizetve</span>}
    </div>
  )
}

// Használat - mint egy HTML tag
<InvoiceCard vendor="Példa Kft." amount={50000} isPaid={true} />
```

### Komponens felismerése

| Amit látsz | Jelentés |
|-----------|---------|
| `function NagyBetus() { return <...> }` | Komponens definíció |
| `const NagyBetus = () => { return <...> }` | Komponens definíció (arrow function) |
| `<NagyBetus />` | Komponens használata (renderelés) |
| `export default function Page()` | Fő komponens exportálása |

> [!warning] Fontos szabály
> Komponens név **MINDIG nagybetűvel** kezdődik: `InvoiceCard`, `UserProfile`, `App`. A kisbetűs nevek (`div`, `span`, `h1`) natív HTML elemek.

---

## Props - adatok átadása komponensnek

A **props** (properties) = bemeneti adatok, amiket a szülő komponens ad a gyereknek:

```tsx
// Szülő → gyerek felé adja az adatot
function App() {
  return (
    <UserCard nev="Anna" kor={28} aktiv={true} />
//            ↑ prop       ↑ szám    ↑ boolean
//   string-et " "-ben, minden mást { }-ben adod át
  )
}

// Gyerek komponens - props-ból olvassa
function UserCard({ nev, kor, aktiv }) {
//                 ↑ destructuring a props-ból
  return <div>{nev} ({kor})</div>
}

// VAGY props objektumként
function UserCard(props) {
  return <div>{props.nev} ({props.kor})</div>
}
```

### children - speciális prop

```tsx
function Card({ children, title }) {
  return (
    <div className="card">
      <h2>{title}</h2>
      {children}          {/* ← ami a <Card> és </Card> KÖZÖTT van */}
    </div>
  )
}

// Használat
<Card title="Profil">
  <p>Ez a tartalom a children-be kerül</p>
  <button>Kattints</button>
</Card>
```

> [!tip] Felismerés
> Ha `{ children }` van a props-ban - ez a komponens "befogadó" - ami a nyitó és záró tag között van, az kerül a `children` helyére. Mint egy keret/wrapper.

---

## Hookok - React "varázslatok"

A **hookok** (hooks) speciális React függvények, amik `use`-szal kezdődnek. Állapotot és mellékhatásokat kezelnek.

### useState - állapot kezelés

```tsx
import { useState } from 'react'

function Counter() {
  const [count, setCount] = useState(0)
//       ↑ érték  ↑ beállító    ↑ kezdőérték
//  Ez tömb destructuring! useState() egy [érték, setter] tömböt ad vissza.

  return (
    <div>
      <p>Kattintások: {count}</p>
      <button onClick={() => setCount(count + 1)}>
{/*                    ↑ klikkeléskor setCount-ot hívja */}
        Kattints
      </button>
    </div>
  )
}
```

**Hogyan olvasd:** `const [X, setX] = useState(kezdőérték)` - "X az aktuális érték, setX-szel módosítom, kezdéskor `kezdőérték`".

### useEffect - mellékhatás (side effect)

```tsx
import { useEffect } from 'react'

function UserProfile({ userId }) {
  const [user, setUser] = useState(null)

  useEffect(() => {
//           ↑ ez a függvény fut le
    async function betolt() {
      const res = await fetch(`/api/users/${userId}`)
      const data = await res.json()
      setUser(data)
    }
    betolt()
  }, [userId])
//    ↑ DEPENDENCY ARRAY - "futtasd újra HA userId változik"
//    [] üres tömb = "csak egyszer futtasd, mount-kor"
//    nincs tömb = "minden rendereléskor futtasd" (általában rossz)

  if (!user) return <p>Betöltés...</p>
  return <h1>{user.nev}</h1>
}
```

**Hogyan olvasd:** `useEffect(() => { ... }, [deps])` - "csináld EZT, amikor a DEPS változik".

### Más gyakori hookok

| Hook | Mire jó | Minta |
|------|---------|-------|
| `useState` | Állapot (adat ami változhat) | `const [x, setX] = useState(0)` |
| `useEffect` | Mellékhatás (API hívás, timer) | `useEffect(() => { fetch... }, [])` |
| `useRef` | DOM elem referencia / nem-renderelő adat | `const ref = useRef(null)` |
| `useMemo` | Számítás cache-elés (teljesítmény) | `const x = useMemo(() => drága(), [deps])` |
| `useCallback` | Függvény cache-elés | `const fn = useCallback(() => {}, [deps])` |
| `useContext` | Globális adat (theme, auth, nyelv) | `const theme = useContext(ThemeContext)` |

> [!tip] Felismerés
> Bármilyen `use`-szal kezdődő függvény - React hook. A hookok **csak komponensek belsejében** hívhatók, és **mindig a komponens elején** kell lenniük (nem if/for blokkban).

---

## Feltételes renderelés - "mutasd vagy ne"

```tsx
function Dashboard({ user, isLoading }) {
  // 1. Korai return - ha tölt, nem kell a többi
  if (isLoading) return <p>Betöltés...</p>
  if (!user) return <p>Nincs bejelentkezve</p>

  return (
    <div>
      {/* 2. Ternary - egyik vagy másik */}
      {user.admin ? <AdminPanel /> : <UserPanel />}

      {/* 3. && operátor - mutasd VAGY semmit */}
      {user.premium && <PremiumBadge />}
{/*   ↑ ha igaz → rendereli a jobb oldalt
      ↑ ha hamis → nem renderel semmit */}
    </div>
  )
}
```

| Minta | Jelentés | Mikor |
|-------|---------|-------|
| `if (...) return <X />` | Korai visszatérés | Betöltés, hiba, nincs adat |
| `{felt ? <A /> : <B />}` | Egyik vagy másik | Két állapot közti váltás |
| `{felt && <A />}` | Megjelenik vagy nem | Opcionális elem |

---

## Lista renderelés - .map()

A listák megjelenítése `.map()`-pel történik:

```tsx
function UserList({ users }) {
  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>
{/*         ↑ key KÖTELEZŐ - egyedi azonosító a React-nek */}
          {user.nev} - {user.email}
        </li>
      ))}
    </ul>
  )
}
```

> [!warning] A `key` prop
> `.map()`-ben a legkülső elemnek **mindig kell `key`** prop egyedi értékkel (általában `id`). Ez nem jelenik meg a felületen - a React belső optimalizálásához kell. Ha hiányzik - warning a konzolon.

---

## Event handlerek - felhasználói interakció

```tsx
function Form() {
  const [nev, setNev] = useState("")

  // Event handler függvény
  const handleSubmit = (e) => {
    e.preventDefault()          // ← megakadályozza az oldal újratöltést
    console.log("Beküldve:", nev)
  }

  return (
    <form onSubmit={handleSubmit}>
{/*        ↑ az event neve camelCase-ben */}
      <input
        value={nev}
        onChange={(e) => setNev(e.target.value)}
{/*     ↑ minden karakter leütésekor frissít
        ↑ e.target.value = az input jelenlegi szövege */}
      />
      <button type="submit">Küldés</button>
    </form>
  )
}
```

| JSX event | Mikor fut | HTML megfelelő |
|-----------|----------|---------------|
| `onClick` | Kattintáskor | `onclick` |
| `onChange` | Input értéke változik | `onchange` |
| `onSubmit` | Form beküldésekor | `onsubmit` |
| `onKeyDown` | Billentyű leütésekor | `onkeydown` |
| `onMouseEnter` | Egér ráhúzásakor | `onmouseenter` |

---

## Fájl struktúra - tipikus React projekt

```
src/
├── components/           ← újrahasználható UI elemek
│   ├── Button.tsx
│   ├── Card.tsx
│   └── UserCard.tsx
├── pages/                ← oldalak (React Router) vagy app/ (Next.js)
│   ├── Dashboard.tsx
│   └── Login.tsx
├── hooks/                ← egyedi hookok
│   └── useAuth.ts
├── lib/                  ← utility függvények, API kliens
│   └── api.ts
├── App.tsx               ← fő komponens
└── main.tsx              ← belépési pont
```

> [!tip] Fájlnév konvenció
> - `NagyBetus.tsx` - komponens fájl (React komponenst exportál)
> - `kissBetus.ts` - utility, hook, config (nem komponens)
> - `.tsx` - tartalmaz JSX-et (React)
> - `.ts` - csak TypeScript, nincs JSX

---

## Összefoglaló - React kód olvasási checklist

Ha React kódra nézel, keresd ezeket:

```
1. IMPORT sor → mit használ ez a fájl?
   import { useState, useEffect } from 'react'
   import { Button } from './components/Button'

2. KOMPONENS → nagybetűs függvény, JSX-et return-öl
   function Dashboard({ user }) { return <div>...</div> }

3. HOOKS → use-szal kezdődő hívások a komponens elején
   const [data, setData] = useState(null)
   useEffect(() => { ... }, [])

4. JSX → HTML-szerű szintaxis { } kiértékelésekkel
   <div>{data && <p>{data.name}</p>}</div>

5. EVENT HANDLER → onClick, onChange, onSubmit
   <button onClick={() => setCount(c + 1)}>

6. LISTA → .map() + key prop
   {items.map(item => <li key={item.id}>{item.name}</li>)}
```

---

## AI-natív fejlesztés

A React szintaxis megtanulása gyorsabb AI-val - kérd Claude Code-ot, hogy magyarázza el a kódrészleteket, vagy generáljon példa komponenseket a tanulásodhoz. Az AI különösen jól tudja bemutatni a JSX, props, hooks és feltételes renderelés mintákat valós kontextusban.

> [!tip] Hogyan használd AI-val
> - *"Magyarázd el lépésről lépésre mit csinál ez a React komponens - minden sort kommentezz"*
> - *"Írj egy példa komponenst ami useState-et, useEffect-et és feltételes renderelést használ - kommentekkel"*
> - *"Konvertáld ezt a HTML oldalt React komponensekre - bontsd szét logikus részekre"*

---

## Kapcsolódó

- [[frontend/react|React]] - React áttekintés
- [[frontend/react-hooks|React Hooks]] - hook-ok koncepcionálisan (miért léteznek, életciklus, szabályok)
- [[frontend/react-state-tipusok|React State típusok]] - lokális vs szerver vs globális state
- [[frontend/react-adatkezeles|React adatkezelés]] - fetch + useState + useEffect minta és a TanStack Query megoldás
- [[frontend/css-vs-nextjs-vs-react|CSS vs Next.js vs React]] - melyik réteg mit csinál
