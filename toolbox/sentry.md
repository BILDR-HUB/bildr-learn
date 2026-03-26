---
tags:
  - eszkoz
  - monitoring
datum: 2026-03-26
szint: "🧱 Scout"
kapcsolodo:
  - "[[frontend/nextjs|Next.js]]"
  - "[[toolbox/posthog|PostHog]]"
  - "[[toolbox/grafana|Grafana]]"
  - "[[cloud/vercel|Vercel]]"
  - "[[backend/hono|Hono]]"
  - "[[cloud/cloudflare|Cloudflare]]"
---

# Sentry

**Kategória:** `error tracking` / `monitoring` (runtime hibák + performance)
**URL:** https://sentry.io
**Ár:** Developer: ingyenes (5K errors/hó, 10K performance units), Team: $26/hó (50K errors)

---

## Mi ez és mire jó?

A **Sentry** automatikusan elkapja a runtime hibákat - ha a production-ben valami eltörik, azonnal látod: mi történt, melyik kódsornál, mi volt a user kontextusa, milyen lépések vezettek oda. Nem kell a user-re várni hogy jelezze.

A Sentry a "produkciós debugger" - elkapja a hibákat mielőtt a user jelezné, megmutatja a stack trace-t, breadcrumb-okat és user kontextust, és alertet küld Slack-re/email-re.

### Fő funkciók

| Funkció | Mire jó |
|---------|---------|
| **Error tracking** | Automatikus hiba elkapás, stack trace, csoportosítás |
| **Breadcrumbs** | Mi történt a hiba előtt (kattintások, navigáció, API hívások) |
| **Release tracking** | Melyik deploy hozta be a hibát? Regression detection |
| **Performance** | Web Vitals (LCP, FID, CLS), slow transaction-ök |
| **Session Replay** | Videószerű visszajátszás a hiba pillanatáról |
| **Alerts** | Slack/email/webhook értesítés új vagy ismétlődő hibákra |
| **Source Maps** | Minified kód - eredeti [[foundations/typescript-vs-python|TypeScript]] sorok |

---

## Összehasonlítás

| Szempont | **Sentry** | LogRocket | Bugsnag | [[toolbox/grafana|Grafana]] |
|----------|-----------|-----------|---------|-------------|
| **Fókusz** | Error tracking + perf | Session replay + errors | Error tracking (egyszerűbb) | Infra [[cloud/monitoring-es-logging|Monitoring]] (metrikák) |
| **Típus** | Runtime hibák | User experience | Runtime hibák | Server metrikák |
| **Free tier** | 5K errors/hó | 1K sessions/hó | 7.5K events/hó | Self-hosted ingyenes |
| **Session Replay** | Van (kiegészítő) | Fő funkció | Nincs | Nincs |
| **Performance** | Web Vitals + tracing | Limitált | Nincs | Prometheus metrikák |
| **Legjobb erre** | Fullstack error tracking | UX debugging | Kis projektek | Szerver/infra |

> [!tip] Sentry + PostHog + Grafana = teljes kép
> - **Sentry** - runtime hibák ("mi tört el?")
> - **[[toolbox/posthog|PostHog]]** - user viselkedés ("mit csinálnak a userek?")
> - **[[toolbox/grafana|Grafana]]** - infra metrikák ("hogyan teljesít a szerver?")

---

## Integráció döntési fa

```
Milyen stack-ed van?
├── Next.js → @sentry/nextjs (automatic instrumentation)
│   └── withSentryConfig() a next.config.js-ben
├── React SPA → @sentry/react (ErrorBoundary komponens)
├── Node.js / Express → @sentry/node
├── Hono (Cloudflare Workers) → @sentry/cloudflare
└── Vercel deployment → Vercel Integration (1-click setup)
```

---

## Setup - [[frontend/nextjs|Next.js]] (leggyakoribb)

```bash
npx @sentry/wizard@latest -i nextjs
```

Ez automatikusan:
- Létrehozza a `sentry.client.config.ts` és `sentry.server.config.ts` fájlokat
- Konfigurálja a `next.config.js`-t source map upload-dal
- Beállítja a `instrumentation.ts`-t
- `.env`-be teszi a DSN-t

| Lépés | Teendő |
|-------|--------|
| 1. Wizard | `npx @sentry/wizard@latest -i nextjs` |
| 2. DSN | Automatikusan bekerül `.env.local`-ba |
| 3. Source Maps | Automatikus upload build-nél |
| 4. Teszt | `throw new Error("Sentry test")` - megjelenik a dashboardon |
| 5. Alerts | Sentry dashboard - Alerts - Slack integration |

---

## Mikor használd (és mikor nem)?

**Igen:**
- Bármilyen production app ahol user-ek vannak
- Ha nem akarsz a konzolban debugolni production hibákat
- Ha tudni akarod melyik deploy rontott el valamit

**Nem kell:**
- Prototípus / early MVP (még nincs user)
- Pure API monitoring - [[toolbox/grafana|Grafana]] jobb
- Ha 5K error/hó free tier elég és nem akarsz fizetni

---

## Hogyan használd promptban?

- *"Add Sentry to my Next.js app - automatic error tracking + source maps"*
- *"Sentry alert: Slack notification ha 5 percen belül 10+ ugyanolyan error jön"*
- *"Custom Sentry context: user ID és subscription plan hozzáadása minden error-hoz"*

> [!tip] Sentry wizard-ot jelezd!
> Ha Sentry setup-ot kérsz, írd: **"use the Sentry wizard (`npx @sentry/wizard`)"** - különben az AI manuálisan konfigurálja, ami 3x annyi kód és könnyen kimarad a source map upload.
