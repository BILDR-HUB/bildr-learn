---
tags:
  - adatbazis
  - auth
  - hosting
datum: 2026-03-06
szint: "🧱 Brick"
kapcsolodo:
  - "[[cloud/vercel|Vercel]]"
  - "[[cloud/railway|Railway]]"
  - "[[cloud/cloudflare|Cloudflare]]"
  - "[[frontend/nextjs|Next.js]]"
  - "[[database/drizzle|Drizzle]]"
  - "[[database/prisma|Prisma]]"
  - "[[backend/clerk|Clerk]]"
  - "[[database/sql-adatbazisok|SQL adatbazisok]]"
  - "[[database/sql-index-szabalyok|SQL Index szabalyok]]"
---

# Supabase

**Kategoria:** `adatbazis` / `auth` / `storage` / `API`
**URL:** https://supabase.com
**Ar/Terv:** Free (2 projekt) / Pro ($25/ho per projekt)

---

## Mi ez es mire jo?

A Supabase egy **nyilt forraskodu Firebase alternativa**, ami PostgreSQL adatbazist ad, plusz rengeteg extra funkciot kore. Egy helyen kapod meg az adatbazist, az autentikaciot, a fajl tarolast es az automatikus API-t.

**Egyszeruen:** Supabase = az adatbazisod + user kezeles + fajl tarolas, mindet egy dashboardrol kezeled.

**Amit kapsz:**
- **PostgreSQL adatbazis** -- igazi SQL adatbazis, nem NoSQL
- **Auth** -- email/jelszo, Google, GitHub, magic link bejelentkezes
- **Storage** -- fajlok (kepek, PDF-ek) tarolasa
- **Realtime** -- valos ideju frissitesek (chat, live dashboard)
- **Edge Functions** -- szerver oldali kod (Deno)
- **Auto-generalt API** -- a tablakhoz automatikusan REST es GraphQL API

**Mikor hasznald:**
- Adatbazis kell a projekthez (a legtobb esetben)
- User regisztracio / bejelentkezes kell
- Fajlokat kell tarolni (profilkepek, dokumentumok)
- [[frontend/nextjs|Next.js]] + [[cloud/vercel|Vercel]] melle backend-nek

**Mikor NE hasznald:**
- Ha nagyon komplex backend logika kell → sajat backend [[cloud/railway|Railway]]-en
- Ha NoSQL kell (MongoDB) → nem erre valo
- Ha a 2 ingyenes projekt limit nem eleg es nem akarsz fizetni

**Alternativak:** Firebase, PlanetScale, Neon, Appwrite

---

## Setup -- lepesrol lepesre

### 1. Regisztracio
- supabase.com → Sign up GitHub-bal
- Hozz letre egy uj projektet → valassz regiot (EU ha EU userek lesznek: Frankfurt)
- Jegyezd meg / mentsd el a database password-ot

### 2. Alap konfiguracio
- A dashboardon: Settings → API → itt talalod a kulcsokat:
  - `SUPABASE_URL` -- a projekt URL-je
  - `SUPABASE_ANON_KEY` -- publikus kulcs (kliens oldalra OK)
  - `SUPABASE_SERVICE_ROLE_KEY` -- **titkos kulcs, SOHA ne keruljon a frontendre**

### 3. Projekt beallitas (Next.js)

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

### 4. Tabla letrehozasa
- Dashboard → Table Editor → New Table
- Vagy SQL Editor-ban irj CREATE TABLE-t

---

## Best Practices

### Architektura / Struktura

- Minden tablahoz legyen `id` (uuid, auto-generated), `created_at` (timestamp)
- Hasznalj **foreign key**-eket a tablak kozott (pl. `user_id` → `users.id`)
- Az API hivasokat egy helyre szervezd: `lib/supabase.ts` vagy `lib/db/` mappa

### Biztonsag -- RLS (Row Level Security)

**Ez a legfontosabb dolog Supabase-ben.** RLS nelkul BARKI lekerhet az OSSZES adatot.

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

> **Szabaly:** Uj tabla → MINDIG kapcsold be az RLS-t es irj policy-kat.

### Teljesitmeny

- Adj **indexet** a gyakran keresett oszlopokra (`CREATE INDEX`)
- Hasznalj `select()` szurot -- ne kerd le az osszes oszlopot ha nem kell
- A Realtime csak akkor kell ha tenyleg live frissites kell -- feleslegesen ne kapcsold be

### Koltsegoptimalizalas

- Free tier: 500MB adatbazis, 1GB fajl storage, 50K auth userek → kisebb projekthez eleg
- Ha pauzalod a projektet (7 nap inaktivitas utan auto-pauzal free-n) → az adatbazis elalszik, de az adat megmarad
- Ha kozeleg a limit → nezd meg Dashboard → Usage

---

## Gyakori mintak / Hasznalati esetek

### 1. Next.js app user kezelessel

```
Vercel (Next.js) → Supabase Auth (login/register) → Supabase DB (user adatok)
```

### 2. Fajl feltoltes (profilkep, dokumentum)

```typescript
const { data, error } = await supabase.storage
  .from('avatars')
  .upload(`${userId}/avatar.png`, file)
```

### 3. Valos ideju funkcio (chat, live dashboard)

```typescript
supabase
  .channel('messages')
  .on('postgres_changes', { event: 'INSERT', schema: 'public', table: 'messages' },
    (payload) => console.log('Uj uzenet:', payload.new)
  )
  .subscribe()
```

---

## Buktatok es hibak amiket elkerulj

- **RLS nelkul az adatbazis nyitott** -- ez a #1 hiba. Mindig kapcsold be
- **`service_role` kulcs a frontenden** -- ha ez kikerul, barki barmit csinalhat. Ez CSAK szerver oldalra valo
- **Free tier auto-pause** -- ha 7 napig nem hasznalod, a DB elalszik. Elso keres lassu lesz. Pro-n ez nincs
- **Tabla modositas Dashboard-on production-ben** -- inkabb SQL migraciokat irj, hogy reprodukalhato legyen
- **Nincs `updated_at` trigger** -- alapbol nem frissiti. Csinalj triggert hozza:

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
- [[cloud/cloudflare|Cloudflare]] -- edge alternativa: D1 (SQLite) olcsobb de egyszerubb mint PostgreSQL
- [[frontend/nextjs|Next.js]]
- [[database/drizzle|Drizzle]]
- [[database/prisma|Prisma]]
- [[backend/clerk|Clerk]]
- [[database/sql-adatbazisok|SQL adatbazisok]]
- [[database/sql-index-szabalyok|SQL Index szabalyok]]
