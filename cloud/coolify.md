---
tags:
  - hosting
  - self-hosted
  - docker
datum: 2026-03-26
szint: "🧱 Scout"
kapcsolodo:
  - "[[cloud/docker-alapok|Docker]]"
  - "[[cloud/docker-compose|Docker Compose]]"
  - "[[cloud/hetzner|Hetzner]]"
  - "[[cloud/traefik|Traefik]]"
  - "[[cloud/vercel|Vercel]]"
  - "[[cloud/railway|Railway]]"
  - "[[cloud/deployment-checklist|Deployment checklist]]"
---

# Coolify

**Kategória:** `hosting` (self-hosted PaaS)
**URL:** https://coolify.io
**Ár/Terv:** Ingyenes (open source, self-hosted) / Cloud: $5/hó-tól

---

## Mi ez és mire jó?

> [!tldr] Egy mondatban
> A saját [[cloud/vercel|Vercel]]/Heroku-d a saját VPS-eden - open-source, self-hosted PaaS ami git push-ból deployol, automatikus SSL-t ad, és adatbázisokat is kezel.

A **Coolify** egy webes dashboard amivel bármilyen appot ([[cloud/docker-alapok|Docker]], Node, static, Python) deployolhatsz a saját szerveredre anélkül, hogy kézzel kellene Docker Compose fájlokat írnod, [[cloud/nginx|Nginx]]-et konfigurálnod, vagy SSL certeket kezelnéd.

**Analógia:** A Vercel a "bérlakás" (kényelmes, de a főbérlő szabályai szerint élsz). A Coolify a "saját ház" - te irányítasz mindent, de a Coolify az a házkezelő aki megcsinálja az SSL-t, a routing-ot, és a deploymenteket.

---

## Mikor Coolify vs alternatívák?

| Szempont | **Coolify** | **Vercel** | **[[cloud/railway|Railway]]** | **Dokku** |
|----------|-------------|----------------|-----------------|-----------|
| **Típus** | Self-hosted PaaS | Managed PaaS | Managed PaaS | Self-hosted PaaS |
| **Ár** | Ingyenes + VPS költség | Hobby free / $20 Pro | $5 kredit/hó | Ingyenes + VPS |
| **Kontroll** | Teljes | Korlátozott | Közepes | Teljes |
| **Setup** | 1 parancs a VPS-en | Regisztráció | Regisztráció | 1 parancs |
| **Dashboard** | Webes UI | Webes UI | Webes UI | CLI only |
| **Adatbázis** | Beépítve (Postgres, Redis, stb.) | Nincs | Van | Plugin-nel |
| **SSL** | Automatikus (Let's Encrypt) | Automatikus | Automatikus | Automatikus |
| **Ideális** | VPS + kontroll + UI | Frontend, Next.js | Gyors backend | Minimalisták |

> [!tip] Döntési fa
> - **Nem akarsz szervert kezelni?** -> Vercel vagy Railway
> - **Van VPS-ed (Hetzner) és kontrollt akarsz?** -> Coolify
> - **Privát adat, GDPR, saját szerver kell?** -> Coolify
> - **Csak CLI kell, nincs UI igény?** -> Dokku (egyszerűbb)
> - **Sok app, egy szerveren?** -> Coolify (multi-app dashboard)

---

## Hogyan működik?

```
Git push → Coolify webhook → Docker build → Deploy → Traefik routing → SSL
```

| Lépés | Mi történik |
|-------|-------------|
| **1. Forrás** | GitHub/GitLab repo csatlakoztatás vagy Docker image megadás |
| **2. Build** | Coolify buildeli a Docker image-et (Nixpacks vagy Dockerfile) |
| **3. Deploy** | Container indul a szerveren |
| **4. Routing** | [[cloud/traefik|Traefik]] reverse proxy irányítja a forgalmat a domain-ről |
| **5. SSL** | Let's Encrypt cert automatikusan generálódik |

---

## Legjobb kombó: Hetzner + Coolify

| Elem | Konkrét ajánlás | Havi költség |
|------|-----------------|-------------|
| **VPS** | [[cloud/hetzner|Hetzner]] CX22 (2 vCPU, 4GB RAM) | ~EUR4.5 |
| **PaaS** | Coolify (self-hosted) | EUR0 |
| **Domain** | Cloudflare [[cloud/domain-es-dns-kezeles|DNS]] | EUR0 |
| **SSL** | Let's Encrypt (Coolify kezeli) | EUR0 |
| **Összesen** | | **~EUR4.5/hó** |

Ez a setup egyenértékű egy Vercel Pro ($20/hó) + Railway ($5/hó) kombóval, de teljes kontrollal és korlátlan erőforrással.

---

## Setup (Hetzner VPS-re)

```bash
# 1. Hetzner-en VPS létrehozás (Ubuntu 22.04)
# 2. SSH belépés, majd:
curl -fsSL https://cdn.coollabs.io/coolify/install.sh | bash

# 3. Böngészőben: http://your-server-ip:8000
# 4. GitHub OAuth app csatlakoztatás
# 5. Új projekt → Forrás: GitHub repo → Deploy
```

---

## AI-natív fejlesztés

A Coolify setup és konfigurálás jól automatizálható AI-val - Claude Code tud Dockerfile-t, docker-compose-t, és Coolify-kompatibilis konfigot generálni. A Traefik label-ek és network konfiguráció a leggyakoribb AI-generálási feladat itt.

> [!tip] Hogyan használd AI-val
> - *"Deploy setup Coolify-ra Hetzner VPS-en: Node.js app Dockerfile-lal, PostgreSQL adatbázissal, automatikus SSL."*
> - *"Hetzner VPS + Coolify - teljes self-hosted infrastruktúra setup Docker Compose-zal."*
> - *"Jelezd: 'Coolify-ra megy, Traefik a reverse proxy' - így a helyes label-eket és network konfigot kapod"*

---

## Kapcsolódó

- [[cloud/docker-alapok|Docker]] - Coolify Docker-re épül, minden app container-ben fut
- [[cloud/docker-compose|Docker Compose]] - Coolify alatt Docker Compose az orchestráció
- [[cloud/hetzner|Hetzner]] - legjobb ár/érték VPS Coolify-hoz
- [[cloud/traefik|Traefik]] - Coolify a Traefik-et használja reverse proxy-ként
- [[cloud/nginx|Nginx]] - alternatív reverse proxy (Coolify-ban Traefik az alap)
- [[cloud/vercel|Vercel]] - managed alternatíva (ha nem akarsz szervert kezelni)
- [[cloud/railway|Railway]] - managed alternatíva (egyszerűbb backend deploy)
