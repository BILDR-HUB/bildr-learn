---
tags:
  - adatbazis
  - sql
  - postgresql
datum: 2026-03-06
szint: "🏗️ Builder"
kapcsolodo:
  - "[[database/sql-adatbazisok|SQL adatbázisok]]"
  - "[[database/sql-index-szabalyok|SQL Index szabályok]]"
  - "[[database/supabase|Supabase]]"
  - "[[database/vector-adatbazisok|Vector adatbázisok]]"
  - "[[database/drizzle|Drizzle]]"
  - "[[database/prisma|Prisma]]"
  - "[[_moc/moc-database|MOC - Database]]"
---

# PostgreSQL specifikus tudás

## Összefoglaló

A [[database/sql-adatbazisok|PostgreSQL]] az alapértelmezett adatbázis a modern web stack-ben — de a legtöbb fejlesztő csak az alap CRUD műveleteket használja belőle. Ez a jegyzet a **három legértékesebb haladó feature-t** mutatja be: **JSONB mezők**, **full-text search** és **Row Level Security (RLS)**.

## JSONB — relációs + dokumentum kezelés egyben

A JSONB (Binary JSON) lehetővé teszi, hogy **strukturálatlan, rugalmas adatokat** tárolj egy relációs táblában — anélkül, hogy MongoDB-re váltanál.

### Alapműveletek

```sql
-- Tábla JSONB oszloppal
CREATE TABLE products (
  id serial PRIMARY KEY,
  name text NOT NULL,
  metadata jsonb DEFAULT '{}'::jsonb,
  created_at timestamptz DEFAULT now()
);

-- Beszúrás
INSERT INTO products (name, metadata) VALUES (
  'Widget Pro',
  '{"color": "blue", "weight_kg": 1.2, "tags": ["premium", "new"], "specs": {"width": 10, "height": 5}}'
);

-- Lekérdezés: top-level mező
SELECT * FROM products WHERE metadata->>'color' = 'blue';

-- Beágyazott mező
SELECT * FROM products WHERE metadata->'specs'->>'width' = '10';

-- Tömb elem keresés (tartalmazza-e?)
SELECT * FROM products WHERE metadata->'tags' ? 'premium';

-- Részleges JSON frissítés (nem írja felül az egészet)
UPDATE products
SET metadata = metadata || '{"color": "red", "stock": 42}'::jsonb
WHERE id = 1;

-- Mező eltávolítása
UPDATE products
SET metadata = metadata - 'stock'
WHERE id = 1;
```

### JSONB operátorok

| Operátor | Mit csinál | Példa |
|----------|-----------|-------|
| `->` | JSON elem lekérése (JSON típusú) | `metadata->'specs'` |
| `->>` | JSON elem szövegként | `metadata->>'color'` |
| `?` | Tartalmazza-e a kulcsot | `metadata ? 'color'` |
| `?&` | Tartalmazza-e az összes kulcsot | `metadata ?& array['color','weight_kg']` |
| `@>` | Tartalmazza-e a JSON részt | `metadata @> '{"color":"blue"}'` |
| `\|\|` | JSON merge | `metadata \|\| '{"new_key": "val"}'` |
| `-` | Kulcs eltávolítása | `metadata - 'key'` |

### JSONB index — ne legyen lassú

```sql
-- GIN index: gyors @>, ?, ?& operátorokhoz
CREATE INDEX idx_products_metadata ON products USING GIN (metadata);

-- Specifikus mező index (ha mindig egy mezőre szűrsz)
CREATE INDEX idx_products_color ON products ((metadata->>'color'));
```

> [!tip] Mikor JSONB, mikor külön tábla?
> **JSONB:** flexibilis, ritkán keresett metaadat (settings, feature flags, extra attribútumok). **Külön tábla:** gyakran keresett, JOIN-olt adat, ahol index és constraint kell.

## Full-text search — beépített keresőmotor

A PostgreSQL beépített full-text search engine-je sokszor **feleslegessé teszi az Elasticsearch / Algolia integrációt**. Magyar nyelvet is támogat.

### Hogyan működik?

```mermaid
graph LR
    DOC[Szöveg:<br/>'PostgreSQL gyors<br/>adatbázis'] -->|to_tsvector| VEC[tsvector:<br/>'adatbázi':3<br/>'gyors':2<br/>'postgresql':1]
    Q[Keresés:<br/>'gyors adatbázis'] -->|to_tsquery| QV[tsquery:<br/>'gyors' & 'adatbázi']
    VEC -->|@@| MATCH[Match!]
    QV -->|@@| MATCH
```

1. **`to_tsvector()`** — szöveget tokenekké alakítja (stemming, stop words)
2. **`to_tsquery()`** — keresési kifejezést query-vé
3. **`@@`** — a match operátor

### Alap használat

```sql
-- Egyszerű keresés
SELECT * FROM posts
WHERE to_tsvector('hungarian', title || ' ' || content)
      @@ to_tsquery('hungarian', 'adatbázis & keresés');

-- Relevancia pontszámmal (ranking)
SELECT
  title,
  ts_rank(
    to_tsvector('hungarian', title || ' ' || content),
    to_tsquery('hungarian', 'adatbázis')
  ) AS rank
FROM posts
WHERE to_tsvector('hungarian', title || ' ' || content)
      @@ to_tsquery('hungarian', 'adatbázis')
ORDER BY rank DESC;
```

### Generált oszlop + GIN index (production)

```sql
-- Generált tsvector oszlop (nem kell minden query-ben számolni)
ALTER TABLE posts ADD COLUMN search_vector tsvector
  GENERATED ALWAYS AS (
    setweight(to_tsvector('hungarian', coalesce(title, '')), 'A') ||
    setweight(to_tsvector('hungarian', coalesce(content, '')), 'B')
  ) STORED;

-- GIN index a gyors kereséshez
CREATE INDEX idx_posts_search ON posts USING GIN (search_vector);

-- Keresés az indexelt oszloppal
SELECT title, ts_rank(search_vector, query) AS rank
FROM posts,
     to_tsquery('hungarian', 'postgresql & gyors') AS query
WHERE search_vector @@ query
ORDER BY rank DESC
LIMIT 20;
```

> [!info] `setweight` — címnek nagyobb súly
> Az `'A'` súly a legmagasabb, `'D'` a legalacsonyabb. A cím (`title`) A súlyú, a tartalom (`content`) B — így a cím match-ek előrébb kerülnek.

### Fuzzy search (hasonlóság alapú)

Ha elgépelés-toleráns keresés kell:

```sql
-- pg_trgm extension (trigram matching)
CREATE EXTENSION IF NOT EXISTS pg_trgm;

-- Hasonlóság keresés
SELECT name, similarity(name, 'Postgrse') AS sim
FROM products
WHERE name % 'Postgrse'    -- % operátor: hasonlóság > threshold
ORDER BY sim DESC;

-- GIN index a trigram kereséshez
CREATE INDEX idx_products_name_trgm ON products USING GIN (name gin_trgm_ops);
```

## Row Level Security (RLS)

Az RLS lehetővé teszi, hogy **az adatbázis szintjén kontrolláld, ki milyen sorokat láthat és módosíthat**. Ez az alapja a [[database/supabase|Supabase]] auth modelljének.

### Miért fontos?

Hagyományos megközelítés: az alkalmazás kódjában szűrsz (`WHERE user_id = currentUser`). Ha egy helyen elfelejtesz szűrni, adatszivárgás.

RLS megközelítés: az **adatbázis** szűr automatikusan — a fejlesztő nem tud véletlen "mindent" lekérni.

### RLS beállítása

```sql
-- 1. RLS bekapcsolása a táblán
ALTER TABLE projects ENABLE ROW LEVEL SECURITY;

-- 2. Alapértelmezés: semmit nem lát senki (deny-all)
-- 3. Policy-k definiálása: ki mit csinálhat

-- Olvasás: csak a saját projektjeit látja
CREATE POLICY "select_own_projects" ON projects
  FOR SELECT
  USING (user_id = auth.uid());

-- Beszúrás: csak magának hozhat létre
CREATE POLICY "insert_own_projects" ON projects
  FOR INSERT
  WITH CHECK (user_id = auth.uid());

-- Frissítés: csak a sajátját módosíthatja
CREATE POLICY "update_own_projects" ON projects
  FOR UPDATE
  USING (user_id = auth.uid())
  WITH CHECK (user_id = auth.uid());

-- Törlés: csak a sajátját törölheti
CREATE POLICY "delete_own_projects" ON projects
  FOR DELETE
  USING (user_id = auth.uid());
```

### Haladó RLS minták

```sql
-- Team-alapú hozzáférés: láthatod a csapatod projektjeit is
CREATE POLICY "team_select_projects" ON projects
  FOR SELECT
  USING (
    user_id = auth.uid()
    OR team_id IN (
      SELECT team_id FROM team_members
      WHERE user_id = auth.uid()
    )
  );

-- Admin mindent lát
CREATE POLICY "admin_select_all" ON projects
  FOR SELECT
  USING (
    EXISTS (
      SELECT 1 FROM users
      WHERE id = auth.uid() AND role = 'admin'
    )
  );

-- Service role bypass (backend API-hoz)
-- A Supabase service_role key automatikusan megkerüli az RLS-t
```

> [!warning] RLS teljesítmény
> Komplex RLS policy-k (subquery-kkel) lassíthatják a lekérdezéseket. Tartsd egyszerűen, és mindig legyen [[database/sql-index-szabalyok|index]] a policy-ban használt oszlopokon (pl. `user_id`, `team_id`).

## Mikor használd?

| Feature | Mikor IGEN | Mikor NE |
|---------|-----------|----------|
| **JSONB** | Flexibilis metadata, settings, változó attribútumok | Gyakran JOIN-olt, indexelt relációs adat |
| **Full-text search** | Egyszerű-közepes keresés, magyar nyelv | Komplex faceted search (→ Elasticsearch) |
| **RLS** | Multi-tenant app, Supabase, közvetlen DB hozzáférés kliensről | Hagyományos backend ahol a szerver szűr |

## Kapcsolódó

- [[database/sql-adatbazisok|SQL adatbázisok]] — SQL alapok, PostgreSQL bemutatása
- [[database/sql-index-szabalyok|SQL Index szabályok]] — GIN, GiST index típusok részletesen
- [[database/supabase|Supabase]] — RLS-re épülő auth modell
- [[database/vector-adatbazisok|Vector adatbázisok]] — pgvector extension a PostgreSQL-hez
- [[database/drizzle|Drizzle]] — TypeScript ORM PostgreSQL-hez
- [[database/prisma|Prisma]] — magas szintű ORM PostgreSQL-hez
- [[_moc/moc-database|MOC - Database]]
