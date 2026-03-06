# MOC - Deployment

> [!tldr]
> Ez a Map of Content összegyűjti az összes deployment-hez és hosting-hoz kapcsolódó jegyzetet. Frontend hostingtól a backend platformokon át a best practice-ekig.

---

## Frontend hosting

| Note | Leírás |
|------|--------|
| [[cloud/vercel|Vercel]] | Next.js-re optimalizált frontend hosting — push to deploy, globális CDN, serverless functions |
| [[cloud/cloudflare|Cloudflare]] | Edge computing platform — Workers + D1 + R2 + Access, $5/hó, belső appokra kiváló |
| Next.js on Cloudflare Workers | Fullstack Next.js CF Workers-ön — OpenNext adapter, SSR/SSG/ISR, $5/hó Vercel alternatíva |
| ViNext | Next.js reimplementáció Vite-on — kísérleti, 4.4x gyorsabb build, 57% kisebb bundle, CF Workers natív |

## Backend hosting

| Note | Leírás |
|------|--------|
| [[cloud/railway|Railway]] | Backend és szolgáltatás hosting — Docker konténerek, adatbázisok, queue-k, cron jobok futtatása |
| [[cloud/hostinger|Hostinger]] | Olcsó VPS hosting — saját Linux szerver felhőben, teljes root hozzáférés, fix havi ár |

## Managed services

| Note | Leírás |
|------|--------|
| [[database/supabase|Supabase]] | Nyílt forráskódú Firebase alternatíva — PostgreSQL + Auth + Storage + Realtime + Edge Functions |

## Best practices

| Note | Leírás |
|------|--------|
| [[cloud/12-faktoros-alkalmazas-epites|12 Faktoros alkalmazas epites]] | Cloud-native alkalmazások 12 alapelve — portábilis, skálázható, könnyen deployolható app-ok építése |
| [[cloud/deployment-checklist|Deployment checklist]] | Deployment előtti ellenőrző lista: Supabase + Vercel, Railway, Hostinger stack-ekhez |
| [[cloud/blue-green-deployment|Blue-Green és Canary Deployment]] | Zero-downtime deploy stratégiák — blue-green, canary, rolling deployment |
| [[cloud/domain-es-dns-kezeles|Domain és DNS kezelés]] | Custom domain beállítás, SSL tanúsítványok, DNS rekordok |
| [[cloud/monitoring-es-logging|Monitoring és Logging]] | Hogyan tudd meg ha valami elromlik production-ben, structured logging |
| [[cloud/statuspage-es-incident-management|Statuspage és Incident Management]] | Ügyfelek tájékoztatása leállásokról, postmortem kultúra |
| [[cloud/cost-estimation|Hosting költségbecslés]] | Mikor melyik platform éri meg SMB szemszögből, rejtett költségek |

---

## API Frameworks

| Note | Leírás |
|------|--------|
| [[backend/hono|Hono]] | Ultragyors edge-natív API framework — Cloudflare Workers, Deno, Bun, Node.js |
| [[backend/express|Express]] | Klasszikus Node.js web framework — a backend fejlesztés kiindulópontja |

## Cloud szolgáltatók

| Note | Leírás |
|------|--------|
| [[cloud/aws-gcp-basics|Felhő szolgáltatók alapjai]] | AWS, GCP, Azure alapok — mikor érdemes váltani managed platformokról |

## CI/CD és Deploy stratégiák

| Note | Leírás |
|------|--------|
| [[cloud/ci-cd-pipelines|CI/CD Pipelines]] | GitHub Actions, automatikus testing és deploy |
| [[cloud/gitops|GitOps]] | Git-alapú deployment stratégia — ArgoCD, Flux, Kubernetes-hez |
| [[cloud/saas-mvp-deployment|SaaS MVP Deployment]] | Leggyorsabb út ötlettől a production-ig, stack választás üzleti szempontból |
| [[cloud/ai-assisted-deployment|AI-assisted Deployment]] | Claude Code automatizált deploy scriptek, Dockerfile és infra-as-code generálás |

## Kapcsolódó note-ok

| Note | Kapcsolat |
|------|-----------|
| [[cloud/docker-alapok|Docker alapok]] | A legtöbb deploy Docker image-ből indul |
| [[toolbox/tailscale|Tailscale]] | VPS-ek biztonságos elérése alagúton keresztül |
| Env változók Next.js-ben | Env változók kezelése deployment környezetekben |
| [[foundations/git-es-github|Git es GitHub]] | Push-to-deploy flow GitHub-ról indul |
| n8n workflow automatizálás | VPS-en futó automatizációs rendszer |
| [[backend/edge-function|Edge function]] | Edge computing koncepció — hogyan működik |
| [[toolbox/grafana|Grafana]] | Monitoring és dashboard-ok VPS-hez |

---

## Tanulási útvonal

Ajánlott sorrend ha nulláról kezded:
1. [[cloud/12-faktoros-alkalmazas-epites|12 Faktoros alkalmazas epites]] — modern deployment elvek megértése: miért így csinálják
2. [[cloud/vercel|Vercel]] — frontend deploy: Next.js app felrakása percek alatt
3. [[cloud/railway|Railway]] — backend deploy: API szerver, adatbázis, háttérfolyamatok
4. [[database/supabase|Supabase]] — managed backend: ha nem akarsz saját szervert üzemeltetni
5. [[cloud/hostinger|Hostinger]] — VPS: teljes kontroll, ha a managed platformok nem elegek

> [!tip] Melyiket válaszd?
> **Legolcsóbb fullstack Next.js:** Next.js on Cloudflare Workers ($5/hó) — SSR/SSG/ISR + D1 + R2 + Access, belső toolokra és landing page-ekre ideális.
> **Legolcsóbb API-only:** Cloudflare Workers + [[backend/hono|Hono]] ($5/hó) — ha nem kell Next.js, csak API.
> **Legegyszerűbb:** Vercel (frontend) + Supabase (backend) — zero ops.
> **Több kontroll:** Vercel (frontend) + Railway (backend) — saját DB, háttérfolyamatok.
> **Teljes kontroll:** Hostinger VPS — mindent te kezelsz, de olcsóbb.

---

## Hézagok

- [x] CI/CD pipelines → [[cloud/ci-cd-pipelines|CI/CD Pipelines]] — GitHub Actions, automatikus testing és deploy
- [x] AWS/GCP basics → [[cloud/aws-gcp-basics|Felhő szolgáltatók alapjai]] — felhő szolgáltatók alapjai, mikor érdemes váltani
- [x] Blue-green deployment → [[cloud/blue-green-deployment|Blue-Green és Canary Deployment]] — zero-downtime deploy stratégia
- [x] Domain és DNS kezelés → [[cloud/domain-es-dns-kezeles|Domain és DNS kezelés]] — custom domain beállítás, SSL tanúsítványok
- [x] Monitoring és logging → [[cloud/monitoring-es-logging|Monitoring és Logging]] — hogyan tudd meg ha valami elromlik production-ben
- [x] AI-assisted deployment → [[cloud/ai-assisted-deployment|AI-assisted Deployment]] — Claude Code automatizált deploy scriptek, infra-as-code generálás
- [x] Cost estimation → [[cloud/cost-estimation|Hosting költségbecslés]] — hosting költségbecslés, mikor melyik platform éri meg (SMB szemszögből)
- [x] SaaS MVP deployment → [[cloud/saas-mvp-deployment|SaaS MVP Deployment]] — leggyorsabb út ötlettől a production-ig, stack választás üzleti szempontból
- [x] Statuspage és incident management → [[cloud/statuspage-es-incident-management|Statuspage és Incident Management]] — ügyfelek tájékoztatása leállásokról, postmortem kultúra
