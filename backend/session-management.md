---
tags:
  - auth
  - security
  - backend
datum: 2026-03-06
szint: "🧱 Brick"
kapcsolodo:
  - "[[backend/jwt|JWT]]"
  - "[[backend/clerk|Clerk]]"
  - "[[database/supabase|Supabase]]"
  - "[[database/redis|Redis]]"
  - "[[frontend/nextjs|Next.js]]"
  - "[[_moc/moc-auth|MOC - Auth]]"
---

# Session Management

## Összefoglaló

A **session management** arról szól, hogyan tartod nyilván, hogy egy felhasználó be van-e jelentkezve, és milyen állapotban van a böngésző és a szerver közötti kapcsolat. Minden authentikált webalkalmazás alapja: a user bejelentkezik, és utána minden kérésénél tudnod kell ki ő — anélkül, hogy újra megadná a jelszavát.

## Session vs JWT — mikor melyik?

A [[backend/jwt|JWT]] stateless megközelítést ad: a szerver nem tárol semmit, a token magában hordozza az információt. A **server-side session** ezzel szemben **stateful**: a szerver tárolja a session adatokat, és csak egy session ID-t kap a kliens.

| Szempont | Server-side session | JWT |
|----------|-------------------|-----|
| **Tárolás** | Szerver (DB / [[database/redis|Redis]]) | Kliens (cookie / memory) |
| **Visszavonás** | Azonnali (session törlése) | Nem azonnali (lejáratig él) |
| **Skálázhatóság** | Shared store kell (Redis) | Bármelyik szerver ellenőrizheti |
| **Méret** | Cookie: ~32 byte (session ID) | Cookie: ~1KB+ (teljes token) |
| **Komplexitás** | Egyszerűbb logika | PKCE, refresh token, signature |

```mermaid
sequenceDiagram
    participant B as Böngésző
    participant S as Szerver
    participant R as Redis / DB

    B->>S: POST /login (email + jelszó)
    S->>R: Session létrehozás (session_id → user adatok)
    S->>B: Set-Cookie: session_id=abc123; HttpOnly; Secure; SameSite=Lax
    B->>S: GET /dashboard (Cookie: session_id=abc123)
    S->>R: session_id=abc123 → user adatok lekérés
    R->>S: { userId: 42, role: "admin" }
    S->>B: Dashboard HTML/JSON
```

## Cookie beállítások — a biztonság itt dől el

A session cookie beállításai kritikusak. Egy rosszul konfigurált cookie az egész auth rendszert sebezhetővé teszi.

```typescript
// Next.js API route — session cookie beállítás
import { cookies } from 'next/headers'

export async function POST(req: Request) {
  const { email, password } = await req.json()
  const user = await verifyCredentials(email, password)

  if (!user) {
    return new Response('Invalid credentials', { status: 401 })
  }

  const sessionId = crypto.randomUUID()
  await redis.set(`session:${sessionId}`, JSON.stringify({
    userId: user.id,
    role: user.role,
    createdAt: Date.now(),
  }), { ex: 86400 }) // 24 óra TTL

  const cookieStore = await cookies()
  cookieStore.set('session_id', sessionId, {
    httpOnly: true,     // JS nem éri el → XSS védelem
    secure: true,       // Csak HTTPS-en megy
    sameSite: 'lax',    // CSRF védelem (GET engedélyezett, POST nem cross-origin)
    path: '/',
    maxAge: 86400,      // 24 óra
  })

  return new Response(JSON.stringify({ success: true }))
}
```

### Cookie flag-ek részletesen

| Flag | Mit csinál | Ha hiányzik |
|------|-----------|-------------|
| `httpOnly` | JavaScript nem éri el a cookie-t | XSS támadással ellopható |
| `secure` | Csak HTTPS-en küldi a böngésző | HTTP-n plaintext-ben utazik |
| `sameSite=lax` | Csak same-site POST-nál küldi | CSRF támadás lehetséges |
| `sameSite=strict` | Soha nem küldi cross-site | Külső linkről belépve kijelentkeztet |
| `path=/` | Az egész domain-re érvényes | Csak adott path alatt érvényes |
| `maxAge` | Ennyi másodpercig él | Session cookie (böngésző bezárásig) |

> [!warning] SameSite=None kerülése
> A `SameSite=None` engedélyezi a cross-site cookie küldést — ez pontosan az amit a CSRF támadás kihasznál. Csak akkor használd, ha cross-origin iframe-ben kell működnie az appodnak (pl. embedded widget).

## CSRF védelem

A **CSRF (Cross-Site Request Forgery)** támadásnál egy külső oldal a bejelentkezett felhasználó nevében küld kérést az appodnak — a böngésző automatikusan mellékeli a session cookie-t.

```
1. User bejelentkezik: app.example.com → session cookie
2. User megnyit egy rosszindulatú oldalt: evil.com
3. evil.com-on rejtett form: <form action="https://app.example.com/api/transfer" method="POST">
4. A böngésző elküldi a cookie-t → a szerver azt hiszi a user csinálta
```

### Védekezés

**1. SameSite cookie (alapvető):**

A `SameSite=Lax` vagy `Strict` megoldja a legtöbb esetet — a böngésző nem küldi a cookie-t cross-origin POST kéréseknél.

**2. CSRF token (extra védelem):**

```typescript
// Session létrehozásnál CSRF token generálás
import crypto from 'crypto'

async function createSession(userId: string) {
  const sessionId = crypto.randomUUID()
  const csrfToken = crypto.randomBytes(32).toString('hex')

  await redis.set(`session:${sessionId}`, JSON.stringify({
    userId,
    csrfToken,
  }), { ex: 86400 })

  return { sessionId, csrfToken }
}

// Middleware: CSRF token ellenőrzés POST/PUT/DELETE kéréseknél
async function verifyCsrf(req: Request, sessionData: SessionData) {
  if (['POST', 'PUT', 'DELETE'].includes(req.method)) {
    const csrfHeader = req.headers.get('x-csrf-token')
    if (csrfHeader !== sessionData.csrfToken) {
      return new Response('CSRF token mismatch', { status: 403 })
    }
  }
}
```

```typescript
// Kliens oldal: CSRF token küldése minden mutáló kérésben
fetch('/api/settings', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'X-CSRF-Token': csrfToken, // meta tag-ből vagy API-ból kapott
  },
  body: JSON.stringify({ theme: 'dark' }),
})
```

## Session store választás

| Store | Mikor jó | Hátrány |
|-------|----------|---------|
| **[[database/redis|Redis]]** | Production — gyors, TTL support, cluster | Extra infra |
| **PostgreSQL** | Ha már van DB (pl. [[database/supabase|Supabase]]) | Lassabb mint Redis |
| **Memory (Map)** | Dev/prototípus | Szerver restart = minden session elveszik |
| **Encrypted cookie** | Egyszerű app, kevés session adat | 4KB limit, minden kérésben utazik |

> [!tip] Redis a legjobb választás production-ben
> A [[database/redis|Redis]] pont erre van kitalálva: gyors key-value lookup, beépített TTL (lejárat), és elosztott környezetben is működik. Az Upstash serverless Redis kifejezetten jól működik [[frontend/nextjs|Next.js]] + [[cloud/vercel|Vercel]] stack-kel.

## Session lifecycle

```typescript
// lib/session.ts — teljes session lifecycle
import { Redis } from '@upstash/redis'

const redis = new Redis({
  url: process.env.UPSTASH_REDIS_REST_URL!,
  token: process.env.UPSTASH_REDIS_REST_TOKEN!,
})

const SESSION_TTL = 60 * 60 * 24 // 24 óra

// Létrehozás
export async function createSession(userId: string, role: string) {
  const sessionId = crypto.randomUUID()
  await redis.set(`session:${sessionId}`, JSON.stringify({
    userId, role, createdAt: Date.now(),
  }), { ex: SESSION_TTL })
  return sessionId
}

// Olvasás (minden requestnél)
export async function getSession(sessionId: string) {
  const data = await redis.get<string>(`session:${sessionId}`)
  return data ? JSON.parse(data) : null
}

// Frissítés (sliding expiration)
export async function touchSession(sessionId: string) {
  await redis.expire(`session:${sessionId}`, SESSION_TTL)
}

// Törlés (logout)
export async function destroySession(sessionId: string) {
  await redis.del(`session:${sessionId}`)
}

// Összes session törlése (jelszó változtatás, security event)
export async function destroyAllSessions(userId: string) {
  const keys = await redis.keys(`session:*`)
  for (const key of keys) {
    const data = await redis.get<string>(key)
    if (data && JSON.parse(data).userId === userId) {
      await redis.del(key)
    }
  }
}
```

## Mikor használd / Mikor NE

**Használd server-side session-t:**
- Ha azonnali session visszavonás kell (admin kijelentkeztetés, jelszó csere)
- Egyszerű szerver-rendered webalkalmazásnál
- Ha a session adatok érzékenyek és nem akarod a kliensen tárolni
- Kis-közepes forgalmú alkalmazásoknál ahol a Redis overhead elhanyagolható

**NE használd (van jobb megoldás):**
- Ha [[backend/clerk|Clerk]] vagy [[database/supabase|Supabase]] Auth-ot használsz — ők kezelik a session-t
- Stateless API-hoz (mobil app, microservices) — [[backend/jwt|JWT]] jobb
- Ha nincs szerver-oldali infrastruktúrád (statikus site) — managed auth kell

## Buktatók

- **Session fixation** — bejelentkezés után mindig generálj új session ID-t, ne használd újra a régit
- **Túl hosszú lejárat** — ne legyen 30 napnál hosszabb, és használj sliding expiration-t
- **Session adatok a cookie-ban** — ne tegyél érzékeny adatot a cookie-ba, csak a session ID-t
- **Redis nélküli multi-instance** — ha több szerver instance fut és memory-ben tárolsz session-t, a userek véletlenszerűen ki-bejelentkeznek (sticky session kell vagy shared store)

## Kapcsolódó

- [[backend/jwt|JWT]] — az alternatív, stateless megközelítés
- [[backend/clerk|Clerk]] — managed session kezelés, nem kell saját implementáció
- [[database/supabase|Supabase]] — beépített session kezelés Supabase Auth-tal
- [[database/redis|Redis]] — a session store ideális választása production-ben
- [[frontend/nextjs|Next.js]] — middleware-ben session ellenőrzés minden kérésnél
- [[backend/rbac-patterns|RBAC Patterns]] — role-alapú hozzáférés a session adatok alapján
