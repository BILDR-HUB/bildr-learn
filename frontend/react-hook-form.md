---
tags: [frontend, react, form, react-hook-form, zod]
datum: 2026-03-26
szint: "🧱 Scout"
kapcsolodo:
  - "[[frontend/react-hooks|React Hooks]]"
  - "[[frontend/react|React]]"
  - "[[frontend/react-state-tipusok|React State típusok]]"
  - "[[frontend/shadcn-ui|shadcn/ui]]"
  - "[[backend/hono|Hono]]"
---

## Mi ez és mire jó?

A **React Hook Form** egy **performáns, könnyű form kezelő library** [[frontend/react|React]]-hez. A lényege: **uncontrolled komponenseket** használ a háttérben (nem renderel újra minden billentyűleütéskor), és egy egyszerű hook API-val (`useForm`) kezeli a validációt, hibákat, és submit-ot.

> [!tldr] Egy mondatban
> A React Hook Form a "form useState spaghetti" megoldása - egyetlen `useForm()` hook helyettesíti az összes form-state kezelést, és Zod-dal kombinálva típusbiztos validációt is kapsz.

### Miért nem elég a sima useState?

| | useState kézzel | React Hook Form |
|---|---|---|
| **Re-renderek** | Minden input változásnál | Csak submit-kor vagy hibánál |
| **Validáció** | Kézzel írod meg | Zod séma vagy beépített rules |
| **Hiba kezelés** | Saját state minden mezőhöz | `errors` objektum automatikusan |
| **[[foundations/typescript-vs-python|TypeScript]] típusok** | Kézzel definiálod | Zod-ból inferálja |
| **10+ mezős form** | Kaotikus | Ugyanolyan tiszta |

---

## React Hook Form + Zod - az arany kombó

A legtöbb modern React projektben ez a minta:

1. **Zod séma** definiálja az adat alakját és validációs szabályokat
2. **React Hook Form** kezeli a form state-et és UI-t
3. **@hookform/resolvers/zod** köti össze a kettőt

Ez azért erős, mert ugyanaz a Zod séma amit a [[backend/hono|Hono]] backend-en API validációhoz használsz, a frontend-en form validációra is megy - **nincs duplikáció**.

---

## Mikor használd (és mikor NE)?

| Szituáció | React Hook Form? | Alternatíva |
|-----------|-----------------|-------------|
| Bármilyen form 3+ mezővel | **Igen** | - |
| Login / regisztrációs form | **Igen** | - |
| Egyetlen input (pl. keresőmező) | **Nem** - overkill | Sima `useState` |
| Wizard / multi-step form | **Igen** - `useFormContext` | - |
| Server Components (RSC) | **Nem** - kliens-only | Server Actions |

### React Hook Form vs Formik

| | React Hook Form | Formik |
|---|---|---|
| **Re-renderek** | Minimális (uncontrolled) | Sok (controlled) |
| **Bundle méret** | ~9KB | ~13KB |
| **Karbantartás** | Aktív (2024+) | Lassult |
| **Zod integráció** | Natív (`@hookform/resolvers`) | Yup-ot preferálja |
| **Amikor válaszd** | Új projekt | Legacy Formik projekt |

---

## Gyakori minták

### 1. Alap form + Zod validáció

A `useForm` hook-ot a Zod resolver-rel inicializálod, és a `register` függvénnyel kötöd az inputokat. A `handleSubmit` automatikusan validál submit előtt.

### 2. Nested / dynamic fields

A `useFieldArray` hook-kal dinamikusan adhatsz hozzá / törölhetsz mezőket (pl. "Adj hozzá még egy tételt" számla form-ban).

### 3. Multi-step wizard

A `useFormContext` provider-rel megosztod a form state-et több step komponens között, anélkül hogy prop drilling-ot csinálnál.

---

## Buktatók

- **Uncontrolled =/= nem látod az értéket** - ha real-time kell a form érték (pl. live preview), használj `watch()`-ot, de tudd hogy az re-rendert okoz
- **Zod resolver verziók** - `@hookform/resolvers` verziónak kompatibilisnek kell lennie a Zod verzióval, különben furcsa TypeScript hibákat kapsz
- **[[frontend/shadcn-ui|shadcn/ui]] form komponensek** - a shadcn/ui beépítetten támogatja a React Hook Form-ot, használd a `<Form>` wrappert

---

## AI-natív fejlesztés

A form építés az AI coding tool-ok egyik legerősebb területe - a Zod séma + React Hook Form + shadcn/ui kombó teljesen generálható. Claude Code-nak elég megadni a mezőket és a validációs szabályokat, és a teljes form kódot (séma, hook setup, JSX, error handling) megkapod.

> [!tip] Hogyan használd AI-val
> - *"Építs egy React Hook Form + Zod form-ot shadcn/ui komponensekkel: név (kötelező), email (email formátum), jelszó (min 8 karakter, szám kell)"*
> - *"Adj hozzá useFieldArray-t ehhez a form-hoz - dinamikusan adható tételek, mindegyikhez név és ár mező"*
> - *"Használd ugyanazt a Zod sémát a Hono backend validációhoz és a frontend React Hook Form-hoz"*

---

## Kapcsolódó

- [[frontend/react-hooks|React Hooks]] - useForm maga is egy hook, a hook mintákat érdemes érteni
- [[frontend/react-state-tipusok|React State típusok]] - form state = lokális kliens state, nem szerver state
- [[frontend/react|React]] - React alapok
- [[frontend/shadcn-ui|shadcn/ui]] - UI komponensek beépített React Hook Form támogatással
- [[backend/hono|Hono]] - backend oldali Zod validáció, ugyanaz a séma mint a form-ban
