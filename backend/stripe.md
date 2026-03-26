---
tags: [backend, payment, api]
datum: 2026-03-26
szint: "🧱 Brick"
kapcsolodo:
  - "[[backend/hono|Hono]]"
  - "[[frontend/nextjs|Next.js]]"
  - "[[backend/middleware|Middleware]]"
  - "[[backend/webhook-verification|Webhook]]"
  - "[[backend/betterauth|BetterAuth]]"
---

# Stripe

**Kategória:** `payment` (fizetés feldolgozás)
**URL:** https://stripe.com
**Ár:** 2.9% + 30c per tranzakció (nincs havi díj)

---

## Mi ez és mire jó?

A **Stripe** a de facto standard payment platform - ha SaaS-t, e-commerce-t vagy bármit építesz ahol pénz mozog, szinte biztosan Stripe-ot fogsz használni. API-first megközelítés, kiváló docs, minden fizetési mód (kártya, bank transfer, Apple/Google Pay).

> [!tldr] Egy mondatban
> Online fizetés feldolgozás fejlesztőknek. Checkout page-et, subscription-öket és egyedi fizetési flow-kat építesz vele - a nehéz rész a **[[backend/webhook-verification|Webhook]] kezelés**, nem az integráció.

---

## Fő koncepciók

| Koncepció | Mire való | Mikor használd |
|-----------|-----------|----------------|
| **Checkout** | Hosted fizetési oldal (Stripe hosztolt) | Leggyorsabb integráció - redirect a Stripe-ra |
| **Payment Intents** | Custom fizetési flow (saját UI) | Ha teljes kontrollt akarsz a fizetési élmény felett |
| **Subscriptions** | Ismétlődő számlázás | SaaS havi/éves csomagok |
| **Customer Portal** | Self-service ügyféloldal | Előfizetés kezelés, kártyacsere, lemondás |
| **Webhooks** | Eseményalapú értesítések | **Mindig kell** - fizetés megerősítés, subscription változás |

---

## Mikor melyik payment megoldás?

| Szempont | **Stripe** | LemonSqueezy | Paddle |
|----------|-----------|--------------|--------|
| **Típus** | Payment processor | Merchant of Record | Merchant of Record |
| **EU ÁFA kezelés** | Neked kell (Stripe Tax segít) | Automatikus | Automatikus |
| **Árazás** | 2.9% + 30c | 5% + 50c | 5% + 50c |
| **Komplexitás** | Közepes-magas | Alacsony | Alacsony |
| **Kontroll** | Teljes | Korlátozott | Korlátozott |
| **Mikor válaszd** | SaaS, e-commerce, custom flow | Indie SaaS, EU-fókusz | SaaS, globális értékesítés |

> [!tip] Ökölszabály
> **Indie SaaS (EU fókusz)** - LemonSqueezy (MoR, nem kell ÁFA-val foglalkozni)
> **Komolyabb SaaS / e-commerce** - Stripe (teljes kontroll, nagyobb ökoszisztéma)
> **Globális digitális termék** - Paddle (MoR + jó nemzetközi lefedettség)

---

## A legfontosabb: Webhook kezelés

A Stripe integráció **nehéz része nem a fizetés indítása, hanem a webhook-ok**. A Stripe nem szinkron módon mondja meg, hogy sikerült-e a fizetés - webhook-on keresztül értesít.

**Kritikus szabályok:**
- **Mindig ellenőrizd a webhook signature-t** - különben bárki küldhet hamis eventeket
- **Idempotens legyen** - ugyanaz az event többször is megérkezhet
- **200-as választ adj gyorsan** - a feldolgozást async csináld, ne tartsd feltartva a Stripe-ot
- **Legalább ezeket kezeld:** `checkout.session.completed`, `invoice.paid`, `customer.subscription.updated`, `customer.subscription.deleted`

```typescript
// Webhook handler minta (Next.js / Hono)
const event = stripe.webhooks.constructEvent(
  body,
  signature,
  process.env.STRIPE_WEBHOOK_SECRET!
)

switch (event.type) {
  case 'checkout.session.completed':
    // Aktiváld a user subscription-jét DB-ben
    break
  case 'customer.subscription.deleted':
    // Deaktiváld a subscription-t
    break
}
```

> [!warning] Webhook-ot mindig kérd!
> Ha payment feature-t kérsz AI-tól, **mindig add hozzá: "Stripe + webhooks"**. Az AI hajlamos kihagyni a webhook részt és csak a checkout-ot generálni - de webhook nélkül nem tudod, hogy sikeres volt-e a fizetés.

---

## AI-natív fejlesztés

A Stripe integráció az egyik legtipikusabb AI-generálási feladat - Claude Code-dal gyorsan felépíthetsz checkout session-t, webhook handler-t és subscription kezelést. A kulcs: mindig kérd a webhook részt is, ne csak a fizetés indítást.

> [!tip] Hogyan használd AI-val
> - *"Stripe Checkout session létrehozás Hono-val + webhook handler ami aktiválja a subscription-t DB-ben - mindkettő kell!"*
> - *"Generálj Stripe Customer Portal integrációt Next.js-hez: billing page ami redirect-el a Stripe portálra"*
> - *"Stripe webhook handler idempotens legyen: ellenőrizd hogy az event-et még nem dolgoztuk-e fel (event ID check DB-ben)"*
