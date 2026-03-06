# MOC - Auth

> [!tldr]
> Ez a Map of Content összegyűjti az összes autentikációhoz és authorizációhoz kapcsolódó jegyzetet. Alapelvektől a managed szolgáltatásokig.

---

## Alapelvek

| Note | Leírás |
|------|--------|
| [[backend/jwt|JWT]] | JSON Web Token működése: stateless autentikáció, header.payload.signature felépítés, session vs JWT összehasonlítás |

## Managed szolgáltatások

| Note | Leírás |
|------|--------|
| [[backend/clerk|Clerk]] | Authentication-as-a-Service — Next.js/React integrációval, multi-tenant support, pre-built UI komponensek |

---

## Kapcsolódó note-ok

| Note | Kapcsolat |
|------|-----------|
| [[database/supabase|Supabase]] | Beépített Auth modul: email/jelszó, OAuth, magic link — alternatíva a Clerk-kel szemben |
| [[foundations/halozatok-es-ip-cimek|Halozatok es IP cimek]] | HTTPS és biztonságos kommunikáció az auth flow-ban |

---

## Tanulási útvonal

Ajánlott sorrend ha nulláról kezded:
1. [[backend/jwt|JWT]] — először értsd meg hogyan működik a token-alapú autentikáció: mit tartalmaz egy JWT, miért stateless, mi a signature szerepe
2. [[backend/clerk|Clerk]] — ha érted az alapokat, a Clerk leveszi a terhet: production-ready auth percek alatt Next.js-ben

> [!tip] Clerk vs Supabase Auth
> **Clerk:** ha Next.js SaaS-t építesz, multi-tenant kell, és fontos a pre-built UI.
> **Supabase Auth:** ha már Supabase-t használsz adatbázisnak, és egy helyen akarod kezelni mindent.

---

## Hézagok

- [ ] OAuth 2.0 deep dive — authorization code flow, PKCE, refresh token-ek részletesen
- [ ] Session management — server-side session-ök, cookie beállítások, CSRF védelem
- [ ] RBAC patterns — Role-Based Access Control tervezési minták
- [ ] Auth.js / NextAuth — open source alternatíva Clerk-kel szemben
- [ ] API key management — hogyan kezeld a service-to-service autentikációt
- [ ] AI agent autentikáció — hogyan auth-olj AI agent-eket API-khoz (tool use, MCP server auth)
- [ ] Webhook verification — bejövő webhook-ok hitelesítése (Stripe, GitHub, Clerk webhook signing)
