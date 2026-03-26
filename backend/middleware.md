---
tags: [backend, koncepció, middleware]
datum: 2026-03-26
szint: "🌱 Newcomer"
kapcsolodo:
  - "[[frontend/nextjs|Next.js]]"
  - "[[backend/edge-function|Edge function]]"
  - "[[backend/express|Express]]"
  - "[[backend/hono|Hono]]"
  - "[[backend/jwt|JWT]]"
  - "[[cloud/nginx|Nginx]]"
  - "[[backend/api-retegek-szintaxis|API rétegek szintaxis]]"
---

# Middleware

**Kategória:** `koncepció` (backend architektúra)

---

## Mi az a middleware?

A **middleware** egy függvény, ami a request és a response **között** fut - elfogja a bejövő kérést, módosíthat rajta, és továbbengedi vagy blokkolja. Egy "szűrő réteg" a kliens és a tényleges üzleti logika között.

> [!tldr]
> **Middleware = köztes réteg.** Request jön be - middleware elfogja - dönt (továbbengedi / redirect / blokkol) - response megy ki. Auth, logging, rate limiting, CORS - mind middleware.

```mermaid
graph LR
    CLIENT["Kliens - böngésző"] --> MW1["Middleware 1 - Auth check"]
    MW1 --> MW2["Middleware 2 - Rate limiting"]
    MW2 --> MW3["Middleware 3 - Logging"]
    MW3 --> HANDLER["Route handler - üzleti logika"]
    HANDLER --> RESPONSE[Response]

    style MW1 fill:#f59e0b,color:black
    style MW2 fill:#f59e0b,color:black
    style MW3 fill:#f59e0b,color:black
```

---

## Middleware pattern-ek

A middleware mindenhol jelen van a webfejlesztésben - de máshol máshogy:

| Platform | Middleware megvalósítás |
|----------|----------------------|
| **Next.js** | `middleware.ts` a projekt gyökerében - edge-en fut |
| **Express** | `app.use()` függvényként - Node.js-ben fut |
| **Hono** | `app.use()` - edge-natív |
| **Django** | `MIDDLEWARE` lista a settings-ben |
| **Nginx** | Konfiguráció direktívák (reverse proxy, SSL) |

---

## Next.js middleware

A [[frontend/nextjs|Next.js]] `middleware.ts` az **edge runtime-on** fut - minden request előtt, a CDN szintjén:

```typescript
// middleware.ts (projekt gyökérben!)
import { NextResponse } from 'next/server'
import type { NextRequest } from 'next/server'

export async function middleware(request: NextRequest) {
  // 1. Auth check
  const token = request.cookies.get('auth-token')?.value

  if (!token && request.nextUrl.pathname.startsWith('/dashboard')) {
    return NextResponse.redirect(new URL('/login', request.url))
  }

  // 2. Request módosítás (header hozzáadás)
  const response = NextResponse.next()
  response.headers.set('x-request-id', crypto.randomUUID())

  return response
}

// Csak ezekre a route-okra fusson
export const config = {
  matcher: ['/dashboard/:path*', '/api/:path*']
}
```

### JWT middleware példa

Egy tipikus példa [[backend/jwt|JWT]] auth middleware-re:

```typescript
// middleware.ts - jose JWT ellenőrzés
import { jwtVerify } from 'jose'

export async function middleware(request: NextRequest) {
  const token = request.cookies.get('auth')?.value

  if (!token) {
    return NextResponse.redirect(new URL('/login', request.url))
  }

  try {
    await jwtVerify(token, new TextEncoder().encode(process.env.JWT_SECRET))
    return NextResponse.next()
  } catch {
    return NextResponse.redirect(new URL('/login', request.url))
  }
}
```

### Edge runtime korlátok

A Next.js middleware edge-en fut - korlátozott környezet:

- Nincs `fs` (fájlrendszer hozzáférés)
- Nincs natív Node.js modulok
- Korlátozott execution time (~25ms [[cloud/vercel|Vercel]]-en)
- Nem minden npm csomag működik (lásd: [[backend/edge-function|Edge function]])

---

## Express middleware

Az [[backend/express|Express]] middleware a klasszikus `(req, res, next)` pattern:

```typescript
import express from 'express'

const app = express()

// 1. Logging middleware
app.use((req, res, next) => {
  console.log(`${req.method} ${req.path}`)
  next() // továbbadja a következő middleware-nek
})

// 2. Auth middleware
function requireAuth(req, res, next) {
  const token = req.headers.authorization?.split(' ')[1]
  if (!token) return res.status(401).json({ error: 'Unauthorized' })

  try {
    req.user = verifyToken(token)
    next()
  } catch {
    res.status(401).json({ error: 'Invalid token' })
  }
}

// 3. Alkalmazás route-ra
app.get('/api/data', requireAuth, (req, res) => {
  res.json({ user: req.user, data: '...' })
})
```

**Express vs Next.js middleware:**

| Szempont | Express | Next.js |
|----------|---------|---------|
| **Fut** | Node.js szerveren | Edge-en (CDN) |
| **Pattern** | `(req, res, next)` | `(request) -> Response` |
| **Láncolás** | `app.use()` sorrendben | Egyetlen `middleware.ts` |
| **Scope** | Route-specifikus vagy globális | `matcher` config-gal |
| **Async** | Callback-based | Native async/await |

---

## Gyakori middleware felhasználások

| Felhasználás | Mit csinál | Lényege |
|---|---|---|
| **Auth check** | Ellenőrzi az autentikációt | `if (!isAuthenticated(request)) return redirect('/login')` |
| **Rate limiting** | IP alapú request limit (Map-ben számolja) | `rateLimiter.get(ip)` - count + resetAt, limit felett blokkol |
| **CORS** | Cross-origin header-ök beállítása | `response.headers.set('Access-Control-Allow-Origin', ...)` |
| **Logging / Request ID** | Unique ID minden requesthez | `response.headers.set('x-request-id', crypto.randomUUID())` |

---

## Middleware sorrend

A middleware-ek **sorrendben** futnak - az első middleware dönt, hogy továbbadja-e:

| Sorrend | Middleware | Ha nem OK |
|---|---|---|
| 1. | Auth | 401 / Redirect |
| 2. | Rate Limit | 429 Block |
| 3. | CORS | Header-ök hozzáadása |
| 4. | Logging | Request logolás |
| 5. | **Handler** | Üzleti logika |

> [!tip] Sorrend fontossága
> Az auth middleware **mindig** a rate limiter előtt legyen - nincs értelme rate limit-elni egy nem autentikált request-et (az auth redirect előbb kell).

---

## AI-natív fejlesztés

A middleware pattern egyszerű és ismétlődő - ideális AI-generálásra. Claude Code-dal másodpercek alatt írhatsz auth middleware-t, rate limiter-t vagy CORS konfigot, és a generált kód általában jó minőségű, mert a pattern jól dokumentált.

> [!tip] Hogyan használd AI-val
> - *"Írj Next.js middleware-t ami a /dashboard/* route-okat védi JWT-vel (jose library), és redirect-el /login-ra ha nincs token"*
> - *"Generálj Hono middleware-t: rate limiting IP-nként, max 100 req/perc, 429-et ad vissza ha túllépi"*
> - *"Adj hozzá CORS middleware-t a Hono app-hoz ami csak a https://myapp.com origin-t engedi"*
