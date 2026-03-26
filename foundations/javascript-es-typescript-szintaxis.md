---
tags:
  - nyelv
  - szintaxis
  - javascript
  - typescript
datum: 2026-03-26
szint: "🌱 Newcomer"
kapcsolodo:
  - "[[foundations/szintaxis-alapok|Szintaxis alapok]]"
  - "[[foundations/python-szintaxis|Python szintaxis]]"
  - "[[foundations/typescript-vs-python|TypeScript vs Python]]"
---

# JavaScript és TypeScript szintaxis

## Miről szól ez a note?

A [[foundations/szintaxis-alapok|Szintaxis alapok]] note-ban a közös fogalmakat tanultad meg. Ez a note a **JavaScript és TypeScript specifikus szintaxis** - együtt tárgyaljuk őket, mert a TypeScript = JavaScript + típusok.

> [!tldr]
> **JavaScript (JS)** a webfejlesztés nyelve - a böngésző és a Node.js/Bun/Deno ezt érti. **TypeScript (TS)** = JS + típusrendszer. Minden TS kód JS is, de fordítani (compile) kell. A szintaxis 90%-a ugyanaz.

---

## JS felismerése - honnan tudod hogy JS/TS?

Ha ezeket látod - JS vagy TS:
- **`const` / `let`** változóknál
- **`function` vagy `=>`** (nyíl) függvényeknél
- **`{ }` kapcsos zárójelek** blokkoknál
- **`//`** kommentek (nem `#`)
- **`true` / `false` / `null` / `undefined`** kisbetűkkel
- **`===`** háromszoros egyenlőség
- **`console.log()`** kiíratás

Ha ezen felül **típusokat** is látsz (`: string`, `interface`, `type`) - az TypeScript.

---

## Változók és a const/let/var hármas

```typescript
const NEV = "Anna"      // <- const: nem módosítható (konstans)
let kor = 28            // <- let: módosítható
var regi = "ne használd" // <- var: régi stílus, scope problémák - kerüld
```

| Kulcsszó | Módosítható? | Scope | Megjegyzés |
|----------|-------------|-------|-----------|
| `const` | Nem | Blokk | Használd ezt alapból |
| `let` | Igen | Blokk | Ha tényleg módosítani kell |
| `var` | Igen | Függvény | Régi kódban látod, ne írd |

> [!tip] Ökölszabály
> **Mindig `const`-ot használj**, kivétel ha tényleg meg kell változtatnod az értéket - akkor `let`. A `var`-t ne használd.

---

## Arrow function - a nyíl függvény

A modern JS/TS-ben a legtöbb függvényt **arrow function**-ként (nyíl függvényként) írják:

```typescript
// Klasszikus
function osszead(a, b) {
  return a + b
}

// Arrow - teljes
const osszead = (a, b) => {
  return a + b
}

// Arrow - rövid (ha egy sor, nincs { } és nincs return)
const osszead = (a, b) => a + b

// Arrow - egy paraméter (nincs zárójel sem)
const duplaz = x => x * 2

// Arrow - nulla paraméter
const random = () => Math.random()
```

> [!tip] A `=>` olvasása
> A `=>` nyilat olvasd úgy: "és akkor". `(a, b) => a + b` = "a és b **és akkor** a + b". Ez nem varázs-szintaxis - csak egy rövidebb módja a `function` kulcsszónak.

---

## Destructuring - szétszedés

Az egyik leggyakoribb JS/TS minta. "Kicsomagolás" egy objektumból vagy tömbből:

### Objektum destructuring

```typescript
const felhasznalo = { nev: "Anna", kor: 28, email: "a@example.com" }

// Hagyományos
const nev = felhasznalo.nev
const kor = felhasznalo.kor

// Destructuring - UGYANAZ, rövidebben
const { nev, kor } = felhasznalo
//      ^     ^       <- "szedd ki a nev-et és kor-t a felhasznalo-ból"

// Átnevezéssel
const { nev: felhasznaloNev, kor: felhasznaloKor } = felhasznalo
//           ^ új név                ^ új név
```

### Tömb destructuring

```typescript
const szamok = [1, 2, 3]

const [elso, masodik] = szamok
// elso = 1, masodik = 2

// React-ben NAGYON gyakori:
const [count, setCount] = useState(0)
//     ^ érték  ^ beállító függvény
```

### Paraméter destructuring

```typescript
// Függvény ami objektumot kap, és rögtön szétszedi
function udvozol({ nev, kor }) {
  return `Szia ${nev}, te ${kor} éves vagy`
}

// Hívás:
udvozol({ nev: "Anna", kor: 28 })
```

> [!tip] Felismerés
> Ha `{ }` vagy `[ ]` van az `=` **bal** oldalán - az destructuring. Nem új objektumot/tömböt hoz létre, hanem egy meglévőt "csomagol ki".

---

## Spread és Rest operator (`...`)

A három pont (`...`) két dolgot jelent, kontextustól függően:

### Spread - "szétszórás"

```typescript
// Tömb másolás + bővítés
const gyumolcsok = ["alma", "korte"]
const ujLista = [...gyumolcsok, "szilva"]
// -> ["alma", "korte", "szilva"]

// Objektum másolás + felülírás
const user = { nev: "Anna", kor: 28 }
const frissitett = { ...user, kor: 29 }
// -> { nev: "Anna", kor: 29 }
```

### Rest - "a maradék"

```typescript
// Függvény paraméterben
function log(elso, ...tobbi) {
  console.log(elso)   // -> "hello"
  console.log(tobbi)  // -> ["vilag", "!"]
}
log("hello", "vilag", "!")

// Destructuring-ban
const { nev, ...maradek } = { nev: "Anna", kor: 28, email: "a@a.com" }
// nev = "Anna", maradek = { kor: 28, email: "a@a.com" }
```

> [!tip] Felismerés
> `...` az `=` jobb oldalán vagy függvény hívásban - **spread** (szétszór). `...` a paraméterlistában vagy destructuring-ban - **rest** (összegyűjt).

---

## Template literal - szöveg behelyettesítés

```typescript
const nev = "Anna"
const kor = 28

// Template literal - backtick (`) karakterrel
const uzenet = `Szia ${nev}, te ${kor} éves vagy!`
//              ^ backtick   ^ ${} közé jön a változó/kifejezés

// Többsoros szöveg is lehet
const html = `
  <div>
    <h1>${nev}</h1>
    <p>Kor: ${kor}</p>
  </div>
`
```

> [!tip] Felismerés
> Backtick ( `` ` `` ) karakter (nem `"` vagy `'`) + `${ }` - template literal. Python megfelelője az `f"...{valtozo}..."`.

---

## TypeScript-specifikus szintaxis

### Típus annotáció

```typescript
// Változón
const nev: string = "Anna"
//         ^ típus

// Függvény paraméteren és visszatérési értéken
function osszead(a: number, b: number): number {
//                  ^          ^           ^ visszatérési típus
  return a + b
}

// Arrow function-on
const osszead = (a: number, b: number): number => a + b
```

### interface - objektum "szerződés"

Az **interface** leírja, milyen alakú egy objektum. Nem kód, nem fut le - csak **garancia**.

```typescript
interface Felhasznalo {
  nev: string
  kor: number
  email?: string         // <- ? = opcionális (nem kötelező)
  readonly id: string    // <- readonly = nem módosítható
}

// Használat - TS hibát ad ha rossz a struktúra
const user: Felhasznalo = {
  nev: "Anna",
  kor: 28,
  id: "abc123"
}
// user.id = "mas" -> HIBA! readonly
// user.xyz = 1    -> HIBA! nincs "xyz" az interface-ben
```

### type - típus alias

A `type` hasonló az `interface`-hez, de rugalmasabb:

```typescript
// Egyszerű alias
type UserId = string

// Union type - "ez VAGY az"
type Statusz = "aktiv" | "inaktiv" | "fuggoben"
//             ^ csak ezek az értékek lehetnek!

// Object type (hasonló az interface-hez)
type Pont = {
  x: number
  y: number
}

// Intersection - "ez ÉS az"
type Admin = Felhasznalo & { jogosultsagok: string[] }
//                        ^ & = mindkettő tulajdonságait megkapja
```

| Amit látsz | Jelentés |
|-----------|---------|
| `interface X { }` | Objektum alakjának leírása |
| `type X = ...` | Típus alias (lehet unió, intersection, stb.) |
| `X \| Y` | Unió: "X VAGY Y" |
| `X & Y` | Intersection: "X ÉS Y egyszerre" |
| `X?` | Opcionális (lehet undefined) |
| `readonly X` | Nem módosítható |

### Generics - típus paraméter

```typescript
// T egy "helyőrző típus" - bármi lehet
function elsoElem<T>(lista: T[]): T {
  return lista[0]
}

elsoElem<string>(["alma", "korte"])  // -> string
elsoElem<number>([1, 2, 3])          // -> number

// Használat interface-ben
interface ApiValasz<T> {
  data: T
  error: string | null
}

// Konkrét használat
const valasz: ApiValasz<Felhasznalo[]> = {
  data: [{ nev: "Anna", kor: 28, id: "1" }],
  error: null
}
```

> [!tip] Felismerés
> `<T>` szögletes zárójelben egy betű vagy név - az egy generic (típus paraméter). Olyan mint egy "típus változó" - a konkrét típus használatkor derül ki. Nem kell megértened a működését - elég tudnod, hogy `<T>` = "ez a típus majd kiderül".

### enum - felsorolás

```typescript
enum Szin {
  Piros = "piros",
  Kek = "kek",
  Zold = "zold"
}

const szin: Szin = Szin.Piros
```

### as / ! - típus felülbírálás

```typescript
// as - "tudom hogy ez ilyen típus" (type assertion)
const elem = document.getElementById("app") as HTMLDivElement

// ! - "tudom hogy ez nem null" (non-null assertion)
const elem = document.getElementById("app")!
//                                          ^ "biztosan létezik, ne szólj miatta"
```

---

## Speciális operátorok

### ?. - Optional chaining

```typescript
// Régi módszer - sok null check
const varos = felhasznalo && felhasznalo.cim && felhasznalo.cim.varos

// Optional chaining - ha bármelyik null/undefined, megáll és undefined-ot ad
const varos = felhasznalo?.cim?.varos
//                       ^    ^ "ha létezik, menj tovább"
```

### ?? - Nullish coalescing

```typescript
// Alapértelmezett érték ha null vagy undefined
const nev = inputNev ?? "Ismeretlen"
//                    ^ "ha inputNev null/undefined, használd ezt"

// Különbség a || -tól:
const szam = 0 || 10    // -> 10 (mert 0 "falsy")
const szam2 = 0 ?? 10   // -> 0  (mert 0 nem null/undefined)
```

### Ternary operator - `? :`

```typescript
// Egysoros if/else
const uzenet = kor >= 18 ? "felnőtt" : "gyerek"
//             ^ feltétel  ^ ha igaz    ^ ha hamis

// React-ben NAGYON gyakori
return <div>{betoltve ? <Tartalom /> : <Loading />}</div>
```

---

## Promise és async/await

A **Promise** egy "ígéret" - az eredmény még nincs meg, de meg fog jönni:

```typescript
// Promise chain (régebbi stílus)
fetch('/api/users')
  .then(response => response.json())    // <- .then = "ha megvan, csináld ezt"
  .then(data => console.log(data))
  .catch(error => console.error(error)) // <- .catch = "ha hiba van"

// async/await (modern stílus) - UGYANAZ, olvashatóbb
async function betolt() {
  try {
    const response = await fetch('/api/users')
    const data = await response.json()
    console.log(data)
  } catch (error) {
    console.error(error)
  }
}
```

> [!tip] Felismerés
> `.then().then().catch()` - régi Promise chain stílus. `async/await` - modern stílus, könnyebb olvasni. Mindkettő ugyanazt csinálja - aszinkron műveletet kezel.

---

## Export / Import minták

```typescript
// Named export - több dolog is exportálható
export function osszead(a: number, b: number) { return a + b }
export const PI = 3.14
// Import: import { osszead, PI } from './matek'

// Default export - egy fő dolog fájlonként
export default function App() { return <div>Hello</div> }
// Import: import App from './App' (nincs { })

// Re-export
export { osszead } from './matek'

// Type-only import (TS) - CSAK típust importál, nem kerül a build-be
import type { Felhasznalo } from './types'
//     ^ type kulcsszó jelzi
```

> [!tip] Felismerés
> - `import { X }` - named import (kapcsos zárójel)
> - `import X` - default import (nincs zárójel)
> - `import type { X }` - csak típust importál (TS), futásidőben nem létezik
> - Ha egy fájl elején 10 import sor van - az normális, a modern JS projektek sok kicsi fájlból állnak

---

## Összefoglaló tábla - JS vs TS

| Funkció | JavaScript | TypeScript (plusz) |
|---------|-----------|-------------------|
| Változó | `const x = 5` | `const x: number = 5` |
| Függvény | `function f(a) {}` | `function f(a: string): void {}` |
| Objektum alak | - | `interface X { nev: string }` |
| Típus alias | - | `type X = "a" \| "b"` |
| Generic | - | `function f<T>(x: T): T {}` |
| Null-safe | `x && x.y` | `x?.y` (mindkettőben) |
| Import | `import { X } from` | `import type { X } from` |

---

## AI-natív fejlesztés

A JavaScript/TypeScript szintaxis ismerete közvetlenül segít az AI-asszisztált fejlesztésben - a Claude Code, Cursor és Copilot mind TypeScript-ben a legerősebbek. Ha érted a szintaxist, jobban tudod review-olni az AI által generált kódot.

> [!tip] Hogyan használd AI-val
> - *"Magyarázd el mit csinál ez a TypeScript kódrészlet: [kód beillesztése]"*
> - *"Írj egy generic TypeScript függvényt ami API választ tipizál"*
> - *"Alakítsd át ezt a .then() chain-t async/await szintaxisra"*

---

## Kapcsolódó

- [[foundations/szintaxis-alapok|Szintaxis alapok]] - közös fogalmak minden nyelvhez
- [[foundations/python-szintaxis|Python szintaxis]] - Python specifikus szintaxis
- [[foundations/typescript-vs-python|TypeScript vs Python]] - mikor melyik nyelvet válaszd
