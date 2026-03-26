# bildr-learn — bildr.hub Tudásbázis

## Projekt leírás

Nyilvános, open-source tudásbázis a bildr.hub közösség számára. Magyar nyelvű fejlesztési jegyzetek, eszköz-dokumentációk, architekturális guidok. A repo Obsidian vault-ként is megnyitható.

## Repo struktúra (réteg-alapú)

- `foundations/` — kódolási alapok (Bash, Git, Linux, hálózatok, Python, dev alapfogalmak)
- `frontend/` — kliens-oldali keretrendszerek (Next.js, React, CSS, TypeScript)
- `backend/` — szerver-oldali logika (Express, Hono, Auth, Edge fn, API-k)
- `database/` — adatbázis réteg (SQL, Drizzle, Prisma, Redis, Supabase)
- `cloud/` — felhő és deploy (Docker, Kubernetes, Cloudflare, Vercel, Railway, Nginx)
- `toolbox/` — fejlesztői eszközök (tmux, Claude Code, Grafana, PostHog, fnm, Mac setup)
- `guides/` — hands-on gyakorlati útmutatók
- `_moc/` — Map of Content összefoglaló hub oldalak

## Fájl konvenciók

- Fájlnév: kebab-case, kisbetűs, ASCII (pl. `docker-alapok.md`, nem `Docker alapok.md`)
- Nyelv: magyar, tegezős, közvetlen stílus; angol technikai kifejezések
- Frontmatter: `tags`, `datum`, `szint`, `kapcsolodo`
- Szint értékek: 🌱 Newcomer | 🧱 Scout | 🏗️ Builder
- Backlink formátum: `[[mappa/fajlnev|Megjelenő név]]`

## Content szabályok

- SOHA ne írj személyes neveket (sem fejlesztő, sem ügyfél, sem céges)
- NE hivatkozz konkrét projektre vagy ügyfélre
- Használj generikus kifejezéseket: "Mentor", "tapasztalt fejlesztő", "a csapat"
- Minden tartalom legyen edukatív és általánosan hasznos
- Embedded képek helyett Mermaid diagramok
- Code blockokban mindig legyen language tag
- NE használj `&` jelet — írd ki hogy "és"

## Obsidian kompatibilitás

A repo Obsidian vault-ként is megnyitható. A `[[backlink]]` szintaxis működik.
Mermaid diagramok, calloutok (`> [!tip]`, `> [!warning]`), táblázatok — mind Obsidian-kompatibilis.

## bildr.hub brand

- Név: bildr.hub (kisbetűs, ponttal)
- Szlogen: "Build. Learn. Grow."
- Hang: motiváló, őszinte, növekedés-orientált, informális ("te")
- Karrierszintek: 🌱 Newcomer - 🧱 Scout - 🏗️ Builder - 🏛️ Architect - 🌟 Mentor - 🌿 Founder

## Kontribúció

PR-alapú workflow — lásd CONTRIBUTING.md. NE commitolj közvetlenül main-re.
