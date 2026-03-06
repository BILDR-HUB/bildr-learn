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

# Projekt-szintu izolacio

## Mirol van szo?

A modern fejlesztesben minden projektet **izolaltan** kezelunk -- sajat fuggosegekkel, sajat verzioval, sajat konfiguracioval. Ez megakadalyozza, hogy az egyik projekt eltorje a masikat.

A regi megkozelites: egy gep = egy globalis konfiguracio. A modern: egy projekt = egy izolalt kornyezet.

---

## Izolacio tipusai

| Szint | Regi (globalis) | Modern (izolalt) | Eszkoz |
|---|---|---|---|
| **Node.js verzio** | `brew install node` | `.nvmrc` per projekt | fnm, nvm |
| **Node csomagok** | `npm install -g` | `devDependencies` + `npx` | npm, pnpm |
| **Python verzio** | `brew install python` | `.python-version` per projekt | pyenv |
| **Python csomagok** | `pip install` globalisan | `venv` per projekt | venv, uv |
| **Env valtozok** | rendszerszintu export | `.env.local` per projekt | dotenv |
| **Futtatokornyezet** | kozvetlenul a gepen | kontenerben | Docker |

---

## Miert fontos?

**Gyakorlati pelda:** Van egy Next.js 16 projekted (Node 24 kell) es egy masik projekted (Node 22 kell). Ha globalisan valtod a Node-ot:

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

### 1. `.nvmrc` -- Node verzio

```
24
```

### 2. `.env.example` -- szukseges env valtozok dokumentalva

```bash
NEXT_PUBLIC_GA_ID=G-XXXXXXXXXX
DATABASE_URL=
API_KEY=
```

### 3. `package.json` -- csomagok es scriptek lokalisan

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

- **Brew** tovabbra is jo globalis CLI toolokra (git, ffmpeg, htop)
- **Egy projekthez** nem kell verziokotes -- ha csak egyet fejlesztesz, a globalis is eleg
- **Ne over-engineerelj** -- Docker dev kornyezethez csak akkor, ha tenyleg kell (pl. adatbazis, Redis)

---

## Kapcsolodo

- [[foundations/python-venv|Python venv]] -- Python izolacio
- [[cloud/docker-alapok|Docker alapok]]
- [[frontend/nextjs|Next.js]]
- [[_moc/moc-environment-setup|MOC - Environment Setup]]
