# Kontribucio a bildr-learn tudasbazishoz

## Hogyan jarulhatsz hozza?

### Uj jegyzet hozzaadasa

1. Fork-old a repot VAGY nyiss egy uj branch-et: `git checkout -b note/[tema-neve]`
2. Hozd letre a fajlt a megfelelo mappaban (lasd: Mappa valasztas)
3. Kovessd a fajl elnevezesi konvenciot (lasd: Fajlnev szabalyok)
4. Irj frontmatter-t (lasd: Frontmatter sablon)
5. Nyiss Pull Request-et

### Meglevo jegyzet javitasa

1. Branch: `git checkout -b fix/[tema-neve]`
2. Szerkeszd a fajlt
3. PR-ben ird le mit valtoztatal es miert

---

## Mappa valasztas

| Reteg | Mappa | Ide kerul |
|-------|-------|-----------|
| Alapok | `foundations/` | Kodolasi koncept, nyelvi feature, OS, halozat, dev alapok |
| Kliens | `frontend/` | Kliens-oldali keretrendszerek, UI, SSR/SSG, stilusok |
| Szerver | `backend/` | Szerver-oldali logika, API-k, auth, edge functions |
| Adatbazis | `database/` | Adatbazis, ORM, cache, BaaS |
| Felho | `cloud/` | Deploy, kontenerizacio, orchestration, hosting platformok |
| Eszkoztar | `toolbox/` | Fejlesztoi eszkozok, monitoring, IDE, setup |
| Gyakorlat | `guides/` | Step-by-step hands-on utmutatok |
| Terkep | `_moc/` | Map of Content osszefoglalo (csak ha 4+ jegyzet van egy temaban) |

---

## Fajlnev szabalyok

- **Kebab-case**, kisbetus, ASCII (ekezet nelkul)
- Jo: `docker-alapok.md`, `nextjs-data-cache.md`, `sql-index-szabalyok.md`
- Rossz: `Docker Alapok.md`, `next.js data cache.md`, `SQL Index szabályok.md`
- NE tegyel datumot a fajlnevbe

---

## Frontmatter sablon

Minden jegyzet elejen kotelezoen:

```yaml
---
tags:
  - [fo-tema-tag]
datum: YYYY-MM-DD
szint: "🌱 Newcomer"   # vagy "🧱 Brick" vagy "🏗️ Builder"
kapcsolodo:
  - "[[mappa/fajlnev|Megjeleno nev]]"
---
```

### Szint ertekek

- **🌱 Newcomer** — bevezeto tartalom, elso lepesek
- **🧱 Brick** — kozephaladok, mar van alapveto tapasztalat
- **🏗️ Builder** — halado, architekturalis dontesek

---

## Stilus vezerlo

- **Nyelv:** magyar, tegezos, kozvetlen, baratsagos
- **Technikai kifejezesek:** angolul (Docker, Kubernetes, ORM, API, stb.)
- **Bold:** kulcsfogalmakra az elso elofordulaskor
- **Code block:** mindig language tag-gel (`ts`, `bash`, `sql`, `yaml`, stb.)
- **Tablazat:** osszehasonlitasokhoz, strukturalt adatokhoz
- **Callout:** `> [!tip]`, `> [!warning]`, `> [!info]` — fontos kiemelések
- **Mermaid diagram:** architektura, flow, dontes-fa vizualizalashoz
- **Backlink:** `[[mappa/fajlnev|Megjeleno nev]]` formatumban

---

## NE csinald

- NE irj szemelyes neveket (sem fejleszto, sem ugyfel, sem ceges)
- NE hivatkozz konkret ugyfélre, projektre vagy szervezetre
- NE hasznalj embedded kepeket — Mermaid diagramot vagy leiras hasznalj
- NE irj frontmatter nelkul
- NE commitolj kozvetlenul main-re — mindig nyiss PR-t
- NE hasznalj `&` jelet — ird ki hogy "es"
- NE hasznalj `–` (en-dash) — hasznalj `-` (kotojel)
