---
tags: [frontend, nextjs, bun, setup]
datum: 2026-03-03
szint: "🧱 Brick"
kapcsolodo:
  - "[[frontend/nextjs|Next.js]]"
  - "[[foundations/git-es-github|Git es GitHub]]"
---

## Mi az a Bun?

**Bun** egy all-in-one JavaScript runtime + package manager + bundler. A Node.js alternativaja, de **3-5x gyorsabb** package install-nal es script futtatásnal.

## Bun mint package manager Next.js-hez

A Bun **drop-in replacement** npm/pnpm helyett. Nem kell semmit konfigurani — ugyanugy `node_modules`-t hasznal, Next.js hivatalosan tamogatja.

### Telepites (macOS)

```bash
curl -fsSL https://bun.sh/install | bash
```

### Hasznalat

```bash
# Uj Next.js projekt
bunx create-next-app@latest my-project

# Meglevo projektnél valtas npm-rol
rm -rf node_modules package-lock.json
bun install    # bun.lockb generalodik

# Dev server inditas
bun run dev    # gyorsabb script start, de a Next.js szerver maga Node-on fut

# Package hozzaadas
bun add zod
bun add -d @types/node
```

### Fontos: Bun lockfile

A `bun.lockb` **binaris** — git diff-ben nem olvashato. Ha ez zavar:

```bash
bun install --yarn   # szoveges yarn.lock-ot general helyette
```

## Go-to Next.js dependency stack

### Alap (minden projektben)

| Csomag | Mire |
| --- | --- |
| `typescript` | Type safety |
| `tailwindcss` v4 + `@tailwindcss/postcss` | Styling |
| `clsx` + `tailwind-merge` | Tailwind class management |
| `lucide-react` | Ikonok |
| `eslint` + `eslint-config-next` | Linting |

### Ajanlott kiegeszitok

| Csomag | Mire |
| --- | --- |
| `prettier` + `prettier-plugin-tailwindcss` | Auto-rendezi a Tailwind class sorrendet |
| `@next/bundle-analyzer` | Bundle meret analizis |
| `sharp` | Next.js Image optimalizacio (ha nem static export) |

## CI/CD - Bun setup

### GitHub Actions

```yaml
- uses: oven-sh/setup-bun@v2
- run: bun install
- run: bun run build
```

### Cloudflare Pages

Cloudflare Pages-en elerheto a Bun, de **Node a default**. Build settings-ben ellenorizd.

## Opcionalis: `.bunfig.toml`

Projekt gyokerbe:

```toml
[install]
peer = false
```

## Kapcsolodo

- [[frontend/nextjs|Next.js]]
- [[foundations/git-es-github|Git es GitHub]] — CI/CD, GitHub Actions
