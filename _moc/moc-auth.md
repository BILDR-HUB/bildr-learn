# MOC - Auth

> [!tldr]
> Ez a Map of Content összegyűjti az összes autentikációhoz és authorizációhoz kapcsolódó jegyzetet. Alapelvektől a managed szolgáltatásokig.

---

## Alapelvek

| Note | Leírás |
|------|--------|
| [[backend/jwt|JWT]] | JSON Web Token működése: stateless autentikáció, header.payload.signature felépítés, session vs JWT összehasonlítás |
| [[backend/oauth-2-0|OAuth 2.0]] | Authorization Code Flow, PKCE, refresh token-ek — hogyan működik a "Login with Google" |
| [[backend/session-management|Session Management]] | Server-side session-ök, cookie beállítások (httpOnly, SameSite), CSRF védelem |
| [[backend/rbac-patterns|RBAC Patterns]] | Role-Based Access Control tervezési minták: flat, hierarchikus, ABAC hybrid |

## Managed szolgáltatások

| Note | Leírás |
|------|--------|
| [[backend/clerk|Clerk]] | Authentication-as-a-Service — Next.js/React integrációval, multi-tenant support, pre-built UI komponensek |
| [[backend/auth-js-nextauth|Auth.js (NextAuth)]] | Open source auth library — 80+ OAuth provider, saját DB-ben user adatok, nincs vendor lock-in |
| [[backend/betterauth|BetterAuth]] | Open-source, self-hosted auth framework — plugin-alapú, framework-agnosztikus, a te DB-d és adataid |

## Service és API auth

| Note | Leírás |
|------|--------|
| [[backend/api-key-management|API Key Management]] | Service-to-service autentikáció, API key generálás, rotation, scope-ok, rate limiting |
| [[backend/webhook-verification|Webhook Verification]] | Bejövő webhook-ok hitelesítése — Stripe, GitHub, Clerk webhook signing (HMAC-SHA256) |
| [[backend/ai-agent-authentication|AI Agent Authentication]] | AI agent-ek auth-olása API-khoz: MCP server auth, user-delegated token, Device Authorization Grant |

---

## Fizetés és auth

| Note | Leírás |
|------|--------|
| [[backend/stripe|Stripe]] | Online fizetés feldolgozás — Checkout, subscriptions, webhook kezelés, auth flow-ba integrálva |

---

## Kapcsolódó note-ok

| Note | Kapcsolat |
|------|-----------|
| [[database/supabase|Supabase]] | Beépített Auth modul: email/jelszó, OAuth, magic link — alternatíva a Clerk-kel szemben |
| [[database/redis|Redis]] | Session store, rate limiting, API key validáció cache |
| [[foundations/halozatok-es-ip-cimek|Halozatok es IP cimek]] | HTTPS és biztonságos kommunikáció az auth flow-ban |
| [[toolbox/claude-agent-sdk|Claude Agent SDK]] | MCP tool-ok és agent orchestráció — AI agent auth kontextus |

---

## Tanulási útvonal

Ajánlott sorrend ha nulláról kezded:
1. [[backend/jwt|JWT]] — először értsd meg hogyan működik a token-alapú autentikáció: mit tartalmaz egy JWT, miért stateless, mi a signature szerepe
2. [[backend/session-management|Session Management]] — az alternatív megközelítés: server-side session-ök, cookie-k, CSRF védelem
3. [[backend/oauth-2-0|OAuth 2.0]] — hogyan működik a "Login with Google" a háttérben (Authorization Code Flow, PKCE)
4. [[backend/clerk|Clerk]] — ha érted az alapokat, a Clerk leveszi a terhet: production-ready auth percek alatt Next.js-ben
5. [[backend/auth-js-nextauth|Auth.js (NextAuth)]] — open source alternatíva ha vendor lock-in nem opció
6. [[backend/betterauth|BetterAuth]] — self-hosted, plugin-alapú auth: ha a te DB-dben akarod az adatokat
7. [[backend/rbac-patterns|RBAC Patterns]] — jogosultság-kezelés: ki mit érhet el az alkalmazásodban
8. [[backend/api-key-management|API Key Management]] — service-to-service auth: API key-ek generálása, rotation, scope-ok
9. [[backend/stripe|Stripe]] — fizetési integráció: Checkout, subscription, webhook verification
10. [[backend/ai-agent-authentication|AI Agent Authentication]] — haladó: hogyan auth-olj AI agent-eket

> [!tip] Clerk vs Supabase Auth vs Auth.js vs BetterAuth
> **Clerk:** ha Next.js SaaS-t építesz, multi-tenant kell, és fontos a pre-built UI.
> **Supabase Auth:** ha már Supabase-t használsz adatbázisnak, és egy helyen akarod kezelni mindent.
> **Auth.js:** ha open source kell, vendor lock-in nem opció, és a user adatokat a saját DB-dben akarod.
> **BetterAuth:** ha self-hosted, plugin-alapú rendszert akarsz — teljes kontroll, framework-agnosztikus.

---

## Hézagok

- [x] OAuth 2.0 deep dive → [[backend/oauth-2-0|OAuth 2.0]] — authorization code flow, PKCE, refresh token-ek részletesen
- [x] Session management → [[backend/session-management|Session Management]] — server-side session-ök, cookie beállítások, CSRF védelem
- [x] RBAC patterns → [[backend/rbac-patterns|RBAC Patterns]] — Role-Based Access Control tervezési minták
- [x] Auth.js / NextAuth → [[backend/auth-js-nextauth|Auth.js (NextAuth)]] — open source alternatíva Clerk-kel szemben
- [x] API key management → [[backend/api-key-management|API Key Management]] — hogyan kezeld a service-to-service autentikációt
- [x] AI agent autentikáció → [[backend/ai-agent-authentication|AI Agent Authentication]] — hogyan auth-olj AI agent-eket API-khoz (tool use, MCP server auth)
- [x] Webhook verification → [[backend/webhook-verification|Webhook Verification]] — bejövő webhook-ok hitelesítése (Stripe, GitHub, Clerk webhook signing)
