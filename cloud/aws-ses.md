---
tags:
  - cloud
  - email
datum: 2026-03-26
szint: "🧱 Brick"
kapcsolodo:
  - "[[cloud/cloudflare|Cloudflare]]"
  - "[[cloud/deployment-checklist|Deployment checklist]]"
---

# AWS SES

**Kategória:** `email küldés` / `cloud`
**URL:** https://aws.amazon.com/ses/

---

## Mi ez és mire jó?

> [!tldr] Egy mondatban
> Amazon email küldő szolgáltatás - a legolcsóbb megoldás nagy volumenű tranzakciós és marketing emailek küldéséhez, de a setup bonyolultabb mint a modern alternatíváknál.

Az **AWS SES** (Simple Email Service) az Amazon email infrastruktúrája. Tranzakciós emaileket (regisztráció megerősítés, jelszó reset, számla) és marketing emaileket (newsletter, kampány) is küld. Az AWS ökoszisztéma része, tehát Lambda, SNS, S3-mal könnyen integrálható.

A fő vonzereje az **ár**: $0.10 / 1000 email - ez töredéke a SendGrid vagy Resend árának nagy volumen esetén. A hátránya, hogy az AWS Console-ban kell konfigurálni (domain verifikáció, DKIM, SPF, sending limits kérése), ami bonyolultabb mint egy modern API-first szolgáltatásnál.

---

## Mikor hasznos?

- **Nagy volumen (10K+ email/hó)** - az ár szempontjából verhetetlen
- **AWS-ben fut az app** - natív integráció Lambda, SNS, SQS-sel
- **Tranzakciós email** - user regisztráció, jelszó reset, értesítések
- **Marketing kampányok** - ha már AWS-ben vagy, nem kell külön szolgáltatás

### Mikor NE használd

- **Gyors setup kell** - Resend 5 perc alatt kész, SES napokig tarthat (sandbox mode, sending limit igénylés)
- **Modern DX kell** - SES API régi stílusú, nincs szép dashboard; Resend és SendGrid modern
- **Kevés email** - ha havi pár száz emailt küldesz, a Resend free tier elég és sokkal egyszerűbb
- **Nem AWS-t használsz** - ha [[cloud/cloudflare|Cloudflare]]/Vercel stack-ed van, az AWS hozzáadása felesleges komplexitás

---

## Árazás

| Tétel | Ár |
|-------|-----|
| **Email küldés** | $0.10 / 1000 email |
| **Email fogadás** | $0.10 / 1000 email |
| **Attachment** | $0.12 / GB |
| **Dedicated IP** | $24.95/hó (opcionális, nagy volumenhez) |

> [!info] Ez töredéke a legtöbb email szolgáltatásnak - pl. SendGrid Pro: $89/hó 100K emailre, míg SES-nél ez $10.

---

## AWS SES vs alternatívák

| Szempont | AWS SES | Resend | SendGrid |
|----------|---------|--------|----------|
| **Ár** | $0.10/1K - legolcsóbb | Free: 3K/hó, Pro: $20/hó | Free: 100/nap, Pro: $20/hó |
| **Setup idő** | Napok (sandbox, limit igénylés) | 5 perc | 15 perc |
| **API DX** | Régi stílusú SDK | Modern, React Email support | Jó SDK |
| **Dashboard** | AWS Console (komplex) | Szép, modern | Részletes analytics |
| **Template** | Limitált | React Email - kódból | Drag-and-drop editor |
| **Mikor válaszd** | Nagy volumen, AWS stack | Modern DX, kis-közepes volumen | Marketing + transactional |

---

## AI-natív fejlesztés

Az AWS SES konfiguráció (Terraform, CDK, IAM policy-k) bonyolult, de jól dokumentált - ideális AI-generálási feladat. Claude Code-dal gyorsan készíthetsz SES setup-ot infrastructure-as-code-ként, ami reproducible és verziókezelhető.

> [!tip] Hogyan használd AI-val
> - *"Állíts be AWS SES-t Terraform-mal: domain verifikáció, DKIM, SPF, és egy Lambda ami welcome emailt küld user regisztrációnál"*
> - *"Írj egy Node.js wrapper-t AWS SES-hez ami template-eket kezel és rate limit-et tartja be"*
> - *"Ha nem vagy AWS-ben, inkább kérd Resend-et - egyszerűbb és gyorsabban beállítható"*

---

## Kapcsolódó

- [[cloud/cloudflare|Cloudflare]] - ha Cloudflare stack-ben vagy, Resend jobb választás SES helyett
- [[cloud/deployment-checklist|Deployment checklist]] - email konfiguráció (DKIM, SPF) a deployment checklist része
