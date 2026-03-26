---
tags:
  - adatbazis
  - auth
  - hosting
datum: 2026-03-06
szint: "🧱 Scout"
kapcsolodo:
  - "[[cloud/vercel|Vercel]]"
  - "[[cloud/railway|Railway]]"
  - "[[cloud/cloudflare|Cloudflare]]"
  - "[[frontend/nextjs|Next.js]]"
  - "[[database/drizzle|Drizzle]]"
  - "[[database/prisma|Prisma]]"
  - "[[backend/clerk|Clerk]]"
  - "[[database/sql-adatbazisok|SQL adatbázisok]]"
  - "[[database/sql-index-szabalyok|SQL Index szabalyok]]"
---

# Supabase

**Kategoria:** `adatbazis` / `auth` / `storage` / `API`
**URL:** https://supabase.com
**Ar/Terv:** Free (2 projekt) / Pro ($25/hó per projekt)

---

## Mi ez és mire jó?

A Supabase egy **nyilt forráskódu Firebase alternativa**, ami PostgreSQL adatbázist ad, plusz rengeteg extra funkciot kore. Egy helyen kapod még az adatbázist, az autentikaciot, a fájl tárolást és az automatikus API-t.

**Egyszerűen:** Supabase = az adatbázisod + user kezeles + fájl tárolás, mindet egy dashboardrol kezeled.

**Amit kapsz:**
- **PostgreSQL adatbázis** -- igazi SQL adatbázis, nem NoSQL
- **Auth** -- email/jelszó, Google, GitHub, magic link bejelentkezes
- **Storage** -- fájlok (kepek, PDF-ek) tárolása
- **Realtime** -- valós ideju frissítesek (chat, live dashboard)
- **Edge Functions** -- szerver oldali kod (Deno)
- **Auto-generalt API** -- a táblakhoz automatikusan REST és GraphQL API

**Mikor használd:**
- Adatbázis kell a projekthez (a legtobb esetben)
- User regisztrácio / bejelentkezes kell
- Fájlokat kell tárolnii (profilkepek, dokumentumok)
- [[frontend/nextjs|Next.js]] + [[cloud/vercel|Vercel]] melle backend-nek

**Mikor NE használd:**
- Ha nagyon komplex backend logika kell → saját backend [[cloud/railway|Railway]]-en
- Ha NoSQL kell (MongoDB) → nem erre valo
- Ha a 2 ingyenes projekt limit nem eleg és nem akarsz fizetni

**Alternativak:** Firebase, PlanetScale, Neon, Appwrite

---

## Setup -- lépésrol lépésre

### 1. Regisztrácio
- supabase.com → Sign up GitHub-bal
- Hozz letre egy új projektet → valassz regiot (EU ha EU userek lesznek: Frankfurt)
- Jegyezd még / mentsd el a database password-ot

### 2. Alap konfigurácio
- A dashboardon: Settings → API → itt talalod a kulcsokat:
  - `SUPABASE_URL` -- a projekt URL-je
  - `SUPABASE_ANON_KEY` -- publikus kulcs (kliens oldalra OK)
  - `SUPABASE_SERVICE_ROLE_KEY` -- **titkos kulcs, SOHA ne kerüljon a frontendre**

### 3. Projekt beállítás (Next.js)

```bash
npm install @supabase/supabase-js
```

```typescript
// lib/supabase.ts
import { createClient } from '@supabase/supabase-js'

export const supabase = createClient(
  process.env.NEXT_PUBLIC_SUPABASE_URL!,
  process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!
)
```

`.env.local`:
```
NEXT_PUBLIC_SUPABASE_URL=https://xxxxx.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=eyJhbGciOiJIUzI1NiIs...
SUPABASE_SERVICE_ROLE_KEY=eyJhbGciOiJIUzI1NiIs...
```

### 4. Tábla létrehozasa
- Dashboard → Table Editor → New Table
- Vagy SQL Editor-ban irj CREATE TABLE-t

---

## Best Practices

### Architektúra / Struktúra

- Minden táblahoz legyen `id` (uuid, auto-generated), `created_at` (timestamp)
- Használj **foreign key**-eket a táblak kozott (pl. `user_id` → `users.id`)
- Az API hivasokat egy helyre szervezd: `lib/supabase.ts` vagy `lib/db/` mappa

### Biztonság -- RLS (Row Level Security)

**Ez a legfontosabb dolog Supabase-ben.** RLS nelkul BARKI lekerhet az Összes adatot.

```sql
-- RLS bekapcsolasa
ALTER TABLE projects ENABLE ROW LEVEL SECURITY;

-- Csak a sajat adataidat latod
CREATE POLICY "Users see own projects" ON projects
  FOR SELECT USING (auth.uid() = user_id);

-- Csak magadnak tudsz beszurni
CREATE POLICY "Users insert own projects" ON projects
  FOR INSERT WITH CHECK (auth.uid() = user_id);
```

> **Szabaly:** Új tábla → MINDIG kapcsold be az RLS-t és irj policy-kat.

### Teljesítmény

- Adj **indexet** a gyakran keresett oszlopokra (`CREATE INDEX`)
- Használj `select()` szűrot -- ne kerd le az összes oszlopot ha nem kell
- A Realtime csak akkor kell ha tényleg live frissítes kell -- feleslegesen ne kapcsold be

### Költségoptimalizalas

- Free tier: 500MB adatbázis, 1GB fájl storage, 50K auth userek → kisebb projekthez eleg
- Ha pauzalod a projektet (7 nap inaktivitas utan auto-pauzal free-n) → az adatbázis elalszik, de az adat megmarad
- Ha közeleg a limit → nézd még Dashboard → Usage

---

## Gyakori mintak / Használati esetek

### 1. Next.js app user kezelessel

```
Vercel (Next.js) → Supabase Auth (login/register) → Supabase DB (user adatok)
```

### 2. Fájl feltöltés (profilkep, dokumentum)

```typescript
const { data, error } = await supabase.storage
  .from('avatars')
  .upload(`${userId}/avatar.png`, file)
```

### 3. Valós ideju funkcio (chat, live dashboard)

```typescript
supabase
  .channel('messages')
  .on('postgres_changes', { event: 'INSERT', schema: 'public', table: 'messages' },
    (payload) => console.log('Uj uzenet:', payload.new)
  )
  .subscribe()
```

---

## Buktatok és hibak amiket elkerülj

- **RLS nelkul az adatbázis nyitott** -- ez a #1 hiba. Mindig kapcsold be
- **`service_role` kulcs a frontenden** -- ha ez kikerul, barki bármit csinálhat. Ez CSAK szerver oldalra valo
- **Free tier auto-pause** -- ha 7 napig nem használod, a DB elalszik. Első keres lassu lesz. Pro-n ez nincs
- **Tábla módosítas Dashboard-on production-ben** -- inkabb SQL migráciokat irj, hogy reprodukalhato legyen
- **Nincs `updated_at` trigger** -- alapbol nem frissíti. Csinálj triggert hozzá:

```sql
CREATE OR REPLACE FUNCTION update_updated_at()
RETURNS TRIGGER AS $$
BEGIN
  NEW.updated_at = NOW();
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;
```

---

## Hasznos parancsok / kodreszletek

```bash
# Supabase CLI telepites
npm install -g supabase

# Lokalis Supabase inditas (Docker kell hozza)
supabase init
supabase start

# Migracio letrehozasa
supabase migration new create_projects_table

# Migracio futtatasa production-ben
supabase db push
```

---

## Hasznos linkek

- Docs: https://supabase.com/docs
- Dashboard: https://supabase.com/dashboard
- Kozosseg/Discord: https://discord.supabase.com
- Statusz oldal: https://status.supabase.com
- GitHub: https://github.com/supabase/supabase

---

## Kapcsolodo anyagok
- [[cloud/vercel|Vercel]]
- [[cloud/railway|Railway]]
- [[cloud/cloudflare|Cloudflare]] -- edge alternativa: D1 (SQLite) olcsobb de egyszerűbb mint PostgreSQL
- [[frontend/nextjs|Next.js]]
- [[database/drizzle|Drizzle]]
- [[database/prisma|Prisma]]
- [[backend/clerk|Clerk]]
- [[database/sql-adatbazisok|SQL adatbázisok]]
- [[database/sql-index-szabalyok|SQL Index szabalyok]]
