# bildr-learn Repo Structure

## Mappastruktura (reteg-alapu)

```
bildr-learn/
├── README.md                # Landing page
├── CLAUDE.md                # Claude Code project context
├── CONTRIBUTING.md          # Kontribucio guide
├── LICENSE                  # MIT
├── .claude/skills/bildr-learn/
│   ├── SKILL.md
│   └── references/
│       └── repo-structure.md  (ez a fajl)
├── foundations/             # Alapok — Bash, Git, Linux, halozatok, Python, dev alapok
├── frontend/                # Kliens — Next.js, React, CSS, TypeScript
├── backend/                 # Szerver — Express, Hono, Auth (JWT, Clerk), Edge fn
├── database/                # Adatbazis — SQL, Drizzle, Prisma, Redis, Supabase
├── cloud/                   # Felho — Docker, K8s, Cloudflare, Vercel, Railway, Nginx, deploy
├── toolbox/                 # Eszkoztar — tmux, Claude Code, Grafana, PostHog, fnm, Mac setup
├── guides/                  # Gyakorlat — Hands-on step-by-step utmutatok
└── _moc/                    # Terkepek — Map of Content hub oldalak
```

## Routing Guide — Melyik tartalom hova kerul?

| Reteg | Mappa | Peldak |
|-------|-------|--------|
| Alapok | `foundations/` | Bash parancssor, Git, Linux, halozatok, Python venv, szoftverfejlesztes alapjai, npm, TypeScript vs Python |
| Kliens | `frontend/` | Next.js, React, CSS, TypeScript, Env valtozok, Data Cache, SSR/SSG, CMS |
| Szerver | `backend/` | Express, Hono, JWT, Clerk, Edge function, API design |
| Adatbazis | `database/` | SQL, Drizzle, Prisma, Redis, Supabase, indexek, ORM-ek |
| Felho | `cloud/` | Docker, Docker Compose, Kubernetes, DevOps, Cloudflare, Vercel, Railway, Nginx, Traefik, deploy, 12-factor |
| Eszkoztar | `toolbox/` | tmux, vi, Claude Code, Grafana, PostHog, Aikido, Tailscale, fnm, Mac setup |
| Gyakorlat | `guides/` | Step-by-step tutorials, migracios guidok, hands-on gyakorlatok |
| Terkep | `_moc/` | Map of Content — temakori osszefoglalok (4+ jegyzet eseten) |

## Fajlnev szabalyok

1. **Kebab-case** — szavak kotojelel: `docker-alapok.md`
2. **Kisbetus** — nincs nagybetu: `nextjs.md` nem `NextJS.md`
3. **ASCII** — ekezet nelkul: `halozatok-es-ip-cimek.md` nem `hálózatok-és-ip-címek.md`
4. **Nincs datum** a fajlnevben — a datum a frontmatter-be kerul
5. **Nincs szokoz** — kotojelel elvalasztva

## Frontmatter sablon

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

## Dynamic Folder Creation szabalyok

- **Threshold:** 4+ jegyzet egy al-temarol egy retegen belul
- **Pelda:** 4 Kubernetes jegyzet a `cloud/` mappaban → javaslat: `cloud/kubernetes/`
- **Mindig user jovahagyas kell** a mappa letrehozasahoz es fajl athelyezeshez
- **Athelyezes utan:** minden backlink es MOC referencia frissitendo

## Backlink format

Mindig teljes utvonallal es megjeleno nevvel:
```
[[mappa/fajlnev|Megjeleno nev]]
```

Peldak:
- `[[cloud/docker-alapok|Docker alapok]]`
- `[[frontend/nextjs|Next.js]]`
- `[[_moc/moc-kubernetes|MOC - Kubernetes]]`
