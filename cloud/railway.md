---
tags:
  - hosting
  - deployment
  - backend
datum: 2026-03-06
szint: "🧱 Scout"
kapcsolodo:
  - "[[cloud/vercel|Vercel]]"
  - "[[database/supabase|Supabase]]"
  - "[[cloud/cloudflare|Cloudflare]]"
  - "[[cloud/docker-alapok|Docker alapok]]"
  - "[[cloud/docker-compose|Docker Compose]]"
  - "[[database/sql-adatbazisok|SQL adatbázisok]]"
  - "[[foundations/git-es-github|Git és GitHub]]"
  - "[[cloud/deployment-checklist|Deployment checklist]]"
  - "[[cloud/hostinger|Hostinger]]"
  - "[[_moc/moc-deployment|MOC - Deployment]]"
---

# Railway

**Kategoria:** `hosting` / `adatbazis` / `backend`
**URL:** https://railway.app
**Ar/Terv:** Hobby ($5/hó kredit + használat alapu) / Pro ($20/hó)

---

## Mi ez és mire jó?

A Railway egy **backend és szolgáltatas hosting platform**, ahol bármit futtathatsz ami [[cloud/docker-alapok|Docker]]-ben megy: backend API, adatbázis, queue, cron job, bármi. Nem serverless mint a Vercel -- itt valodi, folyamatosan futo szervereket kapsz.

**Egyszerűen:** Ha a Vercel a frontendnek valo, a Railway a backendnek és mindennek ami mögötte van.

**Mikor használd:**
- Backend API ami nem fer bele a Vercel serverless limitbe (>60s futas)
- Saját PostgreSQL / MySQL / Redis / MongoDB adatbázis kell
- Hosszan futo folyamatok (AI feldolgozas, video konverzio, queue worker)
- Cron jobok (ütemezett feladatok)
- Ha Supabase helyett saját adatbázis szervert akarsz

**Mikor NE használd:**
- Egyszerű Next.js frontend → arra [[cloud/vercel|Vercel]] jobb
- Ha nem kell backend és Supabase eleg → felesleges komplexitas
- Ha 0 költségvetesed van → az $5/hó minimum megvan

**Alternativak:** Render, Fly.io, DigitalOcean App Platform, Heroku

---

## Deploy flow

```mermaid
graph LR
    A[git push\nGitHub] --> B[Railway\ndetektal]
    B --> C[Build\nNixpacks / Dockerfile]
    C --> D[Deploy]
    D --> E[Futo service\n*.railway.app]
    D --> F[(PostgreSQL\nprivate network)]
```

---

## Setup -- lépésrol lépésre

### 1. Regisztrácio
- railway.app → Sign up GitHub-bal
- Hozz letre egy új projektet

### 2. Service hozzáadasa

Harom modja van:
1. **GitHub repo** → Railway automatikusan buildeli (mint a Vercel)
2. **[[cloud/docker-alapok|Docker]] image** → Ha saját Dockerfile-od van
3. **Template** → Kesz sablonok (PostgreSQL, Redis, stb.) egy kattintassal

### 3. Adatbázis hozzáadasa
- Projekt → "Add Service" → [[database/sql-adatbazisok|PostgreSQL / MySQL]] / Redis / MongoDB
- Railway automatikusan generalja a connection string-et
- Ezt environment változókent megkapod: `DATABASE_URL`

### 4. Environment változók
- Service → Variables ful
- Ide ird be amit az app használ
- A Railway belső valtozóira is hivatkozhatsz: `${{Postgres.DATABASE_URL}}`

---

## Best Practices

### Architektúra / Struktúra

Tipikus Railway projekt felepites:

```
Railway Project
├── API service      ← a backend kodod (GitHub repobol)
├── PostgreSQL       ← adatbazis (Railway template)
├── Redis            ← cache/queue (ha kell)
└── Worker           ← hatterfeladatok (ha kell)
```

- Egy Railway **projekt** = több **service** egyutt
- A service-ek belső hálózaton beszelnek egymassal (mint [[cloud/docker-compose|Docker Compose]])
- Minden service-nek saját env valtozói vannak, de megoszthatók

### Biztonság

- **Ne tedd publikussa az adatbázist** -- csak belső hálózaton keresztul erd el
- A `DATABASE_URL`-t **soha ne commitold** GitHubra
- Használj Railway **private networking**-et: a service-ek `*.railway.internal` cimen erik el egymast
- API kulcsokat mindig env változóba

### Teljesítmény

- Railway-en **mindig fut a szervered** (nem serverless) → nincs cold start
- Ha több keres jon → scale up: több instance, vagy nagyobb gep
- Használj connection pool-t az adatbázishoz (pl. `pgBouncer`)

### Költségoptimalizalas

- Hobby plan: $5/hó kredit (ez sok kis projekthez eleg)
- Csak azert fizetsz amit használsz (CPU, RAM, hálózat, disk)
- Ha nem kell 24/7 futnia → használj **sleep** funkciot (inaktivitas utan elalszik)
- PostgreSQL + kis API = ~$3-7/hó
- Figyelj a RAM használatra -- ez szokott meglepetes lenni

---

## Gyakori mintak / Használati esetek

### 1. Vercel frontend + Railway backend

```
User → Vercel (Next.js) → Railway (Express API) → Railway (PostgreSQL)
```
Amikor a Vercel API routes nem eleg (timeout, websocket, stb.)

### 2. Teljes backend + adatbázis

```
User → Railway (Next.js VAGY Express) → Railway (PostgreSQL + Redis)
```
Mindent egy helyen, nem kell Vercel.

### 3. Cron job / háttér worker

```
Railway (Worker service) → minden ejjel lefut → feldolgoz valamit
```
Pl. email küldés, adat szinkronizalas, report generalas.

---

## Buktatok és hibak amiket elkerülj

- **Port beállítás:** Railway a `PORT` env változót használja -- az appod ezen a porton kell figyeljen. `app.listen(process.env.PORT || 3000)`
- **Build hiba:** Ha nincs `Dockerfile` és Railway nem ismeri fel a projektet → adj hozzá Dockerfile-t vagy `nixpacks.toml`-t
- **Adatbázis törlödik ha törlöd a service-t** -- volume nelkul az adat elveszik. Railway PostgreSQL-nel ez alapbol rendben van, de figyelj ra
- **Alvo szolgáltatas lassu ebredese** -- ha sleep-re van allitva, az első keres 10-30s lehet
- **Költség meglepetes** -- figyeld a dashboardon a usage-ot, kulonosen ha sok RAM-ot használ az app

---

## Hasznos parancsok / kodreszletek

```bash
# Railway CLI telepites
npm i -g @railway/cli

# Bejelentkezes
railway login

# Projekt linkelese a lokalis mappahoz
railway link

# Lokalis futtatas a Railway env valtozokkal
railway run npm run dev

# Deploy
railway up

# Logok
railway logs
```

---

## Hasznos linkek

- Docs: https://docs.railway.app
- Dashboard: https://railway.app/dashboard
- Kozosseg/Discord: https://discord.gg/railway
- Statusz oldal: https://status.railway.app
- Templates: https://railway.app/templates

---

## Kapcsolodo anyagok
- [[cloud/vercel|Vercel]]
- [[database/supabase|Supabase]]
- [[cloud/cloudflare|Cloudflare]] -- edge alternativa: $5/hó serverless, de Docker/PostgreSQL nincs
- [[cloud/docker-alapok|Docker alapok]]
- [[cloud/docker-compose|Docker Compose]]
- [[database/sql-adatbazisok|SQL adatbázisok]]
- [[foundations/git-es-github|Git és GitHub]]
- [[cloud/deployment-checklist|Deployment checklist]]
- [[cloud/hostinger|Hostinger]]
- [[_moc/moc-deployment|MOC - Deployment]]
