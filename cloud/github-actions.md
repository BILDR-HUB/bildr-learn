---
tags:
  - ci-cd
  - github
  - automation
datum: 2026-03-26
szint: "🧱 Brick"
kapcsolodo:
  - "[[foundations/git-es-github|Git]]"
  - "[[cloud/vercel|Vercel]]"
  - "[[cloud/docker-alapok|Docker]]"
  - "[[cloud/cloudflare|Cloudflare]]"
  - "[[cloud/deployment-checklist|Deployment checklist]]"
  - "[[cloud/ci-cd-pipelines|CI/CD]]"
---

# GitHub Actions

**Kategória:** `ci/cd`
**URL:** https://github.com/features/actions
**Ár/Terv:** Ingyenes (public repo korlátlan, private: 2000 perc/hó free tier)

---

## Mi ez és mire jó?

> [!tldr] Egy mondatban
> A GitHub beépített [[cloud/ci-cd-pipelines|CI/CD]] platformja - YAML fájlokkal definiálod, hogy push-ra, PR-re vagy ütemezetten mi fusson automatikusan (teszt, build, deploy).

A GitHub Actions a legkézenfekvőbb automatizálási eszköz, ha a kódod [[foundations/git-es-github|Git]]Hubon van. Nem kell külső szolgáltatás, nem kell integrációt konfigurálni - a repó `.github/workflows/` mappájában lévő YAML fájlok definiálják a pipeline-okat.

**Analógia:** Ha a GitHub a raktár ahol a kódod él, a GitHub Actions az automatizált futószalag ami minden beérkező csomag (commit) után ellenőriz, csomagol, és kiszállít.

---

## Kulcs fogalmak

| Fogalom | Mi ez | Példa |
|---------|-------|-------|
| **Workflow** | Egy YAML fájl = egy automatizált folyamat | `.github/workflows/ci.yml` |
| **Trigger** | Mi indítja el | `push`, `pull_request`, `schedule`, `workflow_dispatch` |
| **Job** | Egy feladat (saját VM-en fut) | `test`, `build`, `deploy` |
| **Step** | Egy lépés a job-on belül | `npm install`, `npm test` |
| **Action** | Újrafelhasználható lépés (marketplace) | `actions/checkout@v4`, `actions/setup-node@v4` |
| **Secret** | Titkos változó (env) | `${{ secrets.API_KEY }}` |
| **Matrix** | Párhuzamos futtatás több konfigurációval | Node 18 + 20, Ubuntu + macOS |

---

## Mikor GitHub Actions vs alternatívák?

| Szempont | **GitHub Actions** | **[[cloud/vercel|Vercel]] Auto-deploy** | **GitLab CI** |
|----------|-------------------|--------------------------|---------------|
| **Setup** | YAML fájl kell | Nulla konfig | YAML fájl kell |
| **Ár** | Ingyenes (public) | Ingyenes (Hobby) | Ingyenes (400 perc) |
| **Mikor jó** | Bármi: teszt, build, deploy, cron | Csak deploy (Next.js) | GitLab repók |
| **Flexibilitás** | Teljes | Csak deploy | Teljes |
| **GitHub integráció** | Natív | API-n keresztül | Nincs |

> [!tip] Döntési fa
> - **Next.js app, csak deploy kell?** -> Vercel auto-deploy elég, nem kell Actions
> - **Tesztek kellenek PR-re?** -> GitHub Actions (lint + test on PR)
> - **Nem GitHub-on van a kód?** -> GitLab CI vagy CircleCI
> - **[[cloud/docker-alapok|Docker]] build + deploy VPS-re?** -> GitHub Actions + SSH deploy

---

## Gyakori workflow minták

### 1. PR ellenőrzés (lint + test)

```yaml
# .github/workflows/ci.yml
name: CI
on: [pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: 20 }
      - run: npm ci
      - run: npm run lint
      - run: npm test
```

### 2. Deploy push-ra (Docker -> VPS)

```yaml
name: Deploy
on:
  push:
    branches: [main]
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: docker build -t myapp .
      - run: docker push ghcr.io/user/myapp
      - name: SSH deploy
        run: ssh user@server "docker pull ghcr.io/user/myapp && docker compose up -d"
```

---

## AI-natív fejlesztés

A GitHub Actions YAML szintaxis ismétlődő és jól dokumentált - Claude Code az egyik legjobban generálható CI/CD formátum. A workflow fájlok generálásakor fontos, hogy jelezd a kontextust: monorepo (Turborepo), Docker, vagy Cloudflare Workers.

> [!tip] Hogyan használd AI-val
> - *"Írj GitHub Actions workflow-t: PR-re lint + test, main push-ra Docker build és deploy Hetzner VPS-re SSH-val."*
> - *"GitHub Actions matrix build - Node 18 és 20, Ubuntu és macOS."*
> - *"Jelezd a kontextust: monorepo (Turborepo), Docker, vagy Cloudflare Workers - ettől függ a workflow"*

---

## Kapcsolódó

- [[foundations/git-es-github|Git]] - Actions a GitHub beépített része
- [[cloud/vercel|Vercel]] - egyszerűbb alternatíva ha csak deploy kell
- [[cloud/docker-alapok|Docker]] - Docker build + push gyakori Actions minta
- [[cloud/cloudflare|Cloudflare]] - Cloudflare Workers deploy GitHub Actions-ből (wrangler)
- [[cloud/deployment-checklist|Deployment checklist]] - Actions a deployment pipeline része
- [[cloud/ci-cd-pipelines|CI/CD]] - Actions a CI/CD eszközök egyike
