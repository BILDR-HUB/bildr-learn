# bildr-learn — bildr.hub Tudasbazis

## Projekt leiras

Nyilvanos, open-source tudasbazis a bildr.hub kozosseg szamara. Magyar nyelvu fejlesztesi jegyzetek, eszkoz-dokumentaciok, architekturalis guidok. A repo Obsidian vault-kent is megnyithato.

## Repo struktura (reteg-alapu)

- `foundations/` — kodolasi alapok (Bash, Git, Linux, halozatok, Python, dev alapfogalmak)
- `frontend/` — kliens-oldali keretrendszerek (Next.js, React, CSS, TypeScript)
- `backend/` — szerver-oldali logika (Express, Hono, Auth, Edge fn, API-k)
- `database/` — adatbazis reteg (SQL, Drizzle, Prisma, Redis, Supabase)
- `cloud/` — felho es deploy (Docker, Kubernetes, Cloudflare, Vercel, Railway, Nginx)
- `toolbox/` — fejlesztoi eszkozok (tmux, Claude Code, Grafana, PostHog, fnm, Mac setup)
- `guides/` — hands-on gyakorlati utmutatok
- `_moc/` — Map of Content osszefoglalo hub oldalak

## Fajl konvenciok

- Fajlnev: kebab-case, kisbetus, ASCII (pl. `docker-alapok.md`, nem `Docker alapok.md`)
- Nyelv: magyar, tegezos, kozvetlen stilus; angol technikai kifejezesek
- Frontmatter: `tags`, `datum`, `szint`, `kapcsolodo`
- Szint ertekek: 🌱 Newcomer | 🧱 Brick | 🏗️ Builder
- Backlink format: `[[mappa/fajlnev|Megjeleno nev]]`

## Content szabalyok

- SOHA ne irj szemelyes neveket (sem fejleszto, sem ugyfel, sem ceges)
- NE hivatkozz konkret projektre vagy ugyfélre
- Hasznalj generikus kifejezeseket: "Mentor", "tapasztalt fejleszto", "a csapat"
- Minden tartalom legyen edukativ es altalanosan hasznos
- Embedded kepek helyett Mermaid diagramok
- Code blockokban mindig legyen language tag
- NE hasznalj `&` jelet — ird ki hogy "es"

## Obsidian kompatibilitas

A repo Obsidian vault-kent is megnyithato. A `[[backlink]]` szintaxis mukodik.
Mermaid diagramok, calloutok (`> [!tip]`, `> [!warning]`), tablazatok — mind Obsidian-kompatibilis.

## bildr.hub brand

- Nev: bildr.hub (kisbetus, ponttal)
- Szlogen: "Build. Learn. Grow."
- Hang: motivalo, oszinte, novekedés-orientalt, informalis ("te")
- Karrierszintek: 🌱 Newcomer - 🧱 Brick - 🏗️ Builder - 🏛️ Architect - ⚙️ Engineer - 🧠 Mastermind - 🌟 Mentor - 🌿 Founder

## Kontribucio

PR-alapu workflow — lasd CONTRIBUTING.md. NE commitolj kozvetlenul main-re.
