---
tags:
  - moc
datum: 2026-03-26
---

# MOC - Cloudflare

> [!tldr]
> Ez a Map of Content összegyűjti a repo összes Cloudflare-hez kapcsolódó jegyzetét. Workers-től a storage-on át a monorepo deploy-ig.

---

## Platform áttekintés

| Jegyzet | Leírás |
|---------|--------|
| [[cloud/cloudflare\|Cloudflare]] | Edge computing platform áttekintés — Workers, D1, R2, Access, $5/hó, belső appokra kiváló |

## Compute

| Jegyzet | Leírás |
|---------|--------|
| [[cloud/nextjs-on-cloudflare-workers\|Next.js on Cloudflare Workers]] | Fullstack Next.js CF Workers-ön — OpenNext adapter, SSR/SSG/ISR, Vercel alternatíva |
| [[cloud/cloudflare-pages\|Cloudflare Pages]] | Statikus és fullstack hosting — Git-integrált deploy, preview URL-ek, Workers integráció |
| [[cloud/cloudflare-browser-rendering\|Cloudflare Browser Rendering]] | Headless böngésző az edge-en — screenshot, scraping, PDF generálás Workers-ből |

## Storage és adatkezelés

| Jegyzet | Leírás |
|---------|--------|
| [[cloud/cloudflare-r2\|Cloudflare R2]] | S3-kompatibilis object storage — zero egress fee, Workers integrációval |
| [[cloud/cloudflare-kv\|Cloudflare KV]] | Edge key-value store — globálisan elosztott, konfig és cache, eventually consistent |
| [[cloud/cloudflare-queues\|Cloudflare Queues]] | Aszinkron üzenetsor — Workers közötti kommunikáció, háttérfeladat feldolgozás |

## Monorepo és architektúra

| Jegyzet | Leírás |
|---------|--------|
| [[cloud/cloudflare-monorepo-mappastruktura\|Cloudflare Monorepo Mappastruktúra]] | Cloudflare-specifikus monorepo szervezés — Workers, Pages, shared packages |

---

## Kapcsolódó note-ok

| Jegyzet | Kapcsolat |
|---------|-----------|
| [[backend/hono\|Hono]] | Edge-natív API framework — CF Workers-ön a legjobb backend választás |
| [[database/drizzle\|Drizzle]] | TypeScript ORM — D1 integrációval, edge-kompatibilis |
| [[cloud/vercel\|Vercel]] | Alternatív hosting — Next.js natív, de drágább |
| [[cloud/domain-es-dns-kezeles\|Domain és DNS kezelés]] | Cloudflare DNS és SSL — gyakran itt kezeled a domain-t is |
| [[backend/edge-function\|Edge function]] | Edge computing koncepció — Workers erre épül |
| [[_moc/moc-monorepo\|MOC - Monorepo]] | Monorepo témájú jegyzetek összefoglalója |

---

## Tanulási útvonal

Ajánlott sorrend ha nulláról kezded:
1. [[cloud/cloudflare\|Cloudflare]] — platform áttekintés, mire jó, mennyibe kerül
2. [[cloud/cloudflare-pages\|Cloudflare Pages]] — statikus site vagy Next.js deploy
3. [[cloud/nextjs-on-cloudflare-workers\|Next.js on Cloudflare Workers]] — fullstack deploy Workers-ön
4. [[cloud/cloudflare-kv\|Cloudflare KV]] — edge cache és konfiguráció
5. [[cloud/cloudflare-r2\|Cloudflare R2]] — fájlok és képek tárolása
6. [[cloud/cloudflare-queues\|Cloudflare Queues]] — aszinkron feladatok, háttérfeldolgozás
7. [[cloud/cloudflare-browser-rendering\|Cloudflare Browser Rendering]] — headless böngésző haladó use case-ekhez
8. [[cloud/cloudflare-monorepo-mappastruktura\|Cloudflare Monorepo]] — ha több Workers/Pages app-ot kezelsz egy repóban

> [!tip] Cloudflare vs Vercel
> **Cloudflare:** olcsóbb ($5/hó), edge-natív, teljes platform (storage, queue, DB). Kevesebb DX, több kontroll.
> **Vercel:** drágább, de Next.js-re optimalizált DX, zero-config deploy. Ha a költség nem szempont, Vercel egyszerűbb.

---

## Hézagok

- [ ] Cloudflare D1 — serverless SQLite adatbázis
- [ ] Cloudflare Access — zero-trust auth belső appokhoz
- [ ] Cloudflare Durable Objects — stateful edge computing
- [ ] Cloudflare AI — ML inference az edge-en
