---
tags:
  - deployment
  - devops
  - checklist
datum: 2026-03-06
szint: "🧱 Scout"
kapcsolodo:
  - "[[_moc/moc-deployment|MOC - Deployment]]"
  - "[[cloud/12-faktoros-alkalmazas-epites|12 Faktoros alkalmazás építes]]"
  - "[[cloud/docker-alapok|Docker alapok]]"
  - "[[cloud/docker-compose|Docker Compose]]"
---

# Belső deployment checklist

> [!info] Kinek szol?
> Ez egy belső checklist a csapatnak. Claude Code-dal dolgozunk -- de deploy elott vegig kell menni ezeken a lépéseken, mert a legtobb production bug és security issue megelözheto.

---

## Univerzalis checklist (MINDEN deploy elott)

Ezek stack-fuggetlenek -- **mindig** vegig kell rajtuk menni.

### Kod minoseg

- [ ] **Claude Code `/review`** -- futtasd le a repon, nézd at a javaslatokat
- [ ] **Nincs hardcoded secret** a kódban (API key, jelszó, token) -- minden `.env`-ben van
- [ ] **`.env.example` fájl** naprakesz -- minden env változó benne van leirassal
- [ ] **`.gitignore`** tartalmazza: `.env`, `.env.local`, `node_modules/`, `.next/`, `dist/`
- [ ] **Nincs `console.log`** production kódban (debug logok kitakaritva)
- [ ] **Error handling** -- API hivasok `try/catch`-ben, user-facing hibaüzenetek ertelmesek

### Security

- [ ] **Aikido** -- GitHub repo csatlakoztatva, scan lefutott, CRITICAL/HIGH NINCS
- [ ] **Dependency audit** -- `bun audit` vagy `npm audit`, nincs ismert CVE
- [ ] **API rate limiting** -- minden publikus endpoint-on van (kulonosen auth, form submit)
- [ ] **CORS** -- csak a saját domain-ek engedélyezve, nem `*`
- [ ] **Input validacio** -- user input MINDIG validalva server-side (Zod, vagy SQL parameterezés)
- [ ] **Auth ellenőrzes** -- vedett route-ok tényleg vedettek (nem csak frontend-en)
- [ ] **HTTP headers** -- `X-Frame-Options`, `Content-Security-Policy`, `Strict-Transport-Security`

> [!warning] Fontos
> Ha nem vagy biztos egy security kerdesben, kerdezd még Claude Code-ot: *"Van security vulnerability ebben a kódban?"* -- megtalálja a legtobbet.

### Git és verziókezeles

- [ ] **Minden commitolva** -- `git status` tiszta
- [ ] **Feature branch**-rol merge-oltel main-be (nem kozvetlenul main-re push)
- [ ] **PR review** megtortent (akar Claude Code `/review`, akar kollega)
- [ ] **Nincs merge conflict** -- clean merge

### Tesztelés

- [ ] **Lokálisan működik** -- `npm / bun run build` hiba nelkul lefut
- [ ] **Alap user flow tesztelve** -- regisztrácio/login, fo funkciok, form submit
- [ ] **Mobile nezet** ellenőrizve (responsive)
- [ ] **404 és error page-ek** -- működnek, nem ures feher kepernyo

---

## Stack-specifikus checklistek

### [[database/supabase|Supabase]] + [[cloud/vercel|Vercel]] (Next.js app)

Ez a leggyakoribb stack: [[frontend/nextjs|Next.js]] frontend [[cloud/vercel|Vercel]]-en, [[database/supabase|Supabase]] backend (DB + Auth + Storage).

#### Supabase

- [ ] **RLS (Row Level Security)** -- MINDEN táblan BE van kapcsolva, policy-k tesztelve
- [ ] **Supabase Advisors** lefuttatva -- Dashboard → Advisors → Security + Performance
- [ ] **Database migration-ok** rendben -- `supabase db push` vagy migration fájlok
- [ ] **Anon key** csak publikus műveletekhez van használva (RLS vedi!)
- [ ] **Service role key** SOHA nincs kliens oldalon -- csak server-side / Edge Function
- [ ] **Auth provider-ek** konfigurálva -- redirect URL-ek production domain-re allitva
- [ ] **Storage bucket policy-k** -- publikus/privat bucket-ek megfelelően beállítva
- [ ] **Edge Function-ok** deployolva -- `supabase functions deploy`

> [!tip] Supabase security one-liner
> Claude Code-ban: *"Futtasd le a Supabase advisors-t és mondd még mi a baj"* -- az MCP tool megcsinalja.

#### Vercel

- [ ] **Environment variables** -- Vercel Dashboard → Settings → Env Variables feltoltve
  - `NEXT_PUBLIC_SUPABASE_URL`
  - `NEXT_PUBLIC_SUPABASE_ANON_KEY`
  - `SUPABASE_SERVICE_ROLE_KEY` (nem `NEXT_PUBLIC_`!)
  - Egyéb API key-ek
- [ ] **Domain** beállítva -- custom domain + SSL automatikus
- [ ] **Preview deployment** tesztelve -- Vercel PR preview-n atnezve
- [ ] **Build log** -- nincs warning ami production-ban probléma lehet
- [ ] **Serverless function timeout** -- ha kell, novelve (alapbol 10s Hobby-n)
- [ ] **Analytics** bekapcsolva -- Vercel Analytics vagy PostHog

---

### [[cloud/railway|Railway]] (backend / API / service)

Amikor saját backend kell: API szerver, háttérfolyamatok, worker-ek, vagy saját DB.

- [ ] **Dockerfile** vagy **Nixpacks** -- Railway felismeri a buildkonfigot
- [ ] **Health check endpoint** -- `GET /health` → 200 OK (Railway ezt figyeli)
- [ ] **Environment variables** -- Railway Dashboard-on beállítva
- [ ] **Start command** helyes -- `bun run start` vagy `node dist/index.js`
- [ ] **Port** -- az app a `PORT` env változobol olvassa (Railway dinamikusan adja)
- [ ] **Persistent storage** -- ha kell, volume csatolva (pl. SQLite, fájlok)
- [ ] **Auto-deploy** -- GitHub repo csatlakoztatva, main branch deploy automatikus
- [ ] **Resource limits** -- nem fut el a memoria/CPU (Railway Dashboard → Metrics)
- [ ] **Database backup** -- ha Railway DB-t használsz, backup konfigurálva
- [ ] **Restart policy** -- automatikus ujrainditas crash eseten

> [!tip] Railway + Docker
> Ha `Dockerfile` van a repoban, Railway automatikusan azt használja. Ha nincs, Nixpacks probalja kitalalni -- de mindig jobb ha van explicit Dockerfile. Lásd [[cloud/docker-alapok|Docker alapok]].

---

### Hetzner / [[cloud/hostinger|Hostinger]] VPS (saját szerver)

Amikor teljes kontroll kell: n8n, self-hosted szolgáltatasok, vagy egyedi setup.

#### Szerver setup

- [ ] **SSH kulcs** beállítva -- jelszavas login KIKAPCSOLVA
- [ ] **Firewall (UFW)** -- csak a szükséges portok nyitva (22, 80, 443)
- [ ] **Fail2ban** telepítve -- brute force védelem SSH-ra
- [ ] **Automatikus frissítesek** -- `unattended-upgrades` bekapcsolva
- [ ] **Non-root user** -- az app nem root-kent fut

#### App deploy

- [ ] **Docker** -- az app konténerben fut, nem kozvetlenul a szerveren
- [ ] **[[cloud/docker-compose|Docker Compose]]** -- `docker-compose.yml` definiálalja a service-eket
- [ ] **Reverse proxy** -- [[cloud/nginx|Nginx]] vagy [[cloud/traefik|Traefik]] a konténer elott (SSL terminalas)
- [ ] **SSL tanúsítvány** -- Let's Encrypt (Certbot) vagy [[cloud/traefik|Traefik]] automatikus
- [ ] **Domain DNS** -- A record a szerver IP-re mutat
- [ ] **Logok** -- `docker logs` elérheto, nem telik még a disk
- [ ] **Monitoring** -- Grafana + Prometheus vagy legalabb uptime check (UptimeRobot)
- [ ] **Backup** -- adatbázis volume rendszeres mentése (cron + rsync/rclone)

#### Ha n8n fut rajta

- [ ] **n8n** Docker konténerben fut, volume-mal a flow-knak
- [ ] **Webhook URL** -- publikusan elérheto (Traefik/Nginx mögött, SSL-lel)
- [ ] **n8n credentials** -- ne legyen plaintext, n8n saját encryption-t használ
- [ ] **Execution data takaritas** -- regi execution-ok törlése (memoria!)
- [ ] **Tailscale** -- ha a szerver nem publikus, Tailscale VPN-en keresztul erheto el

---

## Deploy utani ellenőrzes (MINDEN stack)

- [ ] **Éles URL betolt** -- nem 500, nem 404, nem ures
- [ ] **Login/auth működik** -- production-ben is, nem csak lokálisan
- [ ] **Fo user flow** vegigmegy -- az amit az ugyfel használni fog
- [ ] **Mobile-on is működik** -- telefonrol tesztelve
- [ ] **HTTPS** -- zold lakat, nincs mixed content warning
- [ ] **Aikido** scan ujra -- deploy utan is lefuttatni
- [ ] **Error monitoring** -- bekapcsolva (Vercel/Railway logok, vagy Sentry)
- [ ] **Ugyfelnek kommunikalva** -- URL, belepesi adatok, bármilyen instrukció

---

## Gyors döntési fa: melyik stack-et használd?

```mermaid
graph TD
    START[Uj projekt] --> Q1{Van frontend?}
    Q1 -->|igen| Q2{Kell sajat backend<br/>logika?}
    Q1 -->|nem, csak backend| RAILWAY[Railway]

    Q2 -->|nem, Supabase eleg| VERCEL_SUPA[Vercel + Supabase]
    Q2 -->|igen, sajat API kell| VERCEL_RAIL[Vercel + Railway]

    START --> Q3{Self-hosted<br/>szolgaltatas?}
    Q3 -->|n8n, custom service| VPS[Hostinger VPS<br/>+ Docker + Traefik]

    style VERCEL_SUPA fill:#22c55e,color:white
    style VERCEL_RAIL fill:#3b82f6,color:white
    style RAILWAY fill:#7c3aed,color:white
    style VPS fill:#f97316,color:white
```

## Claude Code tippek deploy-hoz

Ezeket a promptokat használd Claude Code-ban deploy korul:

| Mikor | Prompt |
|-------|--------|
| Kod review | `/review` vagy *"nézd at a kódot security szempontbol"* |
| Env check | *"van hardcoded secret vagy API key a kódban?"* |
| Build hiba | *"miért nem buildel? itt a hibaüzenet: [paste]"* |
| Supabase RLS | *"ellenőrizd hogy minden táblan van-e RLS policy"* |
| Docker help | *"irj Dockerfile-t ehhez a Next.js projekthez"* |
| SSL setup | *"hogyan allitsak be Let's Encrypt-et Traefik-kel?"* |

## Kapcsolodo

- [[_moc/moc-deployment|MOC - Deployment]]
- Aikido
- [[cloud/12-faktoros-alkalmazas-epites|12 Faktoros alkalmazás építes]]
- [[cloud/docker-alapok|Docker alapok]]
- [[cloud/docker-compose|Docker Compose]]
- Env változók Next.js-ben
- Tailscale
