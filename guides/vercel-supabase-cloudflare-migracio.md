---
tags:
  - migracio
  - cloudflare
  - vercel
  - supabase
datum: 2026-03-05
szint: "🏗️ Builder"
kapcsolodo:
  - "[[cloud/cloudflare|Cloudflare]]"
  - "[[cloud/vercel|Vercel]]"
  - "[[database/supabase|Supabase]]"
---

# Vercel + Supabase → Cloudflare migracio

Hogyan migrálj egy működő [[frontend/nextjs|Next.js]] appot [[cloud/vercel|Vercel]] + [[database/supabase|Supabase]] stackről [[cloud/cloudflare|Cloudflare]] Workers + D1-re — lépésről lépésre, a buktatókkal együtt.

---

## Miért érdemes migrálni?

- **Költségcsökkentés:** Vercel Pro ($20/hó) + Supabase Pro ($25/hó) = $45/hó → CF Workers Paid ($5/hó) + D1 (free tier)
- **Egyszerűsítés:** Két külön szolgáltató helyett minden egy platformon (compute, DB, auth, DNS, CDN)
- **Cloudflare Access Zero Trust:** Jelszó nélküli auth, amit a Cloudflare kezel — 0 sor auth kód az appban

---

## Eredmény

A projekt él a CF Workers-ön, CF Access védi, D1 adatbázis fut, cron job ütemezve van. Működik, de a deployment pipeline felállítása meglepően sok buktatót tartalmaz.

---

## Ami jól működik

### Drizzle ORM migráció

A [[database/supabase|Supabase]] (PostgreSQL) → D1 (SQLite) váltás viszonylag gördülékeny a [[database/drizzle|Drizzle]]-nek köszönhetően. A séma nagyrészt 1:1 átfordítható, csak a típuskülönbségeket kell kezelni.

### CF Access Zero Trust auth

Brutálisan egyszerű. A teljes auth flow (login page, session, JWT) [[cloud/cloudflare|Cloudflare]] oldalon van, az app csak a `Cf-Access-Jwt-Assertion` headert olvassa ki. Nulla auth kód, nulla session kezelés.

### Server Actions pattern megmarad

A `getAuthUser() → getDb() → query` minta szinte változatlanul működik, csak a [[database/supabase|Supabase]] kliens helyett [[database/drizzle|Drizzle]] + D1 lesz.

### Wrangler CLI

Secretek, deploy, D1 migráció mind CLI-ből megy, nem kell dashboardon kattintgatni.

---

## Buktatók és megoldások

### 1. OpenNext build végtelen rekurzió

A legnagyobb buktató. A `package.json` `build` script `npx @opennextjs/cloudflare build` volt, de az OpenNext belsőleg `npm run build`-et hív (=`next build`). Ez végtelen rekurziót okoz — lokálisan 15+ node process indul, 8GB RAM elfogy, a CI-n is loopol.

**Megoldás:**
```json
{
  "build": "next build",
  "build:worker": "npx @opennextjs/cloudflare build"
}
```

A CF dashboard Build command-ja: `npm install && npx @opennextjs/cloudflare build` (közvetlenül, NEM `npm run build`-en keresztül).

> [!danger] Aranyszabály
> A `package.json` `build` scriptje MINDIG `next build` legyen ha OpenNext-et használsz. Az OpenNext maga hívja meg a `npm run build`-et belsőleg.

### 2. D1 SQLite vs PostgreSQL különbségek

- **JSON mezők:** Supabase-ben natív `jsonb` → D1-ben `text` + kézi `parseJson<T>()` / `toJson()` wrapper kell
- **Boolean mezők:** D1-ben `integer` mode: `'boolean'` (0/1), nem natív boolean
- **`ON CONFLICT DO NOTHING` + `RETURNING`:** PostgreSQL-ben működik, D1-ben nem kombinálható → külön count query kell
- **Batch limit:** D1 SQL statement méretnek van felső határa → nagy batch insert-eket kisebb darabokra kell bontani

### 3. DNS / Custom domain problémák

- A CF Access app és a Worker custom domain **ugyanazon az accounton** kell legyen — ha a DNS zóna máshol van, 1043-as error
- Régi DNS rekordok (pl. Vercel CNAME) törölése után is maradhat verification TXT rekord → ez is blokkolhatja a custom domain hozzáadást
- A DNS propagáció néhány percet vesz igénybe — nem azonnali, türelem kell

### 4. RLS hiánya D1-ben

[[database/supabase|Supabase]]-ben a Row Level Security automatikusan szűrte a user adatait. D1-ben **nincs RLS** — minden query-hez manuálisan hozzá kell adni az `eq(table.userId, userId)` feltételt. Ez IDOR sebezhetőséget okozhat ha elfelejtődik.

> [!warning] Minden D1 query-hez
> `and(eq(table.id, entityId), eq(table.userId, userId))` — IDOR protection kézzel, nincs automatikus védelem.

### 5. CF Access login page limitáció

A CF Access login page **account szintű** — nem lehet alkalmazásonként más branding (logo, háttérszín). Ha több ügyfélnek csinálsz appot ugyanazon az accounton, mindegyik ugyanúgy néz ki.

**Tanulság:** Multi-tenant esetben minden ügyfelet a saját CF accountjára kell vinni.

---

## Stack értékelés

| Eszköz/Szolgáltatás | Bevált? | Megjegyzés |
|---|---|---|
| **Cloudflare Workers** | igen | Olcsó, gyors, egy helyen minden |
| **D1 (SQLite)** | részben | Működik, de a JSON/boolean/conflict kezelés macerásabb mint PostgreSQL |
| **OpenNext adapter** | részben | Működik, de a build setup trükkös (rekurzió csapda) |
| **CF Access Zero Trust** | igen | Fantasztikus — 0 auth kód, secure by default |
| **[[database/drizzle|Drizzle]] ORM** | igen | A PostgreSQL→SQLite migráció meglepően sima volt |
| **Wrangler CLI** | igen | Secret management, deploy, tail mind CLI-ből |
| **CF Git Integration** | igen | Push to main → auto deploy, mint [[cloud/vercel|Vercel]] |

---

## Mikor érdemes migrálni?

### Érdemes ha:

- **Költség fontos** — $45/hó → $5/hó, tízszeres megtakarítás
- **Egyszerű belső app** — CRUD, dashboard, admin panel, kevés felhasználó
- **Auth egyszerű** — email OTP vagy company SSO (CF Access megoldja)
- **Nincs komplex PostgreSQL igény** — nincs full-text search, nincs PostGIS, nincsenek komplex joinok
- **Egy platformot akarsz** — DNS, CDN, compute, DB, auth, cron mind [[cloud/cloudflare|Cloudflare]]

### NE migrálj ha:

- **PostgreSQL-specifikus feature-ök kellenek** — JSONB operátorok, triggerek, RLS, full-text search, PostGIS
- **Supabase Realtime** — D1-nek nincs realtime subscription
- **Supabase Auth** — ha OAuth providereket (Google, GitHub) használsz és nem akarsz CF Access-re váltani
- **Supabase Storage** — fájl upload/download → CF R2 külön setup
- **Nagy adatbázis** — D1 méretkorlátja 10GB (free: 5GB)
- **Multi-tenant SaaS** — CF Access account-szintű branding limitáció

---

## AI workflow tippek

- **Wrangler CLI mindig megbízhatóbb** mint a dashboard screenshot-ok debugolása
- **Build log elemzés** — az AI azonnal felismerheti a végtelen rekurziót a logból
- **Inkrementális deploy tesztelés** — curl + header elemzés minden lépés után
- **CLAUDE.md + MEMORY.md** kombó az egész migráció alatt kontextusban tartja a döntéseket

---

## Összefoglaló tanulságok

- **`build` script = `next build`** — soha ne legyen `@opennextjs/cloudflare build` a package.json `build`-ben
- **D1-hez mindig kézi userId filter** — nincs RLS, IDOR protection manuális
- **DNS tisztítás migrálás előtt** — régi CNAME + TXT verification rekordok törlése, propagáció megvárása
- **CF Access = ingyenes enterprise auth** — ha nincs egyedi login UI igény, tökéletes megoldás
- **Multi-tenant = külön CF account per ügyfél** — ne közös accounton
- **`next.config.ts` cpus limitálás** — 8GB RAM-os gépeken `cpus: 2` + `workerThreads: false` a biztonság kedvéért lokális buildhez

---

## Kapcsolódó anyagok

- [[cloud/cloudflare|Cloudflare]] — a platform áttekintése, szolgáltatások, árazás
- [[cloud/vercel|Vercel]] — az eredeti hosting platform, összehasonlítás
- [[database/supabase|Supabase]] — az eredeti DB+auth, amit lecseréltünk
- [[database/drizzle|Drizzle]] — az ORM ami mindkét DB-t tudja kezelni
