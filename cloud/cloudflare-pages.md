---
tags:
  - cloudflare
  - hosting
  - frontend
datum: 2026-03-26
szint: "🧱 Scout"
kapcsolodo:
  - "[[cloud/cloudflare|Cloudflare]]"
  - "[[cloud/vercel|Vercel]]"
  - "[[cloud/cloudflare-r2|Cloudflare R2]]"
  - "[[cloud/nextjs-on-cloudflare-workers|Next.js on Cloudflare Workers]]"
  - "[[backend/hono|Hono]]"
---

# Cloudflare Pages

> [!tldr] Mi ez és mire jó?
> Static site és fullstack hosting a [[cloud/cloudflare|Cloudflare]] edge hálózatán. Git push = deploy. Minden PR-hez preview URL, korlátlan bandwidth ingyen. A frontend párja a Cloudflare Workers backend-nek.

## Hogyan működik

1. **GitHub/GitLab repo-t csatlakoztatod** - automatic build minden push-ra
2. **Build step** - CF lefuttatja a build-et (React, [[frontend/nextjs|Next.js]], Astro, stb.)
3. **Edge deploy** - a build output 300+ edge lokációra kerül
4. **Preview URL** - minden PR kap egyedi `<hash>.pages.dev` URL-t

## Mikor melyiket válaszd

| | Cloudflare Pages | [[cloud/vercel|Vercel]] | Netlify |
|---|---|---|---|
| Ár | Korlátlan bandwidth ingyen | 100 GB/hó free, utána fizetsz | 100 GB/hó free |
| Build limit | 500/hó (free), 5000 (paid) | 6000/hó (free) | 300 perc/hó |
| DX | Jó, de Vercel mögött | Legjobb Next.js DX | Pages-hez hasonló |
| Edge runtime | Workers (V8 isolate) | Edge Functions (V8) | Edge Functions |
| CF integráció | Natív (R2, D1, KV, Queues) | Nincs | Nincs |
| Mikor válaszd | CF stack, SPA, static site | Next.js heavy app, Node.js API routes kellenek | Ha már ott vagy |

## Key döntés: Pages vs Vercel

- **Pages-t válaszd** ha a backend Cloudflare Workers-ön fut és az egész stack CF-natív
- **Vercelt válaszd** ha Next.js-t használsz és kellenek a Node.js runtime-specifikus feature-ök (ISR, server actions full Node-dal)
- **Next.js on Pages**: lehetséges `@cloudflare/next-on-pages`-szel, de nem minden Next.js feature támogatott

## Főbb feature-ök

- **Preview deployments** - PR-enként automatikus, megosztható URL
- **Custom domain** - egyszerű [[cloud/domain-es-dns-kezeles|DNS]] beállítás (CF-en belül instant)
- **Rollback** - bármelyik korábbi deploy-ra egy kattintás
- **Functions** - Pages Functions = Workers, a `/functions` mappából auto-deploy
- **Analytics** - beépített web analytics (privacy-friendly)

## Árazás

- **Free:** korlátlan bandwidth, 500 build/hó, 1 concurrent build
- **Pro ($20/hó):** 5000 build/hó, 5 concurrent build

---

## AI-natív fejlesztés

A Cloudflare Pages deploy konfiguráció egyszerű, de a Pages Functions (Workers) és a CF szolgáltatás-integrációk (R2, D1, KV) már bonyolultabbak - itt segít az AI. Claude Code ismeri a Pages Functions szintaxist és a wrangler konfigot.

> [!tip] Hogyan használd AI-val
> - *"Hozz létre egy Cloudflare Pages projektet ehhez a React SPA-hoz. GitHub repo-ból deploy-oljon, a build command legyen `npm run build`, output dir `dist`. Adj hozzá egy `/functions/api` endpoint-ot is."*
> - *"Cloudflare Pages + R2 integráció: a Pages Functions-ből tölts fel fájlokat R2-be presigned URL-lel"*

## Kapcsolódó

- [[cloud/cloudflare|Cloudflare]] - a Cloudflare ökoszisztéma része
- [[cloud/vercel|Vercel]] - alternatív hosting platform
- [[cloud/nextjs-on-cloudflare-workers|Next.js on Cloudflare Workers]] - Next.js futtatás CF edge-en
- [[backend/hono|Hono]] - Pages Functions-ben is használható framework
- [[cloud/cloudflare-r2|Cloudflare R2]] - static asset-ek és fájlok tárolása a Pages mellett
