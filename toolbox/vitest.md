---
tags:
  - eszkoz
  - testing
datum: 2026-03-26
szint: "🧱 Brick"
kapcsolodo:
  - "[[toolbox/playwright|Playwright]]"
  - "[[toolbox/claude-code-projekt-setup|Claude Code]]"
  - "[[frontend/react-vs-spa-vs-preact|React]]"
  - "[[toolbox/turborepo|Turborepo]]"
---

# Vitest

**Kategória:** `testing framework`
**URL:** https://vitest.dev
**Ár:** Ingyenes, open source

---

## Mi ez és mire jó?

A **Vitest** a modern unit/integration test runner - a Vite ökoszisztéma natív teszteszköze, Jest-kompatibilis API-val, de jelentősen gyorsabb.

A Vitest ugyanazt csinálja mint a Jest (teszteket futtatsz vele), de a Vite transform pipeline-ját használja - tehát **nem kell dupla konfig**. Ami a `vite.config.ts`-ben van (aliasok, pluginok, JSX), azt a Vitest automatikusan átveszi.

---

## Vitest vs Jest - döntési fa

| Szempont | Jest | **Vitest** |
|----------|------|-----------|
| **Sebesség** | Lassabb (Babel transform) | 2-5x gyorsabb (Vite/esbuild) |
| **ESM támogatás** | Kísérleti, problémás | Natív ESM |
| **Konfiguráció** | Külön `jest.config.js` + babel | Vite configból örököl |
| **[[foundations/typescript-vs-python|TypeScript]]** | Babel/ts-jest kell | Natív, zero-config |
| **Watch mode** | Lassú újrafuttatás | Instant (Vite HMR-re épül) |
| **API kompatibilitás** | - | Jest-kompatibilis (drop-in) |
| **Érettség** | Stabil, óriási ökoszisztéma | Stabil (v2+), növekvő |

> [!tip] Fő szabály
> **Új projekt - Vitest.** Meglévő Jest projekt - maradj Jestnél, hacsak nem fáj a sebesség.

---

## Fő képességek

- **Snapshot testing** - ugyanaz mint Jestnél (`toMatchSnapshot()`)
- **Mocking** - `vi.fn()`, `vi.mock()`, `vi.spyOn()` (Jest-szerű API)
- **Coverage** - beépített c8/istanbul támogatás (`vitest --coverage`)
- **Watch mode** - csak az érintett teszteket futtatja újra, instant feedback
- **In-source testing** - teszteket írhatsz közvetlenül a forrásfájlba (opcionális)
- **Browser mode** - valódi böngészőben futtat ([[toolbox/playwright|Playwright]]/WebDriverIO integráció)

---

## Gyors setup

```bash
# Telepítés (ha már van Vite projekted)
npm install -D vitest

# Futtatás
npx vitest        # watch mode
npx vitest run    # egyszeri futtatás (CI-hez)
npx vitest --coverage  # coverage riporttal
```

A `package.json`-be:

```json
{
  "scripts": {
    "test": "vitest",
    "test:ci": "vitest run --coverage"
  }
}
```

---

## Hogyan használd promptban?

> [!tip] Claude Code tesztelési prompt
> Amikor [[toolbox/claude-code-projekt-setup|Claude Code]]-dal dolgozol, jelezd a test runnert:
> - *"Írj unit teszteket Vitest-tel a `utils/` mappába"*
> - *"Add hozzá a Vitest coverage-et a [[cloud/ci-cd-pipelines|CI/CD]] pipeline-hoz"*
> - *"Migráld a Jest teszteket Vitest-re - az API szinte azonos, a config az ami változik"*
>
> Ha nem mondod, az AI valószínűleg Jestet használ - explicit jelezd.
