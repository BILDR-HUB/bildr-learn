---
tags: [backend, email, api]
datum: 2026-03-26
szint: "🌱 Newcomer"
kapcsolodo:
  - "[[frontend/nextjs|Next.js]]"
  - "[[frontend/react-vs-spa-vs-preact|React]]"
  - "[[backend/hono|Hono]]"
  - "[[backend/stripe|Stripe]]"
  - "[[backend/betterauth|BetterAuth]]"
---

# Resend

**Kategória:** `email` (transactional email service)
**URL:** https://resend.com
**Ár:** Ingyenes tier: 3000 email/hó, Pro: $20/hó (50K email)

---

## Mi ez és mire jó?

A **Resend** egy modern transactional email szolgáltatás - regisztrációs email, jelszó reset, értesítések, számla küldés. A nagy előnye: **React Email** integráció, amivel [[frontend/react-vs-spa-vs-preact|React]] komponensekből építed az email template-eket JSX-ben.

> [!tldr] Egy mondatban
> Email küldés fejlesztőknek - egyszerű API, React-alapú template-ek, és nem kell az AWS SES komplexitásával szórakozni.

---

## Összehasonlítás

| Szempont | **Resend** | AWS SES | SendGrid | Nodemailer |
|----------|-----------|---------|----------|------------|
| **Típus** | Managed API | Managed (AWS) | Managed API | Self-hosted lib |
| **Ár (alap)** | Ingyenes / 3K email | ~$0.10 / 1K email | Ingyenes / 100 email/nap | Ingyenes (infra költség) |
| **Setup idő** | 5 perc | 30-60 perc (sandbox, SES verify) | 15 perc | Változó (SMTP konfig) |
| **Template rendszer** | React Email (JSX) | Nincs (raw HTML) | Drag és drop builder | Nincs |
| **TypeScript DX** | Kiváló | SDK-n keresztül OK | Közepes | Alap |
| **Deliverability** | Jó | Nagyon jó | Jó | Te kezeled (SPF, DKIM) |

> [!tip] Mikor melyiket?
> **Modern TS projekt, gyors setup** - Resend + React Email
> **Masszív volume, árérzékeny** - AWS SES
> **Marketing email is kell** - SendGrid
> **Teljesen self-hosted kell** - Nodemailer + saját SMTP

---

## React Email - miért jó?

Az email HTML hírhedten szörnyű (table layout-ok, inline CSS, 2005-ös megoldások). A **React Email** ezt oldja meg: React komponenseket írsz, és a library rendereli belőle a kompatibilis HTML-t.

| Komponens | Mire való |
|-----------|-----------|
| `<Html>`, `<Head>`, `<Body>` | Alap struktúra |
| `<Section>`, `<Row>`, `<Column>` | Layout (table-ok helyett) |
| `<Text>`, `<Heading>`, `<Link>` | Szöveg elemek |
| `<Button>` | CTA gomb (link-alapú, működik mindenhol) |
| `<Img>` | Kép (hosted URL kell) |

```typescript
// Resend küldés - ennyi az egész
import { Resend } from 'resend'
import { WelcomeEmail } from '@/emails/welcome'

const resend = new Resend(process.env.RESEND_API_KEY)

await resend.emails.send({
  from: 'App <noreply@yourdomain.com>',
  to: user.email,
  subject: 'Üdvözlünk!',
  react: WelcomeEmail({ name: user.name }),
})
```

---

## Setup lépések

| Lépés | Teendő |
|-------|--------|
| 1. Regisztráció | resend.com - API key generálás |
| 2. Domain hozzáadás | DNS rekordok (MX, SPF, DKIM) - Resend dashboard mutatja |
| 3. Telepítés | `bun add resend` + `bun add @react-email/components` |
| 4. API key | `.env` - `RESEND_API_KEY=re_xxxxxxxxx` |
| 5. Email template | `emails/` mappába React Email komponensek |
| 6. Preview | `npx react-email dev` - localhost:3000 preview |

---

## AI-natív fejlesztés

A React Email template generálás tökéletes AI-feladat - leírod milyen emailt akarsz (welcome, invoice, password reset), és Claude Code legenerálja a teljes React Email komponenst a megfelelő layout-tal. A Resend SDK integráció is egyszerű boilerplate, amit az AI gyorsan megír.

> [!tip] Hogyan használd AI-val
> - *"Generálj React Email template-et: welcome email, a user neve dinamikus, CTA gomb a dashboard-ra, minimális modern dizájn"*
> - *"Resend + React Email template - ha email feature-t kérsz, ezt írd, különben az AI raw HTML string-et generál ami email kliensekben szétesik"*
