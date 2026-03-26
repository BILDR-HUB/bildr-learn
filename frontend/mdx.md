---
tags: [frontend, mdx, content, markdown]
datum: 2026-03-26
szint: "🧱 Scout"
kapcsolodo:
  - "[[frontend/react|React]]"
  - "[[frontend/nextjs|Next.js]]"
  - "[[frontend/tailwind-css|Tailwind CSS]]"
---

## Mi ez és mire jó?

> [!tldr] Egy mondatban
> **Markdown + [[frontend/react|React]] komponensek** egy fájlban - tartalom-központú oldalakhoz (docs, blog, knowledge base) ahol a szöveg Markdown, de interaktív elemeket is be akarsz ágyazni.

Az **MDX** kiterjeszti a Markdownt: a szokásos `# heading`, `**bold**`, `- lista` szintaxis mellé React komponenseket tehetsz. Egy `.mdx` fájl egyszerre tartalom és kód.

```mdx
# Üdvözlünk

Ez egy normál Markdown bekezdés.

<Callout type="info">
  Ez egy React komponens a Markdown közepén.
</Callout>

| Oszlop 1 | Oszlop 2 |
|----------|----------|
| Markdown | táblázat |

<InteractiveChart data={salesData} />
```

---

## Mikor használd / Mikor NE

| Helyzet | Megoldás |
|---------|---------|
| Docs site, dev blog, tudásbázis | MDX - a szerzők Markdownban írnak, de van interaktivitás |
| Marketing landing page | [[frontend/nextjs|Next.js]] + CMS - nem Markdown a tartalom |
| Non-technical szerkesztők írják a tartalmat | CMS (Payload, Sanity) - ők nem akarnak `.mdx` fájlokat szerkeszteni |
| Statikus blog, nincs interaktív elem | Sima Markdown elég - MDX overhead felesleges |
| Komponens dokumentáció (Storybook-szerű) | MDX - kód + demo + szöveg egy fájlban |

---

## Gyakori stack-ek

| Stack | Mikor |
|-------|-------|
| **Next.js + `@next/mdx`** | Next.js app-on belüli MDX oldalak (legegyszerűbb) |
| **Next.js + Contentlayer** | Type-safe content layer, automatikus típusgenerálás |
| **Next.js + Velite** | Contentlayer modern alternatívája (aktívan fejlesztett) |
| **Astro + MDX** | Ha statikus site kell, Astro natívan támogatja |
| **Docusaurus** | Ha docs site kell, MDX beépítve - zero config |

> [!info] Contentlayer státusz
> A Contentlayer eredeti projektje **nem aktívan karbantartott** (2024 óta). A **Velite** (`velite`) az ajánlott alternatíva - hasonló type-safe content layer, aktív fejlesztés.

---

## MDX vs alternatívák

| Szempont | Plain Markdown | **MDX** | CMS (Payload, Sanity) |
|----------|---------------|---------|----------------------|
| **Szerzők** | Fejlesztők | Fejlesztők | Bárki (GUI) |
| **Interaktivitás** | Nincs | React komponensek | CMS block editor |
| **Verziókezelés** | [[foundations/git-es-github|Git]] | Git | CMS dashboard |
| **Type safety** | Nincs | Contentlayer/Velite-tel igen | CMS SDK-val |
| **Setup** | Triviális | Közepes | Közepes-magas |

---

## Next.js + MDX alap setup

```typescript
// next.config.mjs
import createMDX from '@next/mdx'

const withMDX = createMDX({
  options: {
    remarkPlugins: [],    // remark-gfm (táblázatok), stb.
    rehypePlugins: [],    // rehype-highlight (syntax highlighting), stb.
  },
})

export default withMDX({
  pageExtensions: ['js', 'jsx', 'ts', 'tsx', 'md', 'mdx'],
})
```

Ezután az `app/blog/getting-started/page.mdx` fájl automatikusan route lesz a Next.js-ben.

---

## AI-natív fejlesztés

Az MDX fejlesztésnél az AI kiválóan generálja a custom komponenseket (Callout, CodeBlock, InteractiveDemo) és a remark/rehype plugin konfigurációt. Claude Code-dal a teljes MDX pipeline-t - Velite séma, custom komponensek, Next.js konfiguráció - egyetlen session alatt felépítheted.

> [!tip] Hogyan használd AI-val
> - *"Hozz létre egy Next.js + Velite MDX blog setup-ot type-safe frontmatter sémával és Tailwind Typography plugin-nal"*
> - *"Írj egy MDX custom komponenst: CodePlayground, ami szerkeszthető kódblokkot renderel live preview-val"*
> - *"Konfiguráld a remark-gfm és rehype-highlight pluginokat ehhez a Next.js MDX projekthez"*

---

## Kapcsolódó

- [[frontend/react|React]] - MDX-ben React komponenseket használsz
- [[frontend/nextjs|Next.js]] - a leggyakoribb framework MDX-szel
- [[frontend/tailwind-css|Tailwind CSS]] - MDX tartalom stílusozásához `@tailwindcss/typography` plugin
