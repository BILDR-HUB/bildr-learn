---
tags: [frontend, nextjs, env, konfiguracio]
datum: 2026-02-25
szint: "🌱 Newcomer"
kapcsolodo:
  - "[[frontend/nextjs|Next.js]]"
  - "[[cloud/cloudflare|Cloudflare]]"
  - "[[cloud/vercel|Vercel]]"
  - "[[foundations/git-es-github|Git es GitHub]]"
  - "[[cloud/docker-compose|Docker Compose]]"
---

## Mi a problema?

A [[frontend/nextjs|Next.js]]-ben az env valtozok kezelese az egyik leggyakoribb buktato, mert **nem minden env valtozoegyforma**. Van ami build-kor kerul be, van ami futaskor — es ha nem a megfelelo helyen definialod, csendben `undefined` lesz.

---

## Build-time vs Runtime

| | Build-time | Runtime |
|---|---|---|
| **Mikor olvasodik** | `next build` pillnataban | Szerver indulaskor |
| **Hol el** | Beepul a JS bundle-be (string replace) | `process.env`-bol olvassa a szerver |
| **Prefix** | `NEXT_PUBLIC_*` | Nincs prefix |
| **Ki latja** | Kliens + szerver | Csak szerver |
| **Static export** | Ez az egyetlen opcio | Nem elerheto (`output: 'export'`) |

### A kulcsmondat

> [!warning] Build-time csapda
> `NEXT_PUBLIC_*` valtozok a **build pillanataban** kerulnek bele a kodba. Ha a build-kor nincs beallitva, `undefined` lesz — nincs masodik esely. CI dashboardon elore be kell allitani!

---

## Hol kell definlalni?

### Lokalis fejlesztes

```bash
# .env.local (gitignore-ban!)
NEXT_PUBLIC_GA_ID=G-XX8NRZHSF3
DATABASE_URL=postgresql://...
```

### CI/CD build (Cloudflare Pages, Vercel, stb.)

A `.env.local` gitignore-ban van, tehat a CI nem latja. Kulon be kell allitani:

- **Cloudflare Pages:** Dashboard > Settings > Environment Variables
- **Vercel:** Dashboard > Settings > Environment Variables

### Dokumentacio

```bash
# .env.example (commitolva, placeholder ertekekkel)
NEXT_PUBLIC_GA_ID=G-XXXXXXXXXX
DATABASE_URL=
```

---

## Gyakori buktato: `wrangler.toml [vars]`

A [[cloud/cloudflare|Cloudflare]] `wrangler.toml` `[vars]` szekcioja **runtime** valtozokat definial Workers/Functions szamara. Statikus exportnal (`output: 'export'`) ezeknek **nincs hatasuk** a `NEXT_PUBLIC_*` valtozokra, mert azok build-time-ban kellenek.

```toml
# wrangler.toml
[vars]
NEXT_PUBLIC_GA_ID = "G-XX8NRZHSF3"  # ⚠️ NEM build-time! Csak emlekeztetO.
```

---

## Checklist — uj env valtozohoz hozzaadasa

```
1. ✅ .env.local         → lokalisan mukodjon
2. ✅ .env.example       → dokumentalva legyen
3. ✅ CI dashboard       → elesben is mukodjon
4. ✅ Teszt: npm run build → grep -r "ERTEK" out/
```

---

## Kapcsolodo

- [[frontend/nextjs|Next.js]]
- [[cloud/cloudflare|Cloudflare]]
- [[cloud/vercel|Vercel]]
- [[foundations/git-es-github|Git es GitHub]]
- [[cloud/docker-compose|Docker Compose]]
