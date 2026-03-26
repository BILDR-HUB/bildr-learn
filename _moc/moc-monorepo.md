---
tags:
  - moc
datum: 2026-03-26
---

# MOC - Monorepo

> [!tldr]
> Ez a Map of Content összegyűjti a repo összes monorepo-hoz kapcsolódó jegyzetét. Alapelvektől a package struktúrán át a Cloudflare-specifikus felépítésig.

---

## Alapelvek

| Jegyzet | Leírás |
|---------|--------|
| [[toolbox/monorepo-management\|Monorepo management]] | Monorepo tooling áttekintés — Turborepo, Nx, pnpm workspaces, mikor érdemes monorepo |
| [[cloud/monorepo-navigacio\|Monorepo navigáció]] | Hogyan navigálj monorepo-ban — mappastruktúra, script-ek, hatékony keresés |

## Architektúra

| Jegyzet | Leírás |
|---------|--------|
| [[cloud/multi-app-monorepo-architektura\|Multi-app Monorepo architektúra]] | Több alkalmazás egy repóban — app-ok szétválasztása, shared kód, deploy stratégia |
| [[cloud/monorepo-package-struktura\|Monorepo package struktúra]] | Package szervezés — internal packages, shared lib-ek, Drizzle + Hono példák |

## Platform-specifikus

| Jegyzet | Leírás |
|---------|--------|
| [[cloud/cloudflare-monorepo-mappastruktura\|Cloudflare Monorepo Mappastruktúra]] | Cloudflare-specifikus monorepo — Workers, Pages, shared packages, wrangler konfig |

---

## Kapcsolódó note-ok

| Jegyzet | Kapcsolat |
|---------|-----------|
| [[toolbox/turborepo\|Turborepo]] | Build rendszer monorepo-khoz — caching, párhuzamos task-ok |
| [[foundations/pnpm\|pnpm]] | Csomagkezelő — workspaces support, hardlinkek, monorepo-kra kiváló |
| [[cloud/ci-cd-pipelines\|CI/CD Pipelines]] | Monorepo CI: affected detection, párhuzamos build |
| [[cloud/deployment-checklist\|Deployment checklist]] | Deploy ellenőrzés — monorepo-nál app-specifikus checklist kell |
| [[_moc/moc-cloudflare\|MOC - Cloudflare]] | Cloudflare platform — ha a monorepo Workers/Pages app-okat tartalmaz |

---

## Tanulási útvonal

Ajánlott sorrend ha nulláról kezded:
1. [[toolbox/monorepo-management\|Monorepo management]] — mikor érdemes monorepo, milyen toolok vannak
2. [[cloud/monorepo-navigacio\|Monorepo navigáció]] — hogyan tájékozódj egy nagy monorepo-ban
3. [[cloud/multi-app-monorepo-architektura\|Multi-app architektúra]] — app-ok szétválasztása, shared kód
4. [[cloud/monorepo-package-struktura\|Monorepo package struktúra]] — package-ek szervezése, internal lib-ek
5. [[cloud/cloudflare-monorepo-mappastruktura\|Cloudflare Monorepo]] — platform-specifikus felépítés (ha CF-et használsz)

> [!tip] Mikor monorepo?
> Ha 2+ szorosan kapcsolódó app-od van (pl. frontend + backend + shared types), vagy ha shared package-eket akarsz megosztani — monorepo. Ha teljesen független projektek, külön repo egyszerűbb.

---

## Hézagok

- [ ] Nx — Turborepo alternatíva, fejlettebb dependency graph, plugin rendszer
- [ ] Monorepo CI optimalizálás — affected detection, turbo remote caching
- [ ] Changesets — monorepo versioning és changelog automatizálás
