# Kontribúció a bildr-learn tudásbázishoz

## Hogyan járulhatsz hozzá?

### Új jegyzet hozzáadása

1. Fork-old a repót VAGY nyiss egy új branch-et: `git checkout -b note/[tema-neve]`
2. Hozd létre a fájlt a megfelelő mappában (lásd: Mappa választás)
3. Kövesd a fájl elnevezési konvenciót (lásd: Fájlnév szabályok)
4. Írj frontmatter-t (lásd: Frontmatter sablon)
5. Nyiss Pull Request-et

### Meglévő jegyzet javítása

1. Branch: `git checkout -b fix/[tema-neve]`
2. Szerkeszd a fájlt
3. PR-ben írd le mit változtattál és miért

---

## Mappa választás

| Réteg | Mappa | Ide kerül |
|-------|-------|-----------|
| Alapok | `foundations/` | Kódolási koncept, nyelvi feature, OS, hálózat, dev alapok |
| Kliens | `frontend/` | Kliens-oldali keretrendszerek, UI, SSR/SSG, stílusok |
| Szerver | `backend/` | Szerver-oldali logika, API-k, auth, edge functions |
| Adatbázis | `database/` | Adatbázis, ORM, cache, BaaS |
| Felhő | `cloud/` | Deploy, konténerizáció, orchestration, hosting platformok |
| Eszköztár | `toolbox/` | Fejlesztői eszközök, monitoring, IDE, setup |
| Gyakorlat | `guides/` | Step-by-step hands-on útmutatók |
| Térkép | `_moc/` | Map of Content összefoglaló (csak ha 4+ jegyzet van egy témában) |

---

## Fájlnév szabályok

- **Kebab-case**, kisbetűs, ASCII (ékezet nélkül)
- Jó: `docker-alapok.md`, `nextjs-data-cache.md`, `sql-index-szabalyok.md`
- Rossz: `Docker Alapok.md`, `next.js data cache.md`, `SQL Index szabályok.md`
- NE tegyél dátumot a fájlnévbe

---

## Frontmatter sablon

Minden jegyzet elején kötelezően:

```yaml
---
tags:
  - [fo-tema-tag]
datum: YYYY-MM-DD
szint: "🌱 Newcomer"   # vagy "🧱 Scout" vagy "🏗️ Builder"
kapcsolodo:
  - "[[mappa/fajlnev|Megjelenő név]]"
---
```

### Szint értékek

- **🌱 Newcomer** — bevezető tartalom, első lépések
- **🧱 Scout** — középhaladók, már van alapvető tapasztalat
- **🏗️ Builder** — haladó, architekturális döntések

---

## Stílus vezérfonal

- **Nyelv:** magyar, tegezős, közvetlen, barátságos
- **Technikai kifejezések:** angolul (Docker, Kubernetes, ORM, API, stb.)
- **Bold:** kulcsfogalmakra az első előforduláskor
- **Code block:** mindig language tag-gel (`ts`, `bash`, `sql`, `yaml`, stb.)
- **Táblázat:** összehasonlításokhoz, strukturált adatokhoz
- **Callout:** `> [!tip]`, `> [!warning]`, `> [!info]` — fontos kiemelések
- **Mermaid diagram:** architektúra, flow, döntés-fa vizualizáláshoz
- **Backlink:** `[[mappa/fajlnev|Megjelenő név]]` formátumban

---

## NE csináld

- NE írj személyes neveket (sem fejlesztő, sem ügyfél, sem céges)
- NE hivatkozz konkrét ügyfélre, projektre vagy szervezetre
- NE használj embedded képeket — Mermaid diagramot vagy leírást használj
- NE írj frontmatter nélkül
- NE commitolj közvetlenül main-re — mindig nyiss PR-t
- NE használj `&` jelet — írd ki hogy "és"
- NE használj `–` (en-dash) — használj `-` (kötőjel)
