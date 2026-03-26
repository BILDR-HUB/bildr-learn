---
tags:
  - monorepo
  - architektura
  - best-practice
datum: 2026-03-26
szint: "🏗️ Builder"
kapcsolodo:
  - "[[cloud/monorepo-package-struktura|Monorepo package struktúra]]"
  - "[[cloud/cloudflare-monorepo-mappastruktura|Cloudflare Monorepo Mappastruktúra]]"
  - "[[cloud/12-faktoros-alkalmazas-epites|12-faktor]]"
  - "[[cloud/deployment-checklist|Deployment checklist]]"
  - "[[cloud/ci-cd-pipelines|CI/CD]]"
---

# Multi-app Monorepo architektúra

## Mi az a multi-app monorepo?

A [[cloud/monorepo-package-struktura|Monorepo package struktúra]] note elmagyarázza, miért kell szétválasztani a package-eket (core/shared/frontend/functions). De mi van, ha **több önálló alkalmazásod** van, amik közös kódot használnak?

A **multi-app [[toolbox/monorepo-management|Monorepo]]** = egy git repo, ahol az `apps/` mappában több önálló alkalmazás él, a `packages/` mappában pedig a közös kód, amit bármelyik app importálhat.

---

## Az apps/ + packages/ minta

Ez a struktúra két fő mappára épül:

| Mappa | Tartalom | Példa |
|-------|----------|-------|
| `apps/` | Önálló, deployolható alkalmazások | Webapp, admin panel, ügyfél portál, landing page |
| `packages/` | Megosztott kód, amit az appok importálnak | UI komponensek, auth, DB kliens, közös típusok, konfig |

Egy példaprojekt felépítése:

```
projekt/
├── apps/
│   ├── webapp/     — Fő alkalmazás
│   ├── finance/    — Pénzügyi modul
│   ├── admin/      — Belső admin felület
│   ├── office/     — Irodai eszközök
│   └── portal/     — Ügyfél portál
├── packages/
│   ├── auth/       — Autentikáció
│   ├── config/     — Közös TypeScript és ESLint konfig
│   ├── db/         — D1 adatbázis kliens
│   ├── types/      — Közös típusdefiníciók
│   └── ui/         — Közös UI komponensek
```

A lényeg: **5 app, de a logika nagy része a packages-ben él**. Ha változik az auth logika, egy helyen frissíted és mind az 5 app megkapja.

---

## Hogyan viszonyul a 4-package mintához?

A [[cloud/monorepo-package-struktura|Monorepo package struktúra]] a **core/shared/functions/frontend** szétválasztást mutatja be - az egy **egyetlen app** esete, ahol a backend és frontend közötti határt akarod kikényszeríteni.

A multi-app minta erre **ráépül**:

| Szempont | Egy-app monorepo | Multi-app monorepo |
|----------|-----------------|-------------------|
| Fő probléma | Frontend ne lássa a backend kódot | Több app közös kódot használjon |
| `apps/` tartalom | 1 frontend + 1 API | N darab önálló alkalmazás |
| `packages/` tartalom | core, shared, functions | auth, db, ui, types, config |
| Példa | SPA + [[backend/hono|Hono]] Worker | Webapp + Admin + Portál + Finance |
| Build orchestráció | Opcionális | **Kötelező** (-> Turborepo) |

> [!tip] A kettő kombinálható
> Egy multi-app monorepo-ban az egyes appok belsejében is alkalmazhatod a 4-package mintát - pl. az `apps/webapp/` alatt lehet saját `lib/` a webapp-specifikus logikának, miközben a közös dolgok a `packages/`-ből jönnek.

---

## Mikor érdemes multi-app monorepo?

### Igen, használd ha:
- **Több frontend app** van közös designnal -> `packages/ui` megosztja a komponenseket
- **Közös autentikáció** kell -> `packages/auth` egy helyen
- **Egy adatbázist** használ több app -> `packages/db` biztosítja a konzisztens query-ket
- **Egy csapat** fejleszt több összefüggő alkalmazást
- A deploy-ok **összehangoltak** - staging-en együtt teszteled

### Nem, ne használd ha:
- **Egy app van** -> simán a 4-package minta elég (lásd [[cloud/monorepo-package-struktura|Monorepo package struktúra]])
- Az appok **teljesen függetlenek** egymástól, nincs megosztott kód
- **Különböző tech stack** - pl. egy Go backend és egy React frontend (ilyenkor polyrepo jobb)
- **Különböző csapatok** dolgoznak az appokon, saját release ciklussal

---

## Package-ek tipikus felosztása

| Package típus | Mit tartalmaz | Melyik app használja |
|--------------|---------------|---------------------|
| `packages/ui` | Közös komponensek (Sidebar, Header, Card, Modal) | Mind |
| `packages/auth` | Login, session kezelés, jogosultságok | Mind |
| `packages/db` | Adatbázis kliens, query-k, séma | Mind ami adatbázist ér |
| `packages/types` | Közös TypeScript típusok | Mind |
| `packages/config` | TSConfig, ESLint, Tailwind konfig | Mind |

> [!info] A packages/ könyvtár = a közös "igazság forrása"
> Ha egy UI komponens vagy típus a `packages/`-ben van, akkor **garantáltan** ugyanazt a verziót használja minden app. Nincs "nálam működik, nálad nem" probléma.

---

## AI-natív fejlesztés

A multi-app monorepo az a terület ahol az AI kontextus-megadás a legfontosabb. Claude Code-nak tudnia kell melyik app és melyik package a célpont - monorepoban az "adj hozzá egy táblázatot" túl homályos, mert 5 app és 5 package közül kell választania.

> [!tip] Hogyan használd AI-val
> - *"Ez egy pnpm monorepo 5 Next.js app-pal az apps/ alatt. A közös UI a packages/ui-ban van. Adj hozzá egy DataTable komponenst a packages/ui-hoz amit az apps/webapp és apps/finance is használni fog."*
> - *"A packages/db-ben van a D1 adatbázis kliens. Adj hozzá egy új query-t az invoices táblához, és használd az apps/finance-ban."*
> - *"Monorepo-ban a kontextus duplán fontos - mindig add meg a relatív útvonalat (pl. packages/ui/ vagy apps/webapp/), különben rossz helyre írja a kódot"*

---

## Build és deploy

Multi-app monorepo-ban **nem mindig kell mindent újrabuildelni**. A Turborepo (vagy hasonló eszköz) figyeli, melyik package változott, és csak az érintett appokat buildeli:

```
apps/webapp ───────┐
apps/finance ─────►│ packages/ui    → Ha a UI változik, mind újrabildelődik
apps/portal ──────►│ packages/auth  → Ha az auth változik, mind újrabildelődik
apps/admin ───────►│ packages/db    → Ha a DB változik, ami DB-t használ újrabildelődik
apps/office ──────┘
```

A [[cloud/deployment-checklist|Deployment checklist]] elvei itt is érvényesek, de minden appra külön-külön alkalmazva. A [[cloud/ci-cd-pipelines|CI/CD]] pipeline-ban pl. `dorny/paths-filter`-rel csak a változott appokat deployolhatod.

---

## Kapcsolódó

- [[cloud/monorepo-package-struktura|Monorepo package struktúra]] - Az alapvető 4-package szétválasztás (core/shared/functions/frontend)
- [[cloud/cloudflare-monorepo-mappastruktura|Cloudflare Monorepo Mappastruktúra]] - Cloudflare-specifikus monorepo setup
- [[cloud/12-faktoros-alkalmazas-epites|12-faktor]] - Cloud-native elvek, amik a multi-app architektúrára is vonatkoznak
- [[cloud/deployment-checklist|Deployment checklist]] - Deploy folyamat, ami monorepo-ban app-onként külön fut
