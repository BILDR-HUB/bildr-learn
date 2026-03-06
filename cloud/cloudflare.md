---
tags:
  - hosting
  - edge
  - cloudflare
datum: 2026-03-03
szint: "🏗️ Builder"
kapcsolodo:
  - "[[cloud/vercel|Vercel]]"
  - "[[backend/hono|Hono]]"
  - "[[database/drizzle|Drizzle]]"
  - "[[cloud/railway|Railway]]"
  - "[[database/supabase|Supabase]]"
  - "[[cloud/nextjs-on-cloudflare-workers|Next.js on Cloudflare Workers]]"
  - "[[_moc/moc-deployment|MOC - Deployment]]"
---

# Cloudflare

**Kategoria:** `hosting` (edge platform)
**URL:** https://www.cloudflare.com
**Ar/Terv:** Workers Paid: $5/hó (10M request, 5GB DB, R2 storage)

---

## Mi ez és mire jó?

A **Cloudflare** egy **edge computing platform** -- az appod nem egy szerveren fut, hanem a vilag 300+ adatkozpontjaban egyszerre, a felhasználóhoz legkozelebb.

**Egyszerű analogia:**

A [[cloud/vercel|Vercel]] olyan, mint egy etterem: egy helyrol szolgalsz ki mindenkit.
A Cloudflare olyan, mint egy **franchise hálózat**: minden varosban van egy konyha, és mindig a legkozelebbi szolgal ki.

```mermaid
graph TD
    subgraph "Vercel (hagyomanyos)"
        U1[User Budapest] --> S1[Szerver<br/>US East]
        U2[User Tokio] --> S1
        U3[User London] --> S1
    end

    subgraph "Cloudflare (edge)"
        U4[User Budapest] --> E1[Edge<br/>Frankfurt]
        U5[User Tokio] --> E2[Edge<br/>Tokio]
        U6[User London] --> E3[Edge<br/>London]
    end

    style S1 fill:#000,color:white
    style E1 fill:#f48120,color:white
    style E2 fill:#f48120,color:white
    style E3 fill:#f48120,color:white
```

> [!tldr] Egy mondatban
> A Cloudflare egy platform ahol a kódod a felhasználóhoz legkozelebbi szerveren fut, és a $5/hó plan szinte bármilyen kis-kozepes apphoz eleg.

---

## Cloudflare szolgáltatasok attekintese

A Cloudflare nem egy eszkoz, hanem egy **okoszisztema**. A $5/hó plan ezeket mind tartalmazza:

| Szolgáltatas | Mi ez | Analogia |
|---|---|---|
| **Workers** | Kod futtatasa az edge-en | Mint a [[cloud/vercel|Vercel]] Serverless Functions, de gyorsabb |
| **Pages** | Statikus weboldal hosting | Mint a Vercel, de frontend-re optimalizálva |
| **D1** | SQLite adatbázis a felhőben | Mint a [[database/supabase|Supabase]] PostgreSQL, de egyszerűbb és olcsobb |
| **R2** | Fájl tárolás (kepek, PDF-ek) | Mint az AWS S3, de **nincs egress fee** |
| **KV** | Kulcs-érték tar | Mint a [[database/redis|Redis]], de serverless |
| **Queues** | Üzenet sor (aszinkron feldolgozas) | Mint az AWS SQS |
| **Durable Objects** | Állapotmegorzo mini-szerverek | Nincs hasonlo mashol -- egyedi CF koncepcio |
| **Cron Triggers** | Ütemezett feladatok | Mint a cron job, de serverless |

---

## Cloudflare vs Vercel vs Railway

| Szempont | **Cloudflare** | [[cloud/vercel|Vercel]] | [[cloud/railway|Railway]] |
|----------|-----------|--------|---------|
| **Ar** | $5/hó | $20/hó (Pro) | Pay-as-you-go (~$5-20/hó) |
| **Frontend** | Pages (statikus) | Next.js nativ (SSR) | Docker (bármi) |
| **Backend** | Workers (edge) | Serverless Functions | Docker konténer |
| **Adatbázis** | D1 (SQLite) | Nincs (külső kell) | PostgreSQL, MySQL |
| **File storage** | R2 (S3-kompatibilis) | Nincs | Volume mount |
| **Cron jobs** | Cron Triggers (15 perc CPU) | Vercel Cron (60s limit) | Nativ cron |
| **Cold start** | ~0ms (V8 isolate) | 100-500ms | Nincs (always-on) |
| **Mire jó** | API-k, edge logika, belső toolok | Next.js fullstack appok | Bármilyen backend |
| **Mire NEM jó** | Nehez szamitasok, Node.js-only lib-ek | Hosszu futasi ideju processek | Ha olcso hosting kell |

> [!tip] Mikor melyiket valaszd?
> **Cloudflare ($5/hó):** Belső tool, API, kis SaaS → [[backend/hono|Hono]] + React + D1
> **Vercel ($0-20/hó):** Marketing oldal, [[frontend/nextjs|Next.js]] fullstack app → Next.js + Supabase
> **Railway ($5-20/hó):** Ha Docker kell, saját Postgres, hosszu futasu job-ok

---

## $5/hó plan -- mit kapsz?

| Erőforrás | Mennyit kapsz | Mennyit használsz (belső tool) | Eleg? |
|---|---|---|---|
| **Requests** | 10M/hó | ~10-50K/hó | Boven |
| **CPU ido** | 30M ms/hó | ~100K ms/hó | Boven |
| **D1 storage** | 5 GB | < 1 GB (evekig) | Boven |
| **D1 reads** | 25 milliard/hó | ~100K/hó | Boven |
| **D1 writes** | 50M/hó | ~10K/hó | Boven |
| **R2 storage** | $0.015/GB/hó | ~1-5 GB (PDF-ek) | ~$0.07/hó |
| **Cron Triggers** | 15 perc CPU/trigger | < 1 perc | Boven |

> [!success] Osszköltseg egy belső toolra
> **$5/hó fix + ~$0.10 R2** = összesen **~$5.10/hó**
> Összehasonlítasul: Vercel Pro = $20/hó + Supabase Pro = $25/hó = **$45/hó**

---

## Cloudflare Access -- belső appok védelme

A belső appok legfontosabb kerdese: **ki ferhet hozzá?** A Cloudflare Access (Zero Trust) megoldja ezt **kod irasa nelkul**.

**Hogyan működik:**
1. A Workers app ele teszel egy Access policy-t a Cloudflare dashboardon
2. Aki megnyitja az app URL-jet, Cloudflare bejelentkeztet (Google, GitHub, stb.)
3. Csak az engedélyezett email/domain felhasználók fernek hozzá
4. **Nem kell auth kódot irnod** -- nincs [[backend/clerk|Clerk]], nincs Auth.js, nincs [[backend/jwt|JWT]] kezeles

| Szempont | Cloudflare Access | [[backend/clerk|Clerk]] / Auth.js |
|---|---|---|
| **Költség** | Ingyenes (≤ 50 user) | Clerk: $25/hó (Pro) |
| **Beállítás** | Dashboard: 5 perc | Kod: orak |
| **Kod szükséges?** | Nem | Igen (middleware, session, stb.) |
| **SSO** | Google Workspace, Azure AD, GitHub | Provider-fuggo |
| **Mire jó** | Belső tool hozzáférés-védelem | Publikus app user kezeles |

> [!tip] < 10 fos csapatnak tokeletes
> A Free plan 50 user-t tamogat. Egy belső tool-nal (5-10 ember) **soha nem kell fizetni az auth-ert**.
> Google Workspace-szel integralva: "csak @cegnev.hu email-ek ferjenek hozzá" = 2 kattintas.

```mermaid
sequenceDiagram
    participant U as Felhasznalo
    participant CA as Cloudflare Access
    participant W as Workers App

    U->>CA: app.cegnev.hu megnyitasa
    CA->>U: Google login keres
    U->>CA: Google bejelentkezes
    CA->>CA: Email check: @cegnev.hu?
    CA->>W: Tovabbit (+ JWT header)
    W->>U: App tartalom
```

---

## Ceges belső appok -- use case-ek

A Cloudflare Workers **legjobban belső tooloknal és kis SaaS appknal teljesit**. Alabb konkret use case-ek:

### Pozitiv use case-ek (ezekre tokeletes)

#### 1. Szamlakezelö / invoice dashboard

```
Hono API + D1 (szamlak) + R2 (PDF-ek) + Access (Google login)
Koltseg: $5/ho | Vercel+Supabase alternativa: $45/ho
```

#### 2. CRM / ugyfelkezelo

```
Hono API + D1 (ugyfelek, interakciok) + Access (csapat login)
Koltseg: $5/ho
```

#### 3. Projekt dashboard / reporting

```
Hono API + D1 (projektek, feladatok) + Cron (status check)
Koltseg: $5/ho
```

#### 4. Webhook processor / integracio hub

```
Workers (webhook fogadas) + Queues (feldolgozas) + D1 (log)
Koltseg: $5/ho
```

#### 5. Fájlkezelo / document management

```
Hono API + R2 (fajlok) + D1 (metadata) + Access
Koltseg: $5/ho + ~$0.015/GB storage
```

#### 6. API gateway / belső microservice hub

```
Workers (routing) + Service Bindings (inter-worker hivas)
Koltseg: $5/ho
```

---

## Mikor NE használd a Cloudflare-t -- negativ példak

> [!danger] Fontos
> A Cloudflare Workers **nem mindenre jó**. Ezekben az esetekben **rosszabb választas** mint az alternativak.

### 1. Nehez szamitasok (CPU-intenziv feladatok)
**Probléma:** Workers-on **30 mp CPU time limit** van, és **128 MB memoria**.

### 2. PostgreSQL-t igénylo appok
**Probléma:** A D1 SQLite alapu → **egy writer egyszerre**, max 10GB.
**Alternativa:** [[database/supabase|Supabase]] (PostgreSQL) vagy Neon

### 3. WebSocket-heavy appok (real-time)
**Alternativa:** VPS (Node.js + Socket.io), [[database/supabase|Supabase]] Realtime

### 4. Node.js-specifikus library-k
**Probléma:** Workers V8 isolate-ban fut, **nem Node.js-ben**. Sok npm csomag nem működik.

### 5. Self-hosting / on-premise követelmeny
**Alternativa:** VPS (Hetzner) + [[cloud/docker-alapok|Docker]]

### 6. Long-running background processek
**Alternativa:** VPS vagy [[cloud/railway|Railway]]

---

## Setup -- lépésrol lépésre

### 1. Regisztrácio
- https://dash.cloudflare.com → Sign up
- Workers & Pages → Overview → Workers Paid plan ($5/hó)

### 2. CLI (Wrangler) telepítes

```bash
npm install -g wrangler
wrangler login
```

### 3. Projekt létrehozas

```bash
# Hono API Worker
npm create hono@latest my-api
# Runtime: cloudflare-workers

# D1 adatbazis
wrangler d1 create my-db

# R2 bucket
wrangler r2 bucket create my-files
```

### 4. Deploy

```bash
cd my-api && wrangler deploy
```

---

## D1 -- az adatbázis

A **D1** egy **SQLite adatbázis a felhőben**.

**[[database/drizzle|Drizzle]] + D1** integracio:

```typescript
import { drizzle } from 'drizzle-orm/d1'
import * as schema from './schema'

// Cloudflare Worker-ben a DB a "binding"-eken keresztul elerheto
const db = drizzle(env.DB, { schema })

const invoices = await db.select().from(schema.invoices)
```

---

## Best Practices

### Biztonság

- **Secrets:** `wrangler secret put API_KEY` -- titkos kulcsokat soha ne a kódba tedd
- A D1, R2, KV **nem publikusan elérheto** -- csak a Worker-eden belulrol ered el
- Workers automatikusan HTTPS -- nincs HTTP opcio

### Költségoptimalizalas

- A $5/hó plan szinte minden belső toolra **boven eleg**
- R2 **egress ingyenes** -- ha sok PDF-et toltesz le, nem fizetsz extra savszelessegert
- Cron Trigger-ek a plan-ban vannak, nem kell kulon fizetni

---

## Hasznos parancsok / kodreszletek

```bash
# Wrangler CLI
wrangler login                    # bejelentkezes
wrangler dev                      # lokalis dev szerver
wrangler deploy                   # production deploy
wrangler tail                     # eles logok (real-time)

# D1 adatbazis
wrangler d1 create mydb           # DB letrehozas
wrangler d1 execute mydb --command "SELECT * FROM invoices"
wrangler d1 migrations apply mydb # migration futtatas

# R2 storage
wrangler r2 bucket create mybucket
wrangler r2 object put mybucket/file.pdf --file=./file.pdf

# Secrets
wrangler secret put API_KEY       # titkos kulcs beallitas
wrangler secret list              # meglevo titkos kulcsok listaja
```

---

## Hasznos linkek

- **Dashboard:** https://dash.cloudflare.com
- **Workers docs:** https://developers.cloudflare.com/workers/
- **D1 docs:** https://developers.cloudflare.com/d1/
- **R2 docs:** https://developers.cloudflare.com/r2/
- **Pages docs:** https://developers.cloudflare.com/pages/
- **Wrangler CLI:** https://developers.cloudflare.com/workers/wrangler/
- **Pricing:** https://developers.cloudflare.com/workers/platform/pricing/
- **Discord:** https://discord.cloudflare.com

---

## Kapcsolodo anyagok

- [[cloud/nextjs-on-cloudflare-workers|Next.js on Cloudflare Workers]] -- fullstack Next.js futtatasa CF Workers-on (OpenNext adapter)
- ViNext -- Next.js reimplementacio Vite-on, kísérleti, 4.4x gyorsabb build
- [[cloud/vercel|Vercel]] -- alternativ hosting (Next.js-re jobb, de dragabb)
- [[backend/hono|Hono]] -- Cloudflare-nativ API framework
- [[backend/express|Express]] -- regi Node.js framework (nem fut CF-en)
- Edge function -- miért jó az edge computing
- [[database/drizzle|Drizzle]] -- ORM ami D1-gyel is működik
- [[cloud/railway|Railway]] -- alternativ hosting (Docker, PostgreSQL)
- [[database/supabase|Supabase]] -- alternativ adatbázis platform
- Vercel + Supabase → Cloudflare migrácio -- valós projekt retrospektiv a migráciorol
- [[_moc/moc-deployment|MOC - Deployment]] -- deployment stratégiak
