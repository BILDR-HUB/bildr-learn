---
tags:
  - eszkoz
  - linting
  - formatting
datum: 2026-03-26
szint: "🌱 Newcomer"
kapcsolodo:
  - "[[toolbox/claude-code-projekt-setup|Claude Code]]"
  - "[[toolbox/turborepo|Turborepo]]"
  - "[[toolbox/vitest|Vitest]]"
  - "[[_moc/moc-ai-tooling|MOC - AI Tooling]]"
---

# ESLint és Biome

**Kategória:** `dev tool` (linting + formatting)

---

## Mi ez és mire jó?

Kód minőség-ellenőrző és formázó eszközök - az **ESLint** a régóta bevált JS/TS linter (plugin ökoszisztémával), a **Biome** az új all-in-one alternatíva (lint + format, Rust-alapú, 10-100x gyorsabb).

A linter **hibákat és rossz mintákat** talál a kódban (unused variable, missing return type). A formatter **egységes stílust** kényszerít rá (tabulátor vs space, sorhossz). Hagyományosan ez két külön eszköz volt (ESLint + Prettier) - a Biome mindkettőt egyben adja.

---

## ESLint + Prettier vs Biome

| Szempont | ESLint + Prettier | **Biome** |
|----------|-------------------|-----------|
| **Sebesség** | Lassú (JS-alapú) | 10-100x gyorsabb (Rust) |
| **Konfiguráció** | `.eslintrc` + `.prettierrc` + konfliktusok | Egy `biome.json` |
| **Plugin ökoszisztéma** | Hatalmas (eslint-plugin-react, import, stb.) | Növekvő, de kisebb |
| **Formatter** | Prettier (külön tool) | Beépített (Prettier-kompatibilis) |
| **[[foundations/typescript-vs-python|TypeScript]]** | @typescript-eslint (külön) | Natív |
| **Framework szabályok** | Pluginokkal (React, Next, Vue) | Beépített React/Next szabályok |
| **Import rendezés** | eslint-plugin-import (lassú) | Beépített, instant |
| **Érettség** | 10+ év, industry standard | Fiatal (2023~), de gyorsan nő |

---

## Mikor melyiket válaszd?

| Szituáció | Ajánlás |
|-----------|---------|
| **Új projekt, kis csapat** | Biome - egyszerűbb, gyorsabb, kevesebb config |
| **Meglévő projekt sok ESLint pluginnal** | Maradj ESLint-nél - migrációs költség magas |
| **Monorepo, sok fájl** | Biome - a sebesség itt nagyon számít |
| **Speciális pluginek kellenek** (a11y, testing-library) | ESLint - Biome-nak még nincs minden plugin |
| **[[cloud/ci-cd-pipelines|CI/CD]] pipeline lassú a linttől** | Biome - drasztikus gyorsulás |

---

## Gyors setup

### Biome (ajánlott új projekteknél)

```bash
# Telepítés
npm install -D @biomejs/biome

# Init (létrehozza a biome.json-t)
npx biome init

# Futtatás
npx biome check .              # lint + format check
npx biome check --write .      # autofix + format
npx biome format --write .     # csak formázás
```

### ESLint (ha már az van)

```bash
# Telepítés (flat config — ESLint 9+)
npm install -D eslint @eslint/js typescript-eslint

# Futtatás
npx eslint .
npx eslint --fix .
```

---

## Claude Code és a lint konfig

A [[toolbox/claude-code-projekt-setup|Claude Code]] **automatikusan tiszteletben tartja** a projekt lint konfigurációját - ha van `.eslintrc` vagy `biome.json`, a generált kód ahhoz igazodik. Nem kell külön jelezni.

> [!tip] Hogyan használd promptban
> - *"A projektben Biome-ot használunk - futtasd a `biome check --write` parancsot minden módosítás után"*
> - *"Az ESLint flat config-ot használjuk (eslint.config.js) - ne a régi .eslintrc formátumot"*
> - *"Adj hozzá CLAUDE.md-hez hogy a lint parancs `pnpm biome check --write .`"*
>
> A CLAUDE.md-ben érdemes a lint parancsot feltüntetni - így minden session-ben automatikusan fut.
