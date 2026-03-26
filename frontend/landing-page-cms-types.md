---
tags: [frontend, cms, nextjs, headless]
datum: 2026-03-03
szint: "🧱 Scout"
kapcsolodo:
  - "[[frontend/nextjs|Next.js]]"
  - "[[cloud/docker-alapok|Docker alapok]]"
  - "[[cloud/railway|Railway]]"
  - "[[cloud/vercel|Vercel]]"
  - "[[database/supabase|Supabase]]"
---

# Landing Page CMS types

Headless CMS választas landing page fejleszteshez [[frontend/nextjs|Next.js]] stack-kel. Ez a jegyzet összehasonlítja a fobb opciot: self-hosted vs managed, developer experience, pricing és integracio szempontjából.

## Mi az a Headless CMS?

A hagyomanyos CMS (WordPress) a tartalmat és a frontend-et egyutt kezeli. A **headless CMS** csak a tartalmat kezeli, és API-n (REST vagy GraphQL) szolgáltatja ki. A frontend teljesen fuggetlen -- lehet [[frontend/nextjs|Next.js]], Astro, bármi.

**Elonyok:**
- Teljes kontroll a frontend felett
- Ugyanaz a tartalom több platformra (web, mobile, email)
- Jobb performance (SSG/ISR a Next.js-szel)
- Fejleszto-barat workflow

## Döntési fa

```mermaid
graph TD
    START[Landing page CMS kell] --> Q1{Van szerver /<br/>Docker tudas?}

    Q1 -->|Igen| Q2{Budget?}
    Q1 -->|Nem| MANAGED[Managed CMS]

    Q2 -->|Minimalis / $0| SELFHOST[Self-hosted CMS]
    Q2 -->|Van budzsé| Q3{Csapatmeret?}

    Q3 -->|Solo / kicsi| SELFHOST
    Q3 -->|Nagyobb csapat| MANAGED

    SELFHOST --> Q4{TypeScript-first?}
    Q4 -->|Igen| PAYLOAD[Payload CMS]
    Q4 -->|Mindegy| Q5{Egyszeruseg?}
    Q5 -->|Fontos| DIRECTUS[Directus]
    Q5 -->|Pluginek kellenek| STRAPI[Strapi]

    MANAGED --> Q6{Budget erzekeny?}
    Q6 -->|Igen| SANITY[Sanity<br/>Free tier]
    Q6 -->|Nem| Q7{Sok lokalizacio?}
    Q7 -->|Igen| CONTENTFUL[Contentful]
    Q7 -->|Nem| SANITY

    style PAYLOAD fill:#10b981,color:#fff
    style STRAPI fill:#4945ff,color:#fff
    style SANITY fill:#f03e2f,color:#fff
    style CONTENTFUL fill:#0070f3,color:#fff
    style DIRECTUS fill:#6644ff,color:#fff
```

## Self-hosted opciok

### Payload CMS

A **Payload** egy TypeScript-first, Next.js-nativ headless CMS. A 3.0 verzió ota kozvetlenul Next.js App Router-be epul -- nincs kulon szerver.

**Fo jellemzők:**
- Next.js App Router nativ integracio (egyetlen app)
- TypeScript-bol generalt tipusok
- Beepitett Auth, Access Control, Upload
- Admin panel auto-generalt a schema-bol
- PostgreSQL vagy MongoDB backend (Postgres ajanlott a [[database/supabase|Supabase]]-zel)

**Telepítes:**
```bash
npx create-payload-app@latest my-landing-page
# Valaszd: Next.js, PostgreSQL, Blank template
```

**Példa collection (tartalom tipus):**
```typescript
// collections/LandingPages.ts
import { CollectionConfig } from 'payload'

export const LandingPages: CollectionConfig = {
  slug: 'landing-pages',
  admin: {
    useAsTitle: 'title',
  },
  fields: [
    { name: 'title', type: 'text', required: true },
    { name: 'slug', type: 'text', unique: true },
    { name: 'hero', type: 'group', fields: [
      { name: 'heading', type: 'text' },
      { name: 'subheading', type: 'textarea' },
      { name: 'cta', type: 'text' },
      { name: 'image', type: 'upload', relationTo: 'media' },
    ]},
    { name: 'sections', type: 'blocks', blocks: [
      // Feature grid, Testimonials, Pricing, FAQ stb.
    ]},
    { name: 'seo', type: 'group', fields: [
      { name: 'metaTitle', type: 'text' },
      { name: 'metaDescription', type: 'textarea' },
    ]},
  ],
}
```

> [!tip] Payload + Supabase
> A Payload 3.x PostgreSQL adapter-rel használhato a [[database/supabase|Supabase]] adatbázissal. Ugyanaz a Postgres instance kezeli a CMS tartalmat és az app adatokat is.

**Deploy:** Egyetlen Next.js app, tehát [[cloud/vercel|Vercel]]-re vagy [[cloud/railway|Railway]]-re deploy-olhato. Docker-rel is self-hostolhato ([[cloud/docker-alapok|Docker alapok]]).

---

### Strapi

A **Strapi** a legnepszerubb open-source headless CMS. Erett okoszisztema, sok plugin, nagy community.

**Fo jellemzők:**
- Content-Type Builder (vizualis schema editor)
- REST és GraphQL API automatikusan
- Plugin rendszer (SEO, i18n, media library)
- Role-Based Access Control
- JavaScript/TypeScript (v5 már TS-first)

**Telepítes:**
```bash
npx create-strapi@latest my-cms
```

**Next.js integracio:**
```typescript
// lib/strapi.ts
const STRAPI_URL = process.env.STRAPI_URL || 'http://localhost:1337'

export async function getLandingPage(slug: string) {
  const res = await fetch(
    `${STRAPI_URL}/api/landing-pages?filters[slug]=${slug}&populate=deep`,
    {
      headers: { Authorization: `Bearer ${process.env.STRAPI_TOKEN}` },
      next: { revalidate: 60 }, // ISR: 60 masodpercenkent
    }
  )
  const data = await res.json()
  return data.data[0]
}
```

**Deploy:** Kulon szerver kell (nem fut Vercel-en). [[cloud/railway|Railway]] vagy [[cloud/docker-alapok|Docker]] a legjobb opcio.

> [!warning] Strapi gotcha-k
> - A Strapi és a Next.js frontend **ket kulon alkalmazás** -- ket deploy kell
> - A `populate=deep` lassu lehet sok relacional, inkabb explicit populate-et használj
> - A free verzió korlátozott (nincs audit log, nincs content versioning)

---

### Directus

A **Directus** "instant API" bármilyen SQL adatbázisra. Nem kell schema-t kódban definiálalni -- rahuzod egy meglevo DB-re és azonnal van admin UI + API.

**Fo jellemzők:**
- Bármilyen SQL DB-re raulhet (Postgres, MySQL, SQLite, stb.)
- REST + GraphQL automatikusan
- Szep, modern admin UI
- Flows (automatizaciok, hasonlo az n8n-hez)
- Granular permissions
- Beepitett file storage (S3, Supabase Storage, local)

**Mikor jó választas:**
- Ha már VAN egy adatbázisod ([[database/supabase|Supabase]]) és ahhoz akarsz admin felületet
- Ha nem-fejlesztoknek kell szerkeszteni a tartalmat
- Ha gyorsan kell egy CMS, minimalis konfigurálassal

**Deploy:** Docker image, [[cloud/railway|Railway]]-re egy kattintas.

```yaml
# docker-compose.yml
services:
  directus:
    image: directus/directus:latest
    ports:
      - "8055:8055"
    environment:
      DB_CLIENT: pg
      DB_HOST: db.xyz.supabase.co
      DB_PORT: 5432
      DB_DATABASE: postgres
      DB_USER: postgres
      DB_PASSWORD: ${SUPABASE_DB_PASSWORD}
      SECRET: ${DIRECTUS_SECRET}
      ADMIN_EMAIL: admin@example.com
      ADMIN_PASSWORD: ${ADMIN_PASSWORD}
```

## Managed opciok

### Sanity

A **Sanity** egy managed headless CMS, ami kiemelkedik a **real-time collaboration** és a **customizable Studio** teren.

**Fo jellemzők:**
- Sanity Studio -- React-alapu, teljesen testreszabhato admin UI
- GROQ -- saját query nyelv (erosebb, mint REST filterek)
- Real-time collaboration (Google Docs szintű)
- Content Lake -- hosted tartalom, nem kell DB-t kezelni
- Visual Editing (Next.js-ben live preview)
- Generous free tier

**Next.js integracio:**
```typescript
// sanity/client.ts
import { createClient } from '@sanity/client'

export const sanityClient = createClient({
  projectId: process.env.NEXT_PUBLIC_SANITY_PROJECT_ID!,
  dataset: 'production',
  apiVersion: '2024-01-01',
  useCdn: true,
})

// Lekerdezes GROQ-val
const page = await sanityClient.fetch(
  `*[_type == "landingPage" && slug.current == $slug][0]{
    title,
    hero { heading, subheading, "imageUrl": image.asset->url },
    sections[]{ ..., "imageUrl": image.asset->url }
  }`,
  { slug }
)
```

> [!tip] Sanity + Vercel Visual Editing
> A Sanity és a [[cloud/vercel|Vercel]] mely integracioja van: a Vercel Visual Editing-gel a content editor a live oldalon kattinthat egy elemre, és a Sanity Studio-ban szerkesztheti. Landing page-eknel ez nagyon jó UX a marketingeseknek.

---

### Contentful

A **Contentful** egy enterprise-grade managed CMS. Erett, stabil, de dragabb.

**Fo jellemzők:**
- Structured content modelling (eros tipusrendszer)
- Több locale / i18n beepitve
- GraphQL és REST API
- Webhooks (rebuild trigger [[cloud/vercel|Vercel]]-en)
- Rich text field saját renderer-rel
- SDK-k minden platformra

**Mikor Contentful?**
- Enterprise projekt, ahol fontos a support és SLA
- Sok nyelv / lokalizacio kell
- Marketing csapat használja a CMS-t
- Budget nem szuk

> [!warning] Contentful pricing
> A free tier nagyon korlátozott (5 user, 1 locale). Ha kinovod, hirtelen draga lesz ($300+/hó). Landing page-ekhez ez ritkan eri még -- inkabb Sanity vagy self-hosted.

## Pricing összehasonlítas

| CMS | Free tier | Fizetos kezdes | Self-hosted? | Megjegyzes |
|-----|-----------|----------------|--------------|------------|
| **Payload** | Korlatlan (OSS) | Cloud: $35/hó | Igen | Self-hosted = $0, csak hosting kell |
| **Strapi** | Korlatlan (OSS) | Cloud: $29/hó | Igen | Self-hosted = $0, de 2 deploy kell |
| **Directus** | Korlatlan (OSS) | Cloud: $15/hó | Igen | Self-hosted = $0, Docker-rel könnyű |
| **Sanity** | 3 user, 100K API CDN req/hó | $15/user/hó | Nem | Free tier boven eleg landing page-hez |
| **Contentful** | 5 user, 1 locale | $300/hó (Team) | Nem | Draga, enterprise-re van kitalálva |

> [!info] Hosting költségek self-hosted CMS-eknel
> Self-hosted nem jelenti, hogy ingyen van. Szamolj hosting költséggel:
> - [[cloud/railway|Railway]]: ~$5-10/hó (Starter plan + DB)
> - VPS (Hetzner/DigitalOcean): ~$5-10/hó
> - [[database/supabase|Supabase]] DB: free tier-en is eleg landing page-hez

## Developer Experience összehasonlítas

| Szempont | Payload | Strapi | Directus | Sanity | Contentful |
|----------|---------|--------|----------|--------|------------|
| **TypeScript** | Nativ | v5: Nativ | Partial | SDK: Igen | SDK: Igen |
| **Next.js integracio** | Beepitett | Fetch/SDK | Fetch/SDK | SDK + Visual Edit | SDK |
| **Schema definiáltio** | Kódban (TS) | UI vagy kod | UI (DB-bol) | Kódban (TS) | UI |
| **Admin UI** | Auto-generalt | Auto-generalt | Auto-generalt | React Studio | SaaS UI |
| **API tipus** | REST + Local API | REST + GraphQL | REST + GraphQL | GROQ + GraphQL | REST + GraphQL |
| **Learning curve** | Kozepes | Alacsony | Alacsony | Kozepes-Magas | Alacsony |
| **Migration** | Kódban (DB push) | Auto migration | Auto migration | Nincs (hosted) | Nincs (hosted) |
| **Customizability** | Nagyon magas | Magas (pluginek) | Kozepes (Flows) | Magas (Studio) | Alacsony |

## Mikor melyiket valaszd?

### Payload CMS -- ha a kod a kiraly
- TypeScript-first projekt
- Next.js App Router-t használsz
- Egyetlen deploy-t akarsz (CMS + frontend = 1 app)
- Teljes kontroll kell a schema és az admin felett
- [[database/supabase|Supabase]] Postgres-t használod DB-nek

### Strapi -- ha az okoszisztema fontos
- Plugin-eket akarsz használni (SEO, i18n, media)
- Vizualis Content-Type Builder kell a csapatnak
- Már ismered (legnagyobb community)
- Nem gond, hogy 2 kulon service-t deploy-olsz

### Directus -- ha gyorsasag kell
- Már van adatbázisod és admin UI kell ra
- Nem-fejlesztok is kezelni fogjak
- Docker-ben akarsz mindent futtatni
- Minimalis konfigurálast akarsz

### Sanity -- ha a content editing experience a prioritas
- Marketing csapat szerkeszti a landing page-eket
- Visual editing kell (Vercel integracio)
- Real-time collaboration fontos
- Nem akarsz CMS-t hostolni / karbantartani

### Contentful -- ha enterprise
- SLA és support kell
- Sok nyelv / lokalizacio
- Nagyvallalati csapat
- Budget nem kerdes

## Ajanlas landing page-ekhez

> [!tip] A legjobb választas 2025-2026-ban
> **Solo dev / kis csapat + Next.js:** Payload CMS (self-hosted [[cloud/railway|Railway]]-n vagy [[cloud/vercel|Vercel]]-en)
> **Marketinges csapat is szerkeszt:** Sanity (Visual Editing + free tier)
> **Gyors MVP:** Directus ([[cloud/docker-alapok|Docker]]-rel, 5 perc setup)

Landing page-eknel általában keves a tartalom (5-20 oldal), ezert a **free tier** minden opcioval eleg. A döntést inkabb a **developer experience** és a **csapat igényei** hatarozzak még, nem a pricing.

## Kapcsolodo

- [[frontend/nextjs|Next.js]] -- frontend framework
- [[cloud/docker-alapok|Docker alapok]] -- self-hosted CMS konterizalas
- [[cloud/railway|Railway]] -- PaaS deploy self-hosted CMS-ekhez
- [[cloud/vercel|Vercel]] -- frontend deploy + Sanity Visual Editing
- [[database/supabase|Supabase]] -- adatbázis backend, Payload-dal is használhato
