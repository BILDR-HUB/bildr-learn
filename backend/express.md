---
tags: [backend, framework, nodejs]
datum: 2026-03-03
szint: "🧱 Brick"
kapcsolodo:
  - "[[backend/hono|Hono]]"
  - "[[frontend/nextjs|Next.js]]"
  - "[[backend/edge-function|Edge function]]"
  - "[[cloud/railway|Railway]]"
---

# Express

**Kategoria:** `framework` (backend API)
**URL:** https://expressjs.com
**Ar/Terv:** Ingyenes, open source

---

## Mi ez es mire jo?

Az **Express** (vagy Express.js) a **Node.js világ legregebbi es legelterjedtebb web framework-je**. Ha a JavaScript/TypeScript-ben akarsz backend-et irni (API-t, webszervert), akkor ez volt evekig az egyetlen komoly valasztas.

**Milyen problemat old meg:**

Kepzeld el, hogy van egy boltod. A vevok (felhasznalok) bejonnek es kernek dolgokat (HTTP keresek). Valakinek kell fogadni a kereseket, megnezni mit akarnak, es odaadni amit kernek — ez a webszerver. Az Express egy **keret (framework)**, ami megkonnyiti ennek a webszervernek a megirasat.

```mermaid
graph LR
    USER[Felhasznalo<br/>bongeszo / app] -->|HTTP keres<br/>GET /api/users| EXPRESS[Express szerver<br/>Node.js-ben fut]
    EXPRESS -->|lekerdezes| DB[(Adatbazis)]
    DB -->|adat| EXPRESS
    EXPRESS -->|HTTP valasz<br/>JSON adat| USER

    style EXPRESS fill:#000,color:white
```

**Express nelkul** a Node.js-ben igy nez ki egy szerver:

```javascript
// Nyers Node.js — bonyolult, alacsony szintu
const http = require('http')
const server = http.createServer((req, res) => {
  if (req.method === 'GET' && req.url === '/api/users') {
    res.writeHead(200, { 'Content-Type': 'application/json' })
    res.end(JSON.stringify([{ name: 'User' }]))
  } else {
    res.writeHead(404)
    res.end('Not Found')
  }
})
server.listen(3000)
```

**Express-szel** ugyanez:

```javascript
// Express — egyszeru, olvashato, kevesebb kod
const express = require('express')
const app = express()

app.get('/api/users', (req, res) => {
  res.json([{ name: 'User' }])
})

app.listen(3000)
```

> [!tldr] Egy mondatban
> Az Express egy Node.js library ami megkonnyiti HTTP keresek fogadasat es valaszolasát — azaz API-k es webszerverek epiteset.

**Mikor hasznald:**

- Ha sajat Node.js backend API-t irsz (pl. [[cloud/railway|Railway]]-re deployolva)
- Ha tanulni akarsz backend fejlesztest — ez a klasszikus kiindulopont
- Regi projektek karbantartasa — rengeteg letezo projekt Express-re epul

**Mikor NE hasznald:**

- Ha [[frontend/nextjs|Next.js]]-t hasznalsz — abban mar van beepitett API Routes, nem kell Express melle
- Ha Cloudflare Workers-re vagy edge-re deployolsz — ott az Express NEM fut (lasd: [[backend/hono|Hono]])
- Uj projekteknel ahol fontos a teljesitmeny es a meret — modernebb alternativak jobbak

**Alternativak:**

[[backend/hono|Hono]] (edge-nativ, modernebb), Fastify (gyorsabb Express), Koa (Express keszitok ujragondolt verzioja), [[frontend/nextjs|Next.js]] API Routes (ha mar Next.js-t hasznalsz)

---

## A "middleware" koncepcio

Az Express legnagyobb innovacioja a **middleware pattern**. Ez az az otlet, amire az osszes tobbi framework (Hono, Next.js middleware, stb.) is epul.

**Mi a middleware?**

Egy middleware egy fuggveny, ami **a keres es a valasz kozott ul** — mint egy szuro vagy ellenorzopont. Tobb middleware egymas utan fut, mint egy futoszalagon:

```mermaid
graph LR
    REQ[HTTP keres] --> MW1[1. Logger<br/>naploz] --> MW2[2. Auth check<br/>be van jelentkezve?] --> MW3[3. Route handler<br/>visszaadja az adatot] --> RES[HTTP valasz]

    style MW1 fill:#333,color:white
    style MW2 fill:#333,color:white
    style MW3 fill:#000,color:white
```

```javascript
// Middleware pelda
const express = require('express')
const app = express()

// 1. middleware: minden kerest logol
app.use((req, res, next) => {
  console.log(`${req.method} ${req.url}`)
  next() // "next()" = menjen tovabb a kovetkezo middleware-re
})

// 2. middleware: auth ellenorzes
app.use('/api', (req, res, next) => {
  const token = req.headers.authorization
  if (!token) return res.status(401).json({ error: 'Nincs token' })
  next()
})

// 3. route handler (vegso middleware)
app.get('/api/users', (req, res) => {
  res.json([{ name: 'User' }])
})
```

> [!tip] Miert fontos ezt erteni?
> A middleware pattern **mindenhol visszakoszon**: [[frontend/nextjs|Next.js]] middleware, [[backend/hono|Hono]] middleware, Cloudflare Workers — mind ugyanezt a gondolkodast hasznalja. Ha Express-ben megerted, mindenhol tudod alkalmazni.

---

## Miert nem az elsodleges valasztas uj projekteknel?

Az Express 2010-ben jelent meg, es azota a világ sokat valtozott:

| Problema | Miert gond |
|----------|-----------|
| **Nem fut edge-en** | Az Express Node.js-re epul — Cloudflare Workers, Deno, Bun nem mind tamogatja nativan |
| **Nagy meret** | ~2MB + fuggosegek — edge function-okhoz tul nagy |
| **Nincs TypeScript support** | Tipusok utolag lettek hozzaadva, nem TypeScript-first |
| **Lassu** | Modern alternativak (Hono, Fastify) 2-5x gyorsabbak |
| **Elavult async kezeles** | Callback-alapu, a modern async/await nem nativ benne |

**Ezert jott letre a [[backend/hono|Hono]]** — ugyanazt csinalja mint az Express, de modern, gyors, es mindenhol fut (Cloudflare, Deno, Bun, Node.js).

---

## Kapcsolodo

- [[backend/hono|Hono]] — a modern, edge-nativ Express alternativa
- [[frontend/nextjs|Next.js]] — fullstack framework beepitett API route-okkal
- [[backend/edge-function|Edge function]] — mi az edge es miert fontos
- [[cloud/railway|Railway]] — ha megis Express-t deployolsz
