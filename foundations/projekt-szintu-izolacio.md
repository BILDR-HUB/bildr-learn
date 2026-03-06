---
tags:
  - tooling
  - nodejs
  - python
kapcsolodo:
  - "[[foundations/python-venv|Python venv]]"
  - "[[cloud/docker-alapok|Docker alapok]]"
  - "[[frontend/nextjs|Next.js]]"
  - "[[_moc/moc-environment-setup|MOC - Environment Setup]]"
datum: 2026-02-08
szint: "🌱 Newcomer"
---

# Projekt-szintű izolácio

## Mirol van szo?

A modern fejlesztesben minden projektet **izoláltan** kezelunk -- saját függőségekkel, saját verzióval, saját konfigurácioval. Ez megakadalyozza, hogy az egyik projekt eltorje a másikat.

A regi megkozelites: egy gep = egy globális konfigurácio. A modern: egy projekt = egy izolált környezet.

---

## Izolácio tipusai

| Szint | Regi (globális) | Modern (izolált) | Eszkoz |
|---|---|---|---|
| **Node.js verzió** | `brew install node` | `.nvmrc` per projekt | fnm, nvm |
| **Node csomagok** | `npm install -g` | `devDependencies` + `npx` | npm, pnpm |
| **Python verzió** | `brew install python` | `.python-version` per projekt | pyenv |
| **Python csomagok** | `pip install` globálisan | `venv` per projekt | venv, uv |
| **Env változók** | rendszerszintu export | `.env.local` per projekt | dotenv |
| **Futtatokornyezet** | kozvetlenul a gépen | konténerben | Docker |

---

## Miért fontos?

**Gyakorlati példa:** Van egy Next.js 16 projekted (Node 24 kell) és egy másik projekted (Node 22 kell). Ha globálisan valtod a Node-ot:

```
brew link node@24   -> az egyik mukodik, a masik eltorik
brew link node@22   -> a masik mukodik, az egyik eltorik
                      + npm eltunik
```

fnm-mel:

```
cd projekt-a/    -> automatikusan Node 24 (.nvmrc)
cd projekt-b/    -> automatikusan Node 22 (.nvmrc)
                   semmi sem torik el
```

---

## A "Holy Trinity" -- amit mindig allits be projekt elejen

### 1. `.nvmrc` -- Node verzió

```
24
```

### 2. `.env.example` -- szükséges env változók dokumentalva

```bash
NEXT_PUBLIC_GA_ID=G-XXXXXXXXXX
DATABASE_URL=
API_KEY=
```

### 3. `package.json` -- csomagok és scriptek lokálisan

```json
{
  "devDependencies": {
    "wrangler": "^4.0.0"   // NE globalisan, hanem itt
  },
  "scripts": {
    "deploy": "wrangler pages deploy ./out"  // npx-szel fut
  }
}
```

---

## Mikor NE izolalj?

- **Brew** tovabbra is jó globális CLI toolokra (git, ffmpeg, htop)
- **Egy projekthez** nem kell verziókotes -- ha csak egyet fejlesztesz, a globális is eleg
- **Ne over-engineerelj** -- Docker dev környezethez csak akkor, ha tényleg kell (pl. adatbázis, Redis)

---

## Kapcsolodo

- [[foundations/python-venv|Python venv]] -- Python izolácio
- [[cloud/docker-alapok|Docker alapok]]
- [[frontend/nextjs|Next.js]]
- [[_moc/moc-environment-setup|MOC - Environment Setup]]
