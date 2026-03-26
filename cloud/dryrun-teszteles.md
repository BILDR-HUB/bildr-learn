---
tags:
  - teszteles
  - devops
  - best-practice
datum: 2026-03-26
szint: "🧱 Brick"
kapcsolodo:
  - "[[foundations/git-es-github|Git]]"
  - "[[cloud/devops|DevOps]]"
  - "[[cloud/ci-cd-pipelines|CI/CD]]"
  - "[[cloud/deployment-checklist|Deployment checklist]]"
---

# Dry run tesztelés

## Mi az a dry run?

A **dry run** ("száraz futtatás") egy végrehajtási mód, ahol a rendszer **szimulál mindent, de nem hajt végre valódi mellékhatásokat**. Mutatja mit csinálna, de nem csinálja meg.

> [!tldr]
> **Dry run = próba élesben, hatás nélkül.** Ugyanaz a logika fut le, de az írás/törlés/küldés lépések kimaradnak. Teszteléshez, validáláshoz, demo-khoz.

```
Normál mód:     scrape → parse → INSERT INTO db → commit
Dry run mód:    scrape → parse → LOG "would insert 87 records" → done
```

---

## Mikor használd?

| Szituáció | Miért dry run |
|-----------|--------------|
| **Scraper tesztelés** | Ellenőrzöd a parse logikát anélkül, hogy a DB-t módosítanád |
| **Migráció futtatás** | Megnézed mit csinálna a migráció mielőtt élesben futna |
| **Deploy pipeline** | [[cloud/ci-cd-pipelines|CI/CD]]-ben megnézed mi történne deploy nélkül |
| **Email küldés** | Ellenőrzöd a template-et kiküldés nélkül |
| **Tömeges módosítás** | Batch update/delete előtt megnézed hány sort érintene |
| **CLI tool fejlesztés** | `--dry-run` flag mint standard konvenció |

---

## Dry run implementálási pattern-ek

### 1. Flag-alapú pattern

A legegyszerűbb - egy boolean flag vezérli:

```typescript
async function scrapeAndSave(source: string, dryRun = false) {
  const listings = await scrape(source)
  const parsed = listings.map(parseListing)

  if (dryRun) {
    console.log(`[DRY RUN] Would insert ${parsed.length} listings from ${source}`)
    console.log(`[DRY RUN] Sample:`, parsed[0])
    return { inserted: 0, wouldInsert: parsed.length }
  }

  const result = await db.insert(listingsTable).values(parsed)
  return { inserted: result.rowCount }
}
```

### 2. Más pattern-ek

| Pattern | Lényege | Mikor használd |
|---|---|---|
| **Strategy pattern** | `RealWriter` vs `DryRunWriter` interface - a `dryRun` flag alapján cseréled a writer-t | Komplexebb rendszer, több írási művelet (insert, delete, update) |
| **Wrapper/decorator** | `withDryRun(fn, label, isDryRun)` - meglévő függvény köré rak egy log réteget | Meglévő kódba retrofitteled a dry run-t, nem akarod átírni a logikát |

---

## CLI dry run példák

| Parancs | Mit mutat meg |
|---|---|
| `npx shadcn@latest add dialog --dry-run` | Fájlok, dependency-k, módosítások - írás nélkül |
| `git clean -n` | Mit törölne a clean |
| `git push --dry-run` | Mit pusholna |
| `git merge --no-commit --no-ff feature-branch` | Mit merge-ölne (utána `git merge --abort`) |

---

## Dry run best practices

> [!tip] Szabályok
> 1. **Minden destruktív művelet kapjon dry run opciót** - DELETE, UPDATE, migráció, tömeges küldés
> 2. **A dry run output legyen részletes** - ne csak "would do X", hanem mutasd az adatot
> 3. **A logika legyen azonos** - a dry run ugyanazt a kódútvonalat járja be, csak a végső write/delete marad el
> 4. **CLI tooloknál `--dry-run` flag** - ez a konvenció, ne `--test` vagy `--simulate`
> 5. **[[cloud/ci-cd-pipelines|CI/CD]] pipeline-ban alapértelmezetten dry run** - élesítés explicit legyen

### Dry run vs. más tesztelési módszerek

| Módszer | Mi fut | Valódi adat | Side effect |
|---------|--------|-------------|-------------|
| **Unit test** | Izolált függvények, mock-okkal | Nem | Nem |
| **Integration test** | Teljes flow, test DB-vel | Test adat | Test DB-ben igen |
| **Dry run** | Teljes flow, éles adattal | Igen (read-only) | Nem |
| **Staging** | Teljes flow, staging env-ben | Staging adat | Staging env-ben igen |
| **Canary** | Éles flow, kis %-ban | Éles adat | Igen, limitáltan |

> [!warning] Dry run != teszt
> A dry run **nem helyettesíti a teszteket**. A dry run az éles rendszeren fut (vagy éles adatokkal), és azt mutatja mit csinálna. A tesztek izoláltan ellenőrzik a logika helyességét. Mindkettő kell.

---

## AI-natív fejlesztés

A dry run pattern hozzáadása meglévő kódhoz tipikus refaktorálási feladat, amit Claude Code jól kezel. Leírod a destruktív műveleteket és az AI beépíti a flag-alapú vagy strategy pattern-t, a megfelelő logolással.

> [!tip] Hogyan használd AI-val
> - *"A scraper-emhez kell egy dry run mód - INSERT/UPDATE helyett logolja mit csinálna, és a végén summary-t adjon (hány sor, milyen táblák)"*
> - *"Adj hozzá `--dry-run` flaget ehhez a CLI toolhoz - a destruktív műveleteknél (delete, migrate) mutassa mit csinálna, de ne hajtsa végre"*
> - *"Írd le a mellékhatásokat a promptban (DB write, email küldés, fájl törlés) - így az AI pontosan tudja mely lépéseket kell dry run módban kihagyni"*

---

## Kapcsolódó

- [[foundations/git-es-github|Git]] - git dry run parancsok (`git clean -n`, `git push --dry-run`)
- [[cloud/deployment-checklist|Deployment checklist]] - dry run a deploy pipeline-ban
- [[cloud/devops|DevOps]] - [[cloud/ci-cd-pipelines|CI/CD]] dry run pattern-ek
