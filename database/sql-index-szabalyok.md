---
tags:
  - adatbazis
  - sql
  - teljesitmeny
datum: 2026-03-06
szint: "🧱 Brick"
kapcsolodo:
  - "[[database/sql-adatbazisok|SQL adatbazisok]]"
  - "[[database/supabase|Supabase]]"
  - "[[database/prisma|Prisma]]"
  - "[[_moc/moc-database|MOC - Database]]"
---

## Mi az a SQL index es miert kell?

Az index olyan mint egy konyv tartalomjegyzeke: ahelyett hogy az adatbazis vegigolvas minden sort (full table scan), az index alapjan kozvetlenul ugrik a keresett sorokra.

**Nelkul:**
```sql
SELECT * FROM posts WHERE user_id = 42;
-- → vegigolvassa az osszes sort, keresi ahol user_id = 42
-- 1 millio sornal: 1 millio osszehasonlitas
```

**Indexszel:**
```sql
CREATE INDEX idx_posts_user_id ON posts(user_id);
-- → B-tree-ben keresi: O(log n) muveletek
-- 1 millio sornal: ~20 osszehasonlitas
```

---

## Mikor tegyel indexet

### Indexelj ha

**1. WHERE szuroben szerepel a mezo**
```sql
-- Minden ilyen lekerdezesnel index kell user_id-re:
SELECT * FROM posts WHERE user_id = 42;
SELECT * FROM orders WHERE status = 'pending' AND created_at > '2024-01-01';
```

**2. JOIN-ban szerepel (foreign key-ek)**
```sql
-- A "sok" oldal foreign key mezojet mindig indexeld
SELECT p.*, u.name
FROM posts p
JOIN users u ON u.id = p.user_id;   -- p.user_id-re kell index
```

> [!warning] Foreign key ≠ automatikus index
> PostgreSQL-ben a foreign key constraint NEM hoz letre automatikusan indexet. Kezzel kell: `CREATE INDEX idx_posts_user_id ON posts(user_id);`

**3. ORDER BY-ban szerepel**
```sql
-- Ha az eredmenyt rendezed es szurod is egyszerre:
SELECT * FROM posts WHERE published = true ORDER BY created_at DESC;
-- Compound index kell: (published, created_at)
```

**4. Magas kardinalitasu mezo** (sok egyedi ertek)
- email, user_id, order_id → jo jelolt
- boolean (true/false), status (3-4 ertek) → onmagaban gyenge index

---

## Mikor NE tegyel indexet

### Ne indexelj ha

**1. Kis tablan** (< 1000 sor)
- Az index overhead nagyobb mint a full scan nyereseg

**2. Write-heavy tabla**
- Minden `INSERT`, `UPDATE`, `DELETE` frissiti az indexet is → lassit
- Log tablak, audit trail, event stream: keves index

**3. Alacsony kardinalitasu mezo onmagaban**
```sql
-- BAD: is_active csak true/false -- az index alig segit
CREATE INDEX idx_users_is_active ON users(is_active);

-- BETTER: compound indexben mas mezövel kombinalva
CREATE INDEX idx_users_active_created ON users(is_active, created_at);
```

**4. Ritkan futo lekerdezesnel**
- Ha naponta 1x fut egy report query, nincs ertelme indexelni

---

## Compound (osszetett) index szabalyai

A compound index **sorrendje kritikus**.

### Szabaly: a legszukebb szuro elore

```sql
-- Lekerdezes: WHERE user_id = 42 AND status = 'active'
-- HELYES: user_id elore (magasabb kardinalitas)
CREATE INDEX idx_orders_user_status ON orders(user_id, status);

-- Ez a lekerdezes is hasznalja (prefix matching):
WHERE user_id = 42                -- ✅ hasznalja az indexet
WHERE user_id = 42 AND status = 'active'  -- ✅ teljes index

-- Ez NEM:
WHERE status = 'active'           -- ❌ nem prefix, nem hasznalja
```

### Szabaly: ORDER BY megegyezo irany

```sql
-- Lekerdezes: WHERE published = true ORDER BY created_at DESC
CREATE INDEX idx_posts_published_created ON posts(published, created_at DESC);
-- Ha az ORDER BY iranya megegyezik az index iranyaval → index-only scan lehetseges
```

### Covering index (INCLUDE)

Ha a SELECT-ben levo mezok mind az indexben vannak, az adatbazis az alaptablat sem kell olvasni:

```sql
-- PostgreSQL INCLUDE szintaxis:
CREATE INDEX idx_posts_user_covering
ON posts(user_id)
INCLUDE (title, created_at);

-- Ez a lekerdezes index-only scan lesz:
SELECT title, created_at FROM posts WHERE user_id = 42;
```

---

## Index tipusok (PostgreSQL)

| Tipus | Mire jo | Alapertelmezett? |
|---|---|---|
| **B-tree** | Egyenloseg, tartomany (`=`, `<`, `>`, `BETWEEN`) | Igen |
| **Hash** | Csak egyenloseg (`=`), gyorsabb mint B-tree erre a muveletre | Ritkan |
| **GIN** | Tomb, JSON, full-text search | JSON/tomb mezonel |
| **GiST** | Geometriai adatok, full-text (regebbi) | Geo adatoknal |
| **BRIN** | Fizikailag rendezett, nagy tablak (pl. timestamp log tabla) | Idosor adatoknal |

```sql
-- GIN index JSON mezore (Supabase-nel metadata column)
CREATE INDEX idx_events_metadata ON events USING GIN (metadata);

-- Full-text search
CREATE INDEX idx_posts_search ON posts USING GIN (to_tsvector('english', title || ' ' || content));
```

---

## Partial index (reszleges index)

Csak a sorok egy reszet indexeli -- kisebb meret, gyorsabb frissites:

```sql
-- Csak az aktiv user-eket indexeli (a torolt account-ok nelkul)
CREATE INDEX idx_users_active_email ON users(email)
WHERE deleted_at IS NULL;

-- Csak a nem teljesitett rendelesek
CREATE INDEX idx_orders_pending ON orders(created_at)
WHERE status = 'pending';
```

Hasznos ha a lekerdezesek nagy reszenel mindig ugyanazt a szurot alkalmazod.

---

## Indexek ellenorzese es debugolas

### EXPLAIN ANALYZE -- nez-e az index?

```sql
EXPLAIN ANALYZE
SELECT * FROM posts WHERE user_id = 42 ORDER BY created_at DESC;
```

**Mit keress a outputban:**

```
Index Scan using idx_posts_user_id on posts  ← ✅ indexet hasznal
Seq Scan on posts                            ← ⚠️ full table scan, nincs index
Bitmap Index Scan                            ← ✅ index, de tobb sort kap vissza
```

### Meglevo indexek listazasa

```sql
-- Osszes index egy tablan
SELECT indexname, indexdef
FROM pg_indexes
WHERE tablename = 'posts';

-- Unused indexek (amit soha nem hasznal az adatbazis)
SELECT schemaname, tablename, indexname, idx_scan
FROM pg_stat_user_indexes
WHERE idx_scan = 0
ORDER BY tablename;
```

> [!tip] Unused indexek torlese
> Ha egy index `idx_scan = 0`, soha nem hasznalta az adatbazis → torlheto. Csak terheli a write performance-t.

---

## Prisma-specifikus indexek

A [[database/prisma|Prisma]] schema.prisma-ban az indexek:

```prisma
model Post {
  id        String   @id @default(cuid())
  userId    String
  title     String
  published Boolean  @default(false)
  createdAt DateTime @default(now())

  @@index([userId])                              // FK index (kötelezö)
  @@index([published, createdAt(sort: Desc)])    // compound, szures + rendezes
  @@index([userId, published])                   // WHERE userId AND published
}
```

---

## Okolszabalyok osszefoglalva

```
1. Minden foreign key-re → index
2. WHERE + ORDER BY kombinaciohoz → compound index (szuro elore, rendezes moge)
3. Compound indexnel: magasabb kardinalitasu mezo elore
4. Kis tablan (<1000 sor) → ne indexelj
5. Write-heavy tablan → minimalis index
6. EXPLAIN ANALYZE-zal ellenorizd hogy tenyleg hasznalja-e
7. Unused indexeket torolni kell (idx_scan = 0)
```

---

## Kapcsolodo

- [[database/sql-adatbazisok|SQL adatbazisok]] -- SQL alapok, JOIN, lekerdezesek
- [[database/supabase|Supabase]] -- PostgreSQL alapu, ahol az indexek tenylegesen futnak
- [[database/prisma|Prisma]] -- ORM, ahol a @@index definiciokat irod
- Next.js Data Cache es revalidacio -- cache szintu optimalizacio (index = query szintu, cache = request szintu)
- [[_moc/moc-database|MOC - Database]]

---
