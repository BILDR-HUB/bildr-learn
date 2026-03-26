---
tags: [backend, cms, open-source]
datum: 2026-03-26
szint: "🧱 Brick"
kapcsolodo:
  - "[[frontend/nextjs|Next.js]]"
  - "[[frontend/react-vs-spa-vs-preact|React]]"
  - "[[database/mongodb|MongoDB]]"
  - "[[database/postgresql-specifikus|PostgreSQL]]"
  - "[[database/drizzle|Drizzle]]"
  - "[[backend/graphql|GraphQL]]"
---

# Payload CMS

**Kategória:** `CMS` (headless, code-first)
**Docs:** https://payloadcms.com/docs
**Ár:** Open-source (self-hosted ingyenes), Payload Cloud (hosted opció)

---

## Mi ez és mire jó?

> [!tldr] Egy mondatban
> Open-source, TypeScript-first headless CMS, ami **a [[frontend/nextjs|Next.js]] appodon belül fut** - nem külön service, hanem a kódbázisod része. Code-first konfiguráció, nem GUI-first.

A **Payload CMS** egy headless CMS, ami a Next.js App Router-be épül be. A collection-öket (tartalomtípusokat) TypeScript-ben definiálod, és Payload automatikusan generál admin panelt, REST API-t, [[backend/graphql|GraphQL]] API-t, és access control-t.

### Mi teszi különlegessé?

- **A Next.js appodon belül él** - nem külön szerver, nem külön deploy
- **Code-first** - a schema TypeScript kód, nem GUI-ban kattintgatod össze
- **Mindent ad** - admin panel, auth, file upload, verziókezelés, lokalizáció, access control

---

## CMS összehasonlítás

| Szempont | **Payload** | Sanity | Strapi | Contentful |
|----------|------------|--------|--------|------------|
| **Megközelítés** | Code-first, self-hosted | Hosted, GROQ query | GUI-first, self-hosted | Hosted, API-first |
| **Ár** | Ingyenes (self-host) | Ingyenes tier + fizetős | Ingyenes tier + fizetős | Drága (enterprise) |
| **Admin panel** | Auto-generált a schema-ból | Sanity Studio ([[frontend/react-vs-spa-vs-preact|React]]) | Beépített GUI | Hosted dashboard |
| **Framework** | Next.js-be épül | Framework-agnosztikus | Standalone | Framework-agnosztikus |
| **DB** | [[database/postgresql-specifikus|PostgreSQL]], [[database/mongodb|MongoDB]] | Hosted (Sanity) | SQLite, Postgres, MySQL | Hosted |
| **TypeScript** | First-class, type-safe | Jó | Közepes | SDK-k |
| **Vendor lock-in** | Nincs | Közepes (hosted data) | Nincs | Magas |

---

## Mikor használd / Mikor NE

**Mikor jó a Payload:**
- **Content-heavy app** ahol teljes kontroll kell (blog, marketing site, e-commerce katalógus)
- Már van Next.js app és **nem akarsz külön CMS service-t** kezelni
- **Code-first** megközelítés illik a workflow-dba (a schema verziókezelt, PR-ezhető)
- Self-hosted kell, **nincs vendor lock-in**

**Mikor NE használd:**
- **Non-technical szerkesztők** akiknek egyszerű GUI kell - Sanity Studio vagy Contentful jobb DX
- **Nem Next.js a frontend** - Payload Next.js-be épül, más framework-höz Strapi/Sanity jobb
- **Gyors prototípus** ahol CMS setup overhead - Sanity gyorsabban indítható

---

## Alap struktúra

Payload a Next.js `src/` mappában él. A collection-ök TypeScript fájlok:

```typescript
// collections/Posts.ts
import type { CollectionConfig } from 'payload'

export const Posts: CollectionConfig = {
  slug: 'posts',
  admin: { useAsTitle: 'title' },
  access: {
    read: () => true,              // publikus olvasás
    create: ({ req }) => !!req.user, // csak bejelentkezett user
  },
  fields: [
    { name: 'title', type: 'text', required: true },
    { name: 'content', type: 'richText' },
    { name: 'author', type: 'relationship', relationTo: 'users' },
    { name: 'status', type: 'select', options: ['draft', 'published'] },
    { name: 'coverImage', type: 'upload', relationTo: 'media' },
  ],
}
```

Ebből Payload automatikusan generál:
- Admin panel UI-t (CRUD, szűrők, keresés)
- REST API endpoint-okat (`/api/posts`)
- GraphQL API-t
- TypeScript típusokat

---

## AI-natív fejlesztés

A Payload collection config generálás az egyik legjobb AI use case CMS-eknél - leírod milyen tartalomtípust akarsz (mezők, relációk, access control), és Claude Code legenerálja a teljes TypeScript konfigot. A code-first megközelítés miatt a generált kód azonnal verziókezelhető.

> [!tip] Hogyan használd AI-val
> - *"Generálj Payload CMS collection-t: Posts (title, content richText, author relationship, status select, coverImage upload) - publikus olvasás, auth-os írás"*
> - *"Mikor hivatkozz erre: ha content-heavy appot építesz és self-hosted CMS kell. Jelezd a promptban, hogy milyen collection-öket (posts, categories, media) és access control szabályokat akarsz"*
