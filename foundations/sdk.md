---
tags:
  - fejlesztes
  - api
kapcsolodo:
  - "[[database/supabase|Supabase]]"
  - "[[backend/clerk|Clerk]]"
  - "[[database/prisma|Prisma]]"
datum: 2026-02-08
szint: "🧱 Brick"
---

# SDK

## Mi az az SDK?

**SDK = Software Development Kit** -- elore megirt kodcsomagok, amikkel egy szolgáltatast vagy platformot tudsz integralni a saját appodba anelkul, hogy te irnad még az összes alacsony szintű kommunikáciot.

Gondolj ra ugy: egy szolgáltatas (pl. [[backend/clerk|Clerk]], Stripe, [[database/supabase|Supabase]]) ad neked egy REST API-t. Azt te is hivhatnad kezzel `fetch()`-csel, de az SDK **becsomagolja** ezt neked tipusos fuggvenyekbe, error handlinggel, retry logikaval, auth kezelessel.

## Kezi API hivas vs SDK

```ts
// Kezzel -- te kezeled a URL-t, header-oket, hibakat, tipusokat
const res = await fetch('https://api.clerk.com/v1/users/user_123', {
  headers: { Authorization: `Bearer ${secret}` },
})
if (!res.ok) throw new Error('Failed')
const user = await res.json() // tipus: any

// SDK-val -- egy sor, tipusos, hibakezeles benne van
const user = await clerkClient.users.getUser('user_123') // tipus: User
```

## Egy SDK tipikusan mit tartalmaz

- **Client / wrapper osztalyok** -- a REST endpointok fuggvenyekbe csomagolva
- **Tipusdefiniciok** -- TypeScript-ben tudod, mit kapsz vissza
- **Auth kezeles** -- API key, token refresh, session management automatikusan
- **Error handling** -- specifikus hibaosztályok (`NotFoundError`, `RateLimitError`), nem csak generic HTTP status
- **Retry logika** -- ha timeout vagy rate limit van, automatikusan ujraprobalja
- **Framework integracio** -- React hookok, Next.js middleware, [[backend/express|Express]] middleware, stb.
- **Validacio** -- már a hivas elott ellenőrzi, hogy jó parametereket adsz-e még

## Miért használj SDK-t kezi API hivas helyett

- **Kevesebb boilerplate** -- nem irsz URL-eket, header-oket, JSON parse-t
- **Tipusbiztonsag** -- TS autocomplete megmondja mit lehet hivni és mit kapsz vissza
- **Verziókovetes** -- az SDK verziója az API verziójahoz van kotve, nem torik el random
- **Edge case-ek le vannak kezelve** -- pagination, rate limiting, webhook validacio

## Mikor NE használj SDK-t

- **Nincs SDK a te nyelvedhez** -- ilyenkor marad a REST API kezzel
- **Csak 1-2 endpointot hivsz** -- felesleges egy egész SDK-t behuzni, eleg egy `fetch`
- **SDK outdated vagy rosszul karbantartott** -- neha a raw API frissebb
- **Meret szamit** -- ha egy serverless function-ben vagy és a cold start fontos, egy nagy SDK dependency lassithat

## Gyakori SDK-k amiket érdemes ismerni

| Szolgáltatas | SDK csomag              | Mire                 |
| ------------ | ----------------------- | -------------------- |
| Stripe       | `stripe`                | Fizetes              |
| [[database/supabase|Supabase]] | `@supabase/supabase-js` | DB + auth            |
| [[backend/clerk|Clerk]]    | `@clerk/nextjs`         | Auth                 |
| Resend       | `resend`                | Email küldés         |
| OpenAI       | `openai`                | AI API               |
| AWS          | `@aws-sdk/client-s3`    | Cloud szolgáltatasok |
| [[database/prisma|Prisma]]   | `@prisma/client`        | DB ORM (ez is SDK)   |
