# bildr-learn Repo Structure

## Mappastruktúra (réteg-alapú)

```
bildr-learn/
├── README.md                # Landing page
├── CLAUDE.md                # Claude Code project context
├── CONTRIBUTING.md          # Kontribúció guide
├── LICENSE                  # MIT
├── .claude/skills/bildr-learn/
│   ├── SKILL.md
│   └── references/
│       └── repo-structure.md  (ez a fájl)
├── foundations/             # Alapok — Bash, Git, Linux, hálózatok, Python, dev alapok
├── frontend/                # Kliens — Next.js, React, CSS, TypeScript
├── backend/                 # Szerver — Express, Hono, Auth (JWT, Clerk), Edge fn
├── database/                # Adatbázis — SQL, Drizzle, Prisma, Redis, Supabase
├── cloud/                   # Felhő — Docker, K8s, Cloudflare, Vercel, Railway, Nginx, deploy
├── toolbox/                 # Eszköztár — tmux, Claude Code, Grafana, PostHog, fnm, Mac setup
├── guides/                  # Gyakorlat — Hands-on step-by-step útmutatók
└── _moc/                    # Térképek — Map of Content hub oldalak
```

## Routing Guide — Melyik tartalom hova kerül?

| Réteg | Mappa | Példák |
|-------|-------|--------|
| Alapok | `foundations/` | Bash parancssor, Git, Linux, hálózatok, Python venv, szoftverfejlesztés alapjai, npm, TypeScript vs Python |
| Kliens | `frontend/` | Next.js, React, CSS, TypeScript, Env változók, Data Cache, SSR/SSG, CMS |
| Szerver | `backend/` | Express, Hono, JWT, Clerk, Edge function, API design |
| Adatbázis | `database/` | SQL, Drizzle, Prisma, Redis, Supabase, indexek, ORM-ek |
| Felhő | `cloud/` | Docker, Docker Compose, Kubernetes, DevOps, Cloudflare, Vercel, Railway, Nginx, Traefik, deploy, 12-factor |
| Eszköztár | `toolbox/` | tmux, vi, Claude Code, Grafana, PostHog, Aikido, Tailscale, fnm, Mac setup |
| Gyakorlat | `guides/` | Step-by-step tutorialok, migrációs guidok, hands-on gyakorlatok |
| Térkép | `_moc/` | Map of Content — témaköri összefoglalók (4+ jegyzet esetén) |

## Fájlnév szabályok

1. **Kebab-case** — szavak kötőjellel: `docker-alapok.md`
2. **Kisbetűs** — nincs nagybetű: `nextjs.md` nem `NextJS.md`
3. **ASCII** — ékezet nélkül: `halozatok-es-ip-cimek.md` nem `hálózatok-és-ip-címek.md`
4. **Nincs dátum** a fájlnévben — a dátum a frontmatter-be kerül
5. **Nincs szóköz** — kötőjellel elválasztva

## Frontmatter sablon

```yaml
---
tags:
  - [fo-tema-tag]
datum: YYYY-MM-DD
szint: "🌱 Newcomer"   # vagy "🧱 Brick" vagy "🏗️ Builder"
kapcsolodo:
  - "[[mappa/fajlnev|Megjelenő név]]"
---
```

## Dynamic Folder Creation szabályok

- **Threshold:** 4+ jegyzet egy al-témáról egy rétegen belül
- **Példa:** 4 Kubernetes jegyzet a `cloud/` mappában → javaslat: `cloud/kubernetes/`
- **Mindig user jóváhagyás kell** a mappa létrehozásához és fájl áthelyezéshez
- **Áthelyezés után:** minden backlink és MOC referencia frissítendő

## Backlink formátum

Mindig teljes útvonallal és megjelenő névvel:
```
[[mappa/fajlnev|Megjelenő név]]
```

Példák:
- `[[cloud/docker-alapok|Docker alapok]]`
- `[[frontend/nextjs|Next.js]]`
- `[[_moc/moc-kubernetes|MOC - Kubernetes]]`
