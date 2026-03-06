---
tags: [backend, auth, security]
datum: 2026-03-03
szint: "🧱 Brick"
kapcsolodo:
  - "[[backend/clerk|Clerk]]"
  - "[[database/supabase|Supabase]]"
  - "[[database/prisma|Prisma]]"
---

## Mi az a JWT?

A **JWT (JSON Web Token)** egy kompakt, onleiro token formatum, amit autentikaciohoz és informáciaatadashoz használnak. A szerver a bejelentkezes utan kiad egy tokent — ezzel a tokennel azonositja magat a kliens minden keresnel, anelkul hogy a szerver session-t tárolnia.

```
Hagyomanyos session:                    JWT:
────────────────────                    ────────────────────────────────
1. User bejelentkezik                   1. User bejelentkezik
2. Szerver session-t hoz letre          2. Szerver JWT-t general es visszakuldi
3. Session ID-t ad vissza cookie-ban    3. Kliens tarolja (cookie / memory)
4. Minden keresnel: szerver             4. Minden keresnel: szerver
   megnezi az adatbazisban                 CSAK a signature-t ellenorzi
   ki ez a session                         (nincs DB hit)
→ Stateful (szerver tarol)              → Stateless (szerver nem tarol)
```

---

## Felepites: header.payload.signature

A JWT 3 Base64URL-kódolt reszbol all, ponttal elvalasztva:

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJ1c2VyXzEyMyIsImVtYWlsIjoidGVzdEBleGFtcGxlLmNvbSIsImlhdCI6MTcwMDAwMDAwMCwiZXhwIjoxNzAwMDAzNjAwfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

### 1. Header

```json
{
  "alg": "HS256",   // algoritmus: HS256 (HMAC) vagy RS256 (RSA)
  "typ": "JWT"
}
```

### 2. Payload (claims)

```json
{
  "sub": "user_123",              // subject: user ID
  "email": "test@example.com",
  "role": "admin",                // custom claim
  "iat": 1700000000,              // issued at (mikor adtak ki)
  "exp": 1700003600               // expiration (mikor jar le — 1 ora)
}
```

> [!warning] A payload NEM titkositott
> A JWT payload csak Base64URL-kódolt, barki dekodolhatja. **Soha ne tegyel bele jelszót, banki adatot, erzekeny infot.** A signature csak azt garantalja, hogy nem módosítottak — de az adatok olvashatoak.

### 3. Signature

```
HMACSHA256(
  base64url(header) + "." + base64url(payload),
  SECRET_KEY
)
```

A szerver a `SECRET_KEY`-vel ellenőrzi hogy a token valoban tole szarmazik-e és nem módosítottak.

---

## Auth flow JWT-vel

```mermaid
sequenceDiagram
    participant Client
    participant Server
    participant DB

    Client->>Server: POST /login (email + jelszo)
    Server->>DB: Ellenorzi a jelszot
    DB-->>Server: OK, user_id = 123
    Server-->>Client: { accessToken, refreshToken }

    Note over Client: Tarolja a tokeneket

    Client->>Server: GET /api/me (Authorization: Bearer <accessToken>)
    Server->>Server: Signature ellenorzes (nincs DB hit!)
    Server-->>Client: { id: 123, email: "..." }
```

---

## Access token + Refresh token

Ket token kell, mert egymas ellen dolgozo követelmenyeket kell teljesiteni:

| | Access Token | Refresh Token |
|---|---|---|
| **Lejarat** | Rovid: 15 perc – 1 ora | Hosszu: 7–30 nap |
| **Hol tarolod** | Memory (JS változó) vagy httpOnly cookie | httpOnly cookie (CSAK) |
| **Mit csinál** | API hivasokhoz azonositja a usert | Új access tokent ker le |
| **Ha ellopjak** | Max 1 ora kar | Sokkal nagyobb probléma |

```
Access token lejar
      ↓
Kliens: POST /auth/refresh (refresh token cookie-ban)
      ↓
Szerver ellenorzi a refresh tokent (DB-ben tarolja!)
      ↓
Uj access token visszakuldese
```

> [!info] Miért tarolja a szerver a refresh tokent?
> A refresh token az egyetlen amit az adatbázisban kell tárolnii — így tudod **visszavonni** (logout, gyanus aktivitas). Az access token visszavonhatatlan amig le nem jar — ezert kell rovid lejarat.

---

## Hol tarold a tokent

```
❌ localStorage          → XSS tamadassal ellopható
❌ sessionStorage        → szinten XSS-vulnerable
✅ httpOnly cookie       → JS nem eri el, CSRF ellen SameSite=Strict ved
✅ Memory (JS valtozo)   → oldal frissitesnel elveszik, de a legbiztonsagosabb
```

**Ajanlott pattern (Next.js):**
- Access token: memory (React state / Zustand) — rovid lejarat, nem kell perzisztencia
- Refresh token: `httpOnly; Secure; SameSite=Strict` cookie — szerver olvassa, JS nem

---

## Algoritmusok

| Algoritmus | Tipus | Mikor használd |
|---|---|---|
| **HS256** | Szimmetrikus (egy titkos kulcs) | Saját backend — egyszerűbb |
| **RS256** | Aszimmetrikus (privat/publikus kulcspar) | Ha több service ellenőrzi (pl. Clerk, Supabase) |
| **ES256** | Elliptic curve, kisebb kulcs | Modern alternativa RS256-ra |

> [!tip] Supabase és Clerk RS256-ot használ
> A [[database/supabase|Supabase]] és a [[backend/clerk|Clerk]] RS256-tal adja ki a tokeneket. A publikus kulcsot a `/.well-known/jwks.json` endpointjukrol lehet lekerni — ezzel barki ellenőrizheti a tokent, de csak ok tudjak kiadni.

---

## JWT a gyakorlatban

### Supabase — automatikus JWT

A [[database/supabase|Supabase]] bejelentkezeskor automatikusan JWT-t ad vissza:

```typescript
const { data } = await supabase.auth.signInWithPassword({ email, password })
// data.session.access_token → JWT
// data.session.refresh_token → refresh token

// Minden supabase kliens call automatikusan Bearer token-t kuld
// A Row Level Security (RLS) a JWT sub claim-je alapjan szur
```

### Clerk — JWT a saját backendhez

A [[backend/clerk|Clerk]] a saját session-jebol JWT-t tud adni a saját backendednek:

```typescript
// Next.js App Router — szerver oldalon
import { auth } from '@clerk/nextjs/server'

export async function GET() {
  const { userId } = await auth()
  if (!userId) return new Response('Unauthorized', { status: 401 })
  // userId a JWT sub claim-bol jon
}

// Sajat backend API hivashoz: Clerk kiad egy JWT-t
const token = await session.getToken()
fetch('/api/custom', {
  headers: { Authorization: `Bearer ${token}` }
})
```

### Saját JWT implementacio (Node.js)

```typescript
import jwt from 'jsonwebtoken'

const SECRET = process.env.JWT_SECRET!

// Token generalas
export function signAccessToken(userId: string) {
  return jwt.sign(
    { sub: userId },
    SECRET,
    { expiresIn: '15m' }
  )
}

// Token ellenorzes
export function verifyToken(token: string) {
  try {
    return jwt.verify(token, SECRET) as { sub: string }
  } catch {
    return null  // lejart vagy ervenytelen
  }
}

// Middleware
export function authMiddleware(req, res, next) {
  const token = req.headers.authorization?.split(' ')[1]
  const payload = token ? verifyToken(token) : null
  if (!payload) return res.status(401).json({ error: 'Unauthorized' })
  req.userId = payload.sub
  next()
}
```

---

## Mikor használd / Mikor NE

**Mikor jó a JWT:**
- Stateless API — a szerver nem tarol session-t (skálázható, több instance)
- Mobilalkalmazas backend — cookie nelkul is működik
- Microservices — service-ek kozott továbbitható, mindegyik ellenőrizheti
- [[backend/clerk|Clerk]] / [[database/supabase|Supabase]] használatákor — ok adják ki, te csak ellenőrzod

**Mikor NE JWT:**
- Ha azonnali visszavonás kell (pl. admin kitilt egy usert) → a kiadott access token lejarataig ervenyes marad
- Egyszerű szerver-rendered webappnal → hagyomanyos session cookie egyszerűbb
- Ha a payload novekszik (sok custom claim) → minden keresnel ott utazik a headerben

---

## Buktatók

- **`exp` nelkuli token** — soha ne adj ki lejarat nelkuli JWT-t
- **Erzekeny adat a payload-ban** — a payload dekodolhato, ne legyen benne jelszó, kartya szam
- **`none` algoritmus** — nehany regi library elfogadta az `alg: none` tokent (signature nelkul). Mindig explicit algoritmus ellenőrzes
- **Clock skew** — ha a szerver és kliens oraja elter, az `exp` ellenőrzes felremehet. `jwt.verify`-nal van `clockTolerance` opcio

---

## Kapcsolodo

- [[backend/clerk|Clerk]] — managed auth, automatikus JWT kiallitas
- [[database/supabase|Supabase]] — Postgres + auth, JWT-vel vedett RLS szabalyok
- [[database/prisma|Prisma]] — az adatbázis reteg ahol a refresh token-eket tarolod
