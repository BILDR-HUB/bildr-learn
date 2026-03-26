---
tags:
  - architektura
  - dontes
  - guides
datum: 2026-03-26
szint: "🏗️ Builder"
kapcsolodo:
  - "[[frontend/nextjs|Next.js]]"
  - "[[cloud/vercel|Vercel]]"
  - "[[toolbox/claude-code-projekt-setup|Claude Code]]"
  - "[[cloud/12-faktoros-alkalmazas-epites|12 faktoros alkalmazás építés]]"
  - "[[cloud/deployment-checklist|Deployment checklist]]"
---

# Tech stack döntések - hogyan NE bonyolítsd túl

> [!tldr] Miért releváns
> Minden fejlesztőnek van egy "shiny object syndrome" pillanata, amikor a landing page-hez Kubernetes-t akar. A tech stack kiválasztás a legfontosabb architekturális döntés - és a legtöbb projekt **kevesebb réteggel jobban jár**.

---

## Kulcs tanulságok

- A tech stack = a technológiák összessége amivel egy alkalmazás fut (frontend, backend, database, hosting, auth, stb.)
- **Minden réteg komplexitást ad** - és a komplexitás az, ami megöli a projekteket, nem a rossz technológia-választás
- **A startupok nem az under-engineering-től halnak meg, hanem attól hogy nem shippelnek** - és az over-engineering a shipping legfőbb ellensége
- Ha ketten vagytok és MVP-t építetek, a "boring tech" ([[frontend/nextjs|Next.js]] + Postgres + [[cloud/vercel|Vercel]]) szinte mindig a jó válasz

---

## A rétegek - az egyszerűtől a túlbonyolítottig

| Réteg | Mi ez | Egyszerű választás | Over-engineered választás |
|-------|-------|-------------------|--------------------------|
| **Frontend** | Amit a felhasználó lát | HTML/CSS, Next.js | Micro-frontend-ek, custom design system |
| **Backend** | Üzleti logika, API | Next.js API routes, [[backend/hono|Hono]] | Microservices, event-driven architecture |
| **Database** | Adattárolás | PostgreSQL, SQLite | Polyglot persistence (3 különböző DB) |
| **Auth** | Felhasználó azonosítás | Clerk, BetterAuth | Saját OAuth2 + SAML implementáció |
| **Hosting** | Hol fut | Vercel, Railway | Kubernetes cluster 3 availability zone-ban |
| **CI/CD** | Build és deploy | GitHub Actions (alap) | Jenkins + ArgoCD + custom pipeline |
| **Monitoring** | Mi történik élesben | Vercel Analytics, PostHog | Grafana + Prometheus + Jaeger + ELK stack |
| **Caching** | Gyorsítás | Vercel CDN, ISR | Redis + Varnish + custom invalidation |

### A progresszió

1. **Statikus HTML** - kész, működik, 0 komplexitás
2. **+ CSS framework** - szép is, kezelhető
3. **+ JavaScript framework** - interaktív, de most build tool kell
4. **+ Backend API** - adatok kellenek, szerver kell
5. **+ Database** - most ORM kell, migration kell, backup kell
6. **+ Auth** - session management, token refresh, security audit
7. **+ Caching layer** - Redis, invalidation logic
8. **+ Microservices** - most service discovery kell, API gateway, message queue
9. **+ Kubernetes** - most cluster management kell, helm charts, monitoring
10. **+ A csapat felét DevOps-ra költöd** - a funkció fejlesztés megáll

---

## Döntési szempontok

| Kérdés | Ha igen -> egyszerűbb | Ha igen -> komplexebb |
|--------|----------------------|---------------------|
| 1-3 fős csapat? | **Igen** -> monolith, managed services | |
| MVP / validáció fázis? | **Igen** -> a legkevesebb réteg ami működik | |
| >10K egyidejű felhasználó? | | **Igen** -> caching, CDN, esetleg horizontális skálázás |
| Több csapat dolgozik rajta? | | **Igen** -> microservices megfontolandó |
| Compliance követelmények (SOC2, GDPR)? | | **Igen** -> dedikált infra, audit log, encryption at rest |
| Már van működő egyszerű stack? | **Igen** -> ne írd át amíg nem fáj | |

> [!warning] A legfontosabb szabály
> Ne írd át a stack-et amíg nem fáj. Az over-engineering nem abból fakad, hogy rossz technológiát választottál - hanem abból, hogy **túl sok réteget** adtál hozzá túl korán.

---

## A [[toolbox/claude-code-projekt-setup|Claude Code]] és a tech stack

A tech stack az egyik legfontosabb kontextus amit az AI-nak meg kell adnod. Ha Claude Code tudja a stack-edet, **sokkal jobb kódot generál** - mert érti az összefüggéseket a rétegek között.

> [!tip] Gyakorlati tanács
> A CLAUDE.md fájlban érdemes egy "Tech Stack" szekciót tartani. Például: "Next.js 15 (App Router) + Drizzle + PostgreSQL (Neon) + Clerk auth + Vercel hosting" - ez minden session elején kontextust ad az AI-nak.

---

## AI-natív fejlesztés

A tech stack döntés ma már nem csak a csapat tudásáról szól - az AI tool-ok támogatottsága is szempont. A "boring tech" (Next.js, PostgreSQL, Tailwind, shadcn/ui) nem csak bevált, hanem az AI coding tool-ok is a legjobban ezeket a technológiákat ismerik. Claude Code-dal az egész stack-et felépítheted és karbantarthatod.

> [!tip] Hogyan használd AI-val
> - *"Ajánlj tech stack-et ehhez a projekthez: [leírás] - MVP fázis, 2 fejlesztő, 3 hónap deadline"*
> - *"Írd meg a CLAUDE.md Tech Stack szekciót a jelenlegi projekthez, hogy minden AI session tudja a kontextust"*
> - *"Értékeld ennek a projektnek a stack-jét: túl komplex? Mit egyszerűsíthetnénk?"*

---

## Kapcsolódó

- [[frontend/nextjs|Next.js]] - a "default" full-stack framework, ami a legtöbb projektnél elég
- [[cloud/vercel|Vercel]] - managed hosting, ami a deploy réteget triviálissá teszi
- [[toolbox/claude-code-projekt-setup|Claude Code]] - CLAUDE.md-ben a stack leírás az AI kontextus alapja
- [[cloud/12-faktoros-alkalmazas-epites|12 faktoros alkalmazás építés]] - architekturális elvek amiket érdemes követni
- [[cloud/deployment-checklist|Deployment checklist]] - mikor milyen infrastruktúra kell
