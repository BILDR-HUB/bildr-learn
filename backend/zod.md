---
tags: [backend, validation, typescript]
datum: 2026-03-26
szint: "🌱 Newcomer"
kapcsolodo:
  - "[[backend/hono|Hono]]"
  - "[[backend/trpc|tRPC]]"
  - "[[backend/express|Express]]"
  - "[[foundations/typescript-vs-python|TypeScript]]"
---

# Zod

**Kategória:** `validation library` (schema validation - TypeScript-first)
**URL:** https://zod.dev
**Ár:** Ingyenes, open source (MIT)

---

## Mi ez és mire jó?

A **Zod** egy **séma validációs library** - [[foundations/typescript-vs-python|TypeScript]]-ben definiálod az adat "alakját" (schema), és a Zod futásidőben ellenőrzi, hogy a kapott adat tényleg megfelel-e. A kulcs: **egyszer írod meg a sémát, és mindenhol használod** - frontend form validáció, backend API input, környezeti változók, bármi.

> [!tldr] Egy mondatban
> A Zod a "TypeScript típusok runtime-ban" - ugyanazt a sémát használod frontend form validációhoz és backend API validációhoz ([[backend/hono|Hono]], [[backend/trpc|tRPC]]), így nincs duplikáció.

### Miért nem elég a TypeScript típus?

A TypeScript típusok **fordítási időben** léteznek - a `type User = { name: string }` nem véd meg attól, hogy a szerver rossz adatot kapjon. A Zod **futásidőben** is validál:

| | TypeScript típus | Zod séma |
|---|---|---|
| **Mikor ellenőriz** | Fordítási időben (build) | Futásidőben (runtime) |
| **Véd API inputtól** | Nem | Igen |
| **Véd form adattól** | Nem | Igen |
| **Generál TypeScript típust** | - | Igen (`z.infer<typeof schema>`) |
| **Hibaüzenet** | Nincs (build error) | Részletes, testre szabható |

---

## Hol használd?

| Használat | Library kombó | Mit csinál |
|-----------|--------------|------------|
| **Backend API input** | [[backend/hono|Hono]] + `@hono/zod-validator` | Route handler előtt validálja a request body-t |
| **Frontend form** | React Hook Form + `@hookform/resolvers/zod` | Form mezők validálása submit előtt |
| **tRPC input** | [[backend/trpc|tRPC]] `.input(zodSchema)` | End-to-end típusbiztos API input |
| **Env változók** | `z.object({}).parse(process.env)` | Alkalmazás indulásánál ellenőrzi a .env-et |

> [!tip] "Write once, validate everywhere"
> Egy Zod sémát definiálsz a `shared/` mappában, és importálod a frontend form-ba ÉS a backend route-ba. Ha változik a séma, mindkét oldal azonnal tudja.

---

## Alternatívák

| Library | Mikor válaszd |
|---------|---------------|
| **Zod** | TypeScript projekt, tRPC/Hono integráció kell |
| **Yup** | Régebbi projekt, Formik-kal használod |
| **Joi** | Node.js backend-only, nincs TypeScript-first |
| **ArkType** | Teljesítmény-kritikus (10-100x gyorsabb), de fiatalabb ökoszisztéma |
| **Valibot** | Kisebb bundle méret kell (tree-shakeable) |

---

## Buktatók

- **Bundle méret** - Zod ~13KB (gzip), ami SPA-ban nem elhanyagolható. Ha ez gond: Valibot tree-shakeable alternatíva
- **Hibaüzenetek** - alapból angol és technikai. Frontend form-okhoz testre kell szabni: `.min(1, { message: "Kötelező mező" })`
- **Circular referenciák** - `z.lazy()` kell rekurzív sémákhoz, de AI hajlamos elfelejteni

---

## AI-natív fejlesztés

A Zod séma generálás az egyik legtermészetesebb AI-feladat - leírod az adatstruktúrát szavakban, és Claude Code legenerálja a teljes Zod sémát típusokkal együtt. A kulcs: jelezd az integrációt (Hono, tRPC, React Hook Form), mert mindegyikhez más wrapper kell.

> [!tip] Hogyan használd AI-val
> - *"Generálj Zod sémát: User (name min 2 char, email, age optional number, role enum: admin/user) + z.infer típus"*
> - *"Mindig jelezd az integrációt: 'Zod + Hono', 'Zod + React Hook Form', 'Zod + tRPC'. Az AI más boilerplate-et generál mindegyikhez"*
> - *"Zod env validáció: CLOUDFLARE_ACCOUNT_ID string, FIRECRAWL_API_KEY string, PORT number default 3000"*
