---
tags:
  - eszkoz
  - monorepo
datum: 2026-03-26
szint: "🧱 Brick"
kapcsolodo:
  - "[[toolbox/monorepo-management|Monorepo Management]]"
  - "[[cloud/ci-cd-pipelines|CI/CD]]"
  - "[[cloud/vercel|Vercel]]"
  - "[[cloud/devops|DevOps]]"
---

# Turborepo

**Kategória:** `dev tool` / `monorepo`
**Docs:** https://turbo.build/repo/docs

---

## Mi ez és mire jó?

A **Turborepo** egy build orchestrátor, ami egy monorepoban a taskok (build, lint, test) futtatását optimalizálja - cache-eli az eredményeket és csak azt futtatja újra, ami tényleg változott.

Amikor van 5 app és 5 package a monorepoban, a `pnpm build` **mindent** újra buildelne. A Turborepo ehelyett:

1. **Felismeri a függőségi gráfot** - tudja, hogy az `apps/crm` függ a `packages/ui`-tól
2. **Cache-eli az eredményeket** - ha a `packages/ui` nem változott, nem buildeli újra
3. **Párhuzamosít** - ami független egymástól, azt egyszerre futtatja

---

## Mikor használd?

### Kell Turborepo ha:
- **2+ app van** a monorepoban, közös package-ekkel
- A build idő **túl hosszú**, mert mindent újrafordít
- [[cloud/ci-cd-pipelines|CI/CD]]-ben akarod gyorsítani a pipeline-t
- **Több fejlesztő** dolgozik és a remote cache fontos

### Nem kell ha:
- **Egy app** van (akkor simán `pnpm build` elég)
- A build amúgy is gyors (kis projekt)
- Nem monorepo

---

## A turbo.json - a központi konfig

A `turbo.json` a repo gyökerében él, és leírja, milyen taskokat ismer a Turborepo és hogyan viszonyulnak egymáshoz.

A legfontosabb koncepciók:

| Fogalom | Mit jelent | Példa |
|---------|-----------|-------|
| **Task** | Egy futtatható parancs (script) | `build`, `lint`, `typecheck`, `dev` |
| **dependsOn** | Milyen taskok kell, hogy előbb lefussanak | A `build` megvárja a függő package-ek `build`-jét |
| **cache** | Letárolható-e az eredmény | A `build` igen, a `dev` nem |
| **outputs** | Milyen fájlokat/mappákat cache-eljen | `.next/`, `dist/` |

Egy tipikus turbo.json struktúra:

```
tasks:
  build      → cache, dependsOn: [^build]  (előbb a package-ek)
  lint       → cache, dependsOn: []         (független)
  typecheck  → cache, dependsOn: []         (független)
  dev        → no cache, persistent          (fut amíg leállítod)
```

> [!tip] A `^` prefix a dependsOn-ban
> A `^build` azt jelenti: "a saját **package függőségeim** build task-jai". Tehát ha `apps/crm` függ `packages/ui`-tól, akkor előbb a `packages/ui` `build`-je fut le, aztán a `crm`-é. Ez a **topológiai sorrend**.

---

## Caching - a Turborepo szíve

A caching az, ami a Turborepo-t igazán hasznossá teszi. Minden task eredményét **hash alapján** tárolja:

**Input hash = a task "ujjlenyomata":**
- Forrásfájlok tartalma
- Környezeti változók (amik hatnak az eredményre)
- Függőségek verziója

**Ha a hash nem változott - a korábbi eredmény visszajön cache-ből, nem futtatja újra.**

Két szintje van:

| Szint | Hol tárol | Mikor hasznos |
|-------|----------|---------------|
| **Local cache** | `.turbo/` mappa | Egyedül dolgozol, saját gépen |
| **Remote cache** | [[cloud/vercel|Vercel]] / self-hosted | Csapatmunka - amit egy kolléga buildeltet, te is megkapod |

> [!info] Gyakorlatban mit jelent?
> Ha a `packages/ui` nem változott, és te `pnpm build`-ot futtatsz, a Turborepo a `packages/ui` buildjet **másodpercek alatt** cache-ből adja vissza, ahelyett hogy újra lefuttatná. Nagy monorepo-kban ez a build időt **percekről másodpercekre** csökkentheti.

---

## Turborepo a mindennapi munkában

### Dev szerver indítása

A Turborepo `dev` task-ja **minden appot egyszerre indít**:

```
pnpm dev
  → apps/crm      → localhost:3001
  → apps/intra     → localhost:3000
  → apps/office    → localhost:3002
  → apps/finance   → localhost:3003
```

A `persistent: true` beállítás mondja meg, hogy ez a task nem áll le magától (dev szerver).

### Build és CI/CD

A [[cloud/ci-cd-pipelines|CI/CD]] pipeline-ban a Turborepo **csak az érintett appokat buildeli**:

```
PR: "packages/ui változott"
  → Turborepo: packages/ui KELL (változott)
  → Turborepo: apps/crm KELL (függ ui-tól)
  → Turborepo: apps/finance KELL (függ ui-tól)
  → Turborepo: packages/db SKIP (nem változott, nem függ ui-tól)
  → Turborepo: apps/portal SKIP (cache hit)
```

---

## Hogyan használd promptban?

Amikor AI-val dolgozol Turborepo-s projekten:

- *"Ez egy Turborepo monorepo. Add hozzá a `typecheck` taskot a turbo.json-höz, cache-elhető legyen, és ne függjön más taskoktól."*
- *"A build hibát dob a packages/auth-ban. Nézd meg a turbo.json-t, hogy a dependsOn jól van-e beállítva."*
- *"Adj hozzá egy új package-et `packages/email` néven és kösd be a turbo.json pipeline-ba."*

---

## Turborepo vs alternatívák

| Szempont | Turborepo | Nx | Lerna |
|----------|-----------|-----|-------|
| Fókusz | Build orchestráció + cache | Teljes monorepo toolkit | Package publishing |
| Komplexitás | Alacsony (1 fájl: turbo.json) | Magas (sok konfig) | Közepes |
| Sebesség | Nagyon gyors (Rust-ban írt) | Gyors | Lassabb |
| Remote cache | Vercel integráció beépítve | Nx Cloud | Nincs natív |
| Mikor válaszd | Monorepo build gyorsítás | Vállalati, komplex monorepo | npm package publishálás |
