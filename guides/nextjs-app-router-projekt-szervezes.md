---
tags:
  - nextjs
  - architektura
datum: 2026-03-04
szint: "🧱 Scout"
kapcsolodo:
  - "[[frontend/nextjs|Next.js]]"
  - "[[cloud/vercel|Vercel]]"
---

# Hogyan szervezz egy Next.js App Router projektet?

Ez a guide bemutatja a bevalt konvenciókat egy karbantartható, keretrendszer-fuggetlen Next.js App Router projekt felépítéséhez.

---

## React vs Next.js — miért kell mindkettő?

> [!info] Fontos megérteni
> A React és a [[frontend/nextjs|Next.js]] **nem ugyanaz**, de szorosan összefügg. A React a motor, a Next.js az autó.

**React** = UI **könyvtár** (library)
- Komponenseket írni, állapotot kezelni, UI-t renderelni
- Önmagában csak a "nézet" réteget adja
- Nem mond semmit a routing-ról, szerver kommunikációról, deploy-ról

**[[frontend/nextjs|Next.js]]** = React **keretrendszer** (framework)
- React-re épül, de hozzáadja amit a React nem ad:
  - **Fájl-alapú routing** (az `app/` mappa struktúra = URL struktúra)
  - **Server Components** (szerver-oldali renderelés)
  - **Server Actions** (szerver logika közvetlenül)
  - **API route-ok** (backend is egy projektben)
  - Build, deploy, optimalizáció

**Minden Next.js kód React kód is** — de nem minden React kód Next.js. A `hooks/`, `providers/`, `components/` mappák **React pattern-ök**. Az `app/routes/`, layout-ok, route group-ok **Next.js sajátosságok**.

---

## A projekt mappastruktúra

A `src/` mappán belül **funkció szerint szétválasztott** almappák. Minden mappának pontosan egy feladata van.

```
src/
├── app/routes/          ← CSAK route definíciók (Next.js)
├── components/          ← globálisan használt komponensek (React)
│   └── shadcn/          ← shadcn/ui generált komponensek
├── hooks/               ← React hookok — IDE kerül a business logika
├── providers/           ← React provider-ek (pl. auth-provider.tsx)
└── modules/             ← önálló, komplex komponensek (route-ok alapján)
```

---

## `app/routes/` — csak routing (Next.js)

A route fájlok legyenek a **lehető legkisebbek** — csak definiálják melyik URL-hez mi tartozik, és importáljanak a `modules/`-ból. Semmi logika, semmi UI, csak import.

```tsx
// app/routes/dashboard/page.tsx — EZ a jó
import { DashboardModule } from "@/modules/dashboard";

export default function DashboardPage() {
  return <DashboardModule />;
}
```

> [!warning] Miért?
> Ha keretrendszert kell váltani (pl. Next.js → Remix → valami más), az `app/routes/` mappát kidobod, a `modules/` marad. Ez **sokkal könnyebbé teszi** a migrációt.

---

## `components/` — globálisan használt (React)

Olyan komponensek amik **mindenhol használva vannak** az alkalmazásban. Pl. gomb, input, modal, kártya. A `shadcn/` almappában élnek a shadcn/ui által generált UI komponensek.

---

## `hooks/` — business logika helye (React)

> [!tip] Ez az egyik legfontosabb konvenció
> A business logikát **React hook-okba** kell kiszervezni, nem a komponensekbe. Így a komponens csak a megjelenítésért felel, a logika **újrahasználható és tesztelhető**.

```tsx
// hooks/useApplicationForm.ts — logika
export function useApplicationForm() {
  const [data, setData] = useState({});
  const save = async () => { /* ... */ };
  return { data, setData, save };
}

// modules/application/ApplicationForm.tsx — UI
export function ApplicationForm() {
  const { data, setData, save } = useApplicationForm();
  return <form onSubmit={save}>...</form>;
}
```

---

## `providers/` — React Context

Provider-ek a React Context API-hoz. Ezek az egész alkalmazásra kiterjedő "globális állapotok":

- `auth-provider.tsx` — bejelentkezett user adatai
- `theme-provider.tsx` — dark/light mode

---

## `modules/` — komplex, önálló egységek (React)

Komplexebb mint egy sima komponens — egy teljes feature vagy oldal logikája. **Route-ok alapján kell elrendezni:**

```
modules/
├── dashboard/           ← /dashboard route-hoz
│   ├── components/      ← csak a dashboard-on használt komponensek
│   ├── DashboardModule.tsx
│   └── index.ts
├── auth/                ← /bejelentkezes, /regisztracio
├── palyazat/            ← /palyazat
└── admin/               ← /admin
```

---

## A legfontosabb szabály: modul-függetlenség

> **Modulok NEM függhetnek egymástól.** Ez nem opcionális — ez a szabály.

```
modules/
  auth/          ← NEM importálhat a dashboard-ból ❌
  dashboard/     ← NEM importálhat az auth-ból ❌
```

Ha két modul ugyanazt a kódot akarja használni → a közös kód a `components/` (UI) vagy `hooks/` (logika) mappába kerül.

**Miért?**
- Nincs spagetti-függőség → könnyebb karbantartani
- Bármikor kicserélhetsz egy modult anélkül hogy más modulokat érintenél
- Keretrendszer-váltásnál a modulok változatlanok maradnak

---

## Layout-ok: globális és lokális

A [[frontend/nextjs|Next.js]] App Router-ben a layout-ok **beágyazódnak** — a gyermek oldal megkapja a szülő layout-ját.

```
app/
  layout.tsx                    ← GLOBÁLIS — minden oldal (header, footer)
  (authenticated)/
    layout.tsx                  ← LOKÁLIS — bejelentkezett usereknek (sidebar)
    dashboard/page.tsx
  (public)/
    layout.tsx                  ← LOKÁLIS — publikus oldalak
    page.tsx
```

### Route groups — `(zárójelben)`

A zárójelben lévő mappák **szervezési egységek** az URL-ben **nem jelennek meg**:

```
URL: /dashboard        (nem /authenticated/dashboard)
URL: /                 (nem /public/)
```

Ezzel **layout szinten** szétválaszthatod a bejelentkezett és publikus felületeket anélkül, hogy az URL-t befolyásolnád.

---

## shadcn/ui — komponens könyvtár

- **Nem npm package** → a forráskód a te repódban él (`components/shadcn/`)
- **CLI-vel** gyorsan és könnyen lehet komponenseket létrehozni: `npx shadcn@latest add button`
- Alatta **Radix UI** fut → headless (csak logika/accessibility, nincs stílus)
- Stílusok **Tailwind CSS**-sel → a generált komponenseket **szabadon testre szabod**
- A headless rész (Radix) npm-ből frissül, a stílus a tied

> [!tip] Miért jobb mint egy hagyományos UI library?
> Nincs vendor lock-in. A kód a tied, nem egy `node_modules`-ból jön. Ha holnap változtatni akarsz egy gomb kinézetén, csak szerkeszted a fájlt.

---

## Auth: Better Auth — email OTP

Egy jó auth megoldás [[frontend/nextjs|Next.js]] projekthez a **Better Auth** **email OTP client**-tel:
- Jelszómentes bejelentkezés (magic link / OTP kód)
- Edge-kompatibilis (Cloudflare Workers-ön fut)
- Postgres-native session tárolás
- Ingyenes, self-hosted

---

## AI workflow tippek

### TypeScript inference — ne definiálj feleslegesen

Gyakori hiba hogy az AI **felesleges type-okat generál** mindenhova ahelyett hogy TypeScript inference-re bízná.

```tsx
// ❌ Felesleges — az AI ezt szereti csinálni
const result: { name: string; age: number } = getData();

// ✅ Jobb — a TypeScript magától tudja
const result = getData(); // TypeScript inferálja a típust a getData() return type-jából
```

Ha van jó struktúra (pl. [[database/drizzle|Drizzle]] séma, Zod schema), a TypeScript magától tudja inferrelni a típusokat.

### Folyamatos feedback az AI-nak

Tapasztalat: heteken át **folyamatosan feedbacket** adni az AI-nak ("ez nem így, ez följebb, ez lejjebb") → beáll egy jó minőségi szintre. A minőség nem az első prompton dől el, hanem az iteráción.

### Sztorikra bontva dolgozni

Ne egy nagy blokkban adj ki mindent az AI-nak. **Sztorikra / ticketekre bontva**, fázisonként code review-val → kiszűri a felesleges dolgokat, és az agent nem "fárad el" a context window-ban.

---

## Összefoglaló

- **`src/` struktúra**: routes / components / hooks / providers / modules — mindennek megvan a helye
- **Route fájlok minimálisak** — csak importálnak, a logika a modules-ban és hooks-ban él
- **Modulok NEM függhetnek egymástól** → közös kód a `components/` vagy `hooks/` mappába
- **Business logika → hooks-ba** kiszervezni, nem a komponensbe rakni
- **shadcn/ui CLI** → gyorsan lehet komponenseket generálni és testre szabni
- **Layout-ok:** globális + lokális, route group-okkal `(zárójelben)` szétválasztva
- **TypeScript inference-re bízni** ahol lehet — ne definiálj mindent kézzel
- **React = könyvtár** (UI), **Next.js = keretrendszer** (routing + szerver + deploy)
