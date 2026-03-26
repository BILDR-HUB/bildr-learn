---
tags:
  - hosting
  - vps
  - self-hosted
datum: 2026-03-26
szint: "🌱 Newcomer"
kapcsolodo:
  - "[[cloud/coolify|Coolify]]"
  - "[[cloud/docker-alapok|Docker]]"
  - "[[cloud/deployment-checklist|Deployment checklist]]"
  - "[[cloud/vercel|Vercel]]"
---

# Hetzner

> [!tldr] Mi ez és mire jó?
> Német cloud provider - megbízható, **olcsó** VPS és dedikált szerverek. EU adatközpontok (Falkenstein, Nürnberg, Helsinki), GDPR-barát. A self-hosting arany standardja.

## Fő termékek

| Termék | Ártól | Mire jó |
|---|---|---|
| **Cloud Server (VPS)** | ~EUR3.79/hó | Web app, API, [[cloud/docker-alapok|Docker]] workload |
| **Dedicated Server** | ~EUR39/hó | Nagy teljesítmény, sok erőforrás |
| **Object Storage** | ~EUR5/TB/hó | Fájlok, backupok, statikus tartalom |
| **Load Balancer** | ~EUR5.39/hó | Forgalomeloszlás több szerver közt |
| **Volumes** | ~EUR0.052/GB/hó | Persistent block storage |
| **Firewalls** | Ingyenes | Alap hálózati védelem |

## Cloud provider összehasonlítás

| | Hetzner | DigitalOcean | AWS/GCP |
|---|---|---|---|
| **Ár** | Legolcsóbb | Közepes | Drága |
| **Régió** | EU (+ US) | Globális | Globális |
| **Komplexitás** | Egyszerű | Egyszerű | Komplex |
| **Self-hosting** | Ideális | Jó | Overkill |
| **GDPR** | Natív EU | US-cég | US-cég |
| **Célközönség** | Indie dev, KKV | Startupok | Enterprise |

## A legjobb kombó: Hetzner + Coolify

Ez gyakorlatilag a **saját [[cloud/vercel|Vercel]]-ed** havi EUR5-10-ért:

1. Hetzner Cloud VPS (CX22 - 2 vCPU, 4 GB RAM, ~EUR5.39/hó)
2. [[cloud/coolify|Coolify]] telepítve rá - push-to-deploy, SSL, domain kezelés
3. Docker - minden app konténerben fut

Ez a setup kezeli a legtöbb kis-közepes projektet.

## Mikor válaszd

- **Self-hosted appok** - ha nem akarsz Vercel/Netlify árazást
- **Docker workloadok** - Coolify-val egyszerű deployment
- **EU compliance** - GDPR-barát, adatok EU-ban maradnak
- **Budget-tudatos projektek** - 3-5x olcsóbb, mint a nagy cloud providerek

## Mikor NE válaszd

- **Serverless/edge** kell - erre jobb a [[cloud/vercel|Vercel]] vagy [[cloud/cloudflare|Cloudflare]]
- **Managed szolgáltatások** kellenek (managed DB, queue, stb.) - AWS/GCP erősebb
- **Globális edge CDN** - Hetzner erre nem fókuszál

---

## AI-natív fejlesztés

A Hetzner VPS setup (SSH konfig, Docker telepítés, Coolify install) jól automatizálható AI-val. Claude Code-dal lépésről lépésre végigmehetsz a szerver beállításon, és a Dockerfile + docker-compose generálás is tipikus AI-feladat.

> [!tip] Hogyan használd AI-val
> - *"Ha AI-val deployolsz, add meg: 'Hetzner VPS + Coolify + Docker'. A szerverre SSH-val csatlakozol, Coolify-t telepíted, és utána git push-ra megy a deploy."*
> - *"Hetzner VPS initial setup: UFW firewall szabályok, Docker telepítés, Coolify install - lépésről lépésre"*

## Kapcsolódó

- [[cloud/docker-alapok|Docker]] - Hetzner VPS-en futtatott Docker környezet
- [[cloud/deployment-checklist|Deployment checklist]] - deployment Hetzner szerverre
- [[cloud/coolify|Coolify]] - self-hosted PaaS Hetzner VPS-en
