---
tags:
  - adatbazis
  - orm
  - typescript
datum: 2026-03-06
szint: "🧱 Brick"
kapcsolodo:
  - "[[database/prisma|Prisma]]"
  - "[[database/supabase|Supabase]]"
  - "[[database/sql-adatbazisok|SQL adatbazisok]]"
  - "[[database/sql-index-szabalyok|SQL Index szabalyok]]"
  - "[[cloud/cloudflare|Cloudflare]]"
  - "[[cloud/docker-alapok|Docker alapok]]"
  - "[[cloud/docker-compose|Docker Compose]]"
  - "[[cloud/railway|Railway]]"
  - "[[frontend/nextjs|Next.js]]"
  - "[[backend/hono|Hono]]"
  - "[[backend/clerk|Clerk]]"
  - "[[_moc/moc-database|MOC - Database]]"
---

# Drizzle

**Kategoria:** `adatbazis` / `ORM` / `dev tool`
**URL:** https://orm.drizzle.team

---

## Mi ez es mire jo?

A **Drizzle ORM** egy TypeScript-first ORM (Object-Relational Mapping), ami SQL-t ir helyetted, de ugy, hogy te kontrollalod mi tortenik. A fo filozofiaja: _"Ha ismered az SQL-t, ismered a Drizzle-t."_

**Milyen problemat old meg:**

Adatbazissal kell kommunikalnod az appodbol. Irhatnal raw SQL-t, de az nem tipusbiztos, nehez karbantartani, es nincs autocomplete. A Drizzle ad neked TypeScript tipusokat, migration-oket, es egy query builder-t, ami szinte 1:1 lekezpezi az SQL-t -- de nem rejti el eloled, mint a Prisma.

# Drizzle vs [[database/prisma|Prisma]] osszehasonlitas

|Szempont|Drizzle|Prisma|
|---|---|---|
|**Megkozelites**|SQL-kozelibb, te kontrollalsz|Magas szintu absztrakcio|
|**Sema definicio**|TypeScript fajlok|Sajat `.prisma` nyelv|
|**Bundle size**|Minimalis (~50kb)|Nagy (~2MB engine)|
|**Edge/serverless**|Nativan fut|Problemas (engine kell)|
|**Relaciok**|SQL join-ok VAGY relational API|Beepitett relation szintaxis|
|**Raw SQL**|Termeszetes, barmikor|Lehetseges de nem idiomatikus|
|**Tanulasi gorbe**|Ha tudsz SQL-t, azonnal megy|Prisma-specifikus nyelvet tanulsz|
|**Migration**|`drizzle-kit` (push/generate)|`prisma migrate`|
|**Docker/serverless**|Tokeletes (nincs kulon engine)|Engine binary kell a containerbe|


---

## Architektura

```mermaid
graph TD
    A[Server Action / API Route] --> B[queries/ reteg]
    B --> C[Drizzle ORM]
    C --> D[schema.ts\nTypeScript tipusok]
    C --> E[Generalt SQL]
    E --> F[(PostgreSQL)]
    G[drizzle-kit] --> H[Migration SQL]
    H --> F
```

---
## Setup -- lepesrol lepesre

### 1. Telepites (Postgres pelda)

```bash
# Core + Postgres driver + migration tool
npm install drizzle-orm postgres
npm install -D drizzle-kit
```

A `postgres` csomag a driver (postgres.js) -- alternativa: `pg`, `@neondatabase/serverless`, `@vercel/postgres`.

### 2. Alap konfiguracio

`drizzle.config.ts` (projekt gyoker):

```ts
import { defineConfig } from 'drizzle-kit'

export default defineConfig({
  schema: './src/db/schema.ts',
  out: './drizzle',               // migration fajlok ide kerulnek
  dialect: 'postgresql',
  dbCredentials: {
    url: process.env.DATABASE_URL!
  }
})
```

`.env`:

```env
DATABASE_URL=postgresql://user:password@localhost:5432/mydb
```

### 3. Projekt beallitas

**Sema definialas** -- `src/db/schema.ts`:

```ts
import { pgTable, text, serial, timestamp, integer, boolean } from 'drizzle-orm/pg-core'

export const users = pgTable('users', {
  id: serial('id').primaryKey(),
  clerkId: text('clerk_id').unique().notNull(),
  email: text('email').unique().notNull(),
  name: text('name'),
  role: text('role', { enum: ['admin', 'member'] }).default('member'),
  stripeCustomerId: text('stripe_customer_id'),
  createdAt: timestamp('created_at').defaultNow()
})

export const posts = pgTable('posts', {
  id: serial('id').primaryKey(),
  title: text('title').notNull(),
  content: text('content'),
  published: boolean('published').default(false),
  authorId: integer('author_id').references(() => users.id),
  createdAt: timestamp('created_at').defaultNow()
})
```

**DB kapcsolat** -- `src/db/index.ts`:

```ts
import { drizzle } from 'drizzle-orm/postgres-js'
import postgres from 'postgres'
import * as schema from './schema'

const client = postgres(process.env.DATABASE_URL!)

export const db = drizzle(client, { schema })
```

**Migration futtatas:**

```bash
# Sema → SQL migration generalas
npx drizzle-kit generate

# Pushol egyenesen a DB-be (dev-ben gyors, migration nelkul)
npx drizzle-kit push

# Migration alkalmazas (production)
npx drizzle-kit migrate
```

### 4. Osszekotes mas eszkozokkel

**Clerk-kel** (webhook → user sync):

```ts
// app/api/webhooks/clerk/route.ts
import { db } from '@/db'
import { users } from '@/db/schema'

export async function POST(req: Request) {
  const event = await verifyClerkWebhook(req) // svix validacio

  if (event.type === 'user.created') {
    await db.insert(users).values({
      clerkId: event.data.id,
      email: event.data.email_addresses[0]?.email_address,
      name: `${event.data.first_name} ${event.data.last_name}`
    })
  }
}
```

**Next.js Server Actions-szel:**

```ts
'use server'
import { db } from '@/db'
import { posts } from '@/db/schema'
import { auth } from '@clerk/nextjs/server'
import { eq } from 'drizzle-orm'

export async function createPost(title: string, content: string) {
  const { userId } = await auth()
  if (!userId) throw new Error('Unauthorized')

  const user = await db.query.users.findFirst({
    where: eq(users.clerkId, userId)
  })

  const [post] = await db.insert(posts).values({
    title,
    content,
    authorId: user!.id
  }).returning()

  return post
}
```

**[[cloud/docker-alapok|Docker]]-ben:**

```dockerfile
# Nincs extra engine binary mint Prismanal -- csak a node_modules kell
FROM node:20-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci --omit=dev
COPY . .
RUN npm run build
CMD ["node", "dist/index.js"]
```

```yaml
# docker-compose.yml
services:
  app:
    build: .
    environment:
      DATABASE_URL: postgresql://user:pass@db:5432/mydb
    depends_on:
      - db
  db:
    image: postgres:16-alpine
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass
      POSTGRES_DB: mydb
    volumes:
      - pgdata:/var/lib/postgresql/data

volumes:
  pgdata:
```


---

## Best Practices

### Architektura / Struktura

Tartsd a DB reteget elkulonitve -- ne szord szet a query-ket a komponensekbe:

```
src/
├── db/
│   ├── index.ts          # kapcsolat (export const db = drizzle(...))
│   ├── schema/
│   │   ├── users.ts      # users tabla
│   │   ├── posts.ts      # posts tabla
│   │   └── index.ts      # re-export minden schema
│   └── queries/
│       ├── users.ts      # getUserByClerkId(), createUser(), stb.
│       └── posts.ts      # getPostsByAuthor(), stb.
```

A `queries/` reteg azert fontos, mert a server action-okban es API route-okban nem kell DB logikat ismetelned:

```ts
// db/queries/users.ts
import { db } from '@/db'
import { users } from '@/db/schema'
import { eq } from 'drizzle-orm'

export async function getUserByClerkId(clerkId: string) {
  return db.query.users.findFirst({
    where: eq(users.clerkId, clerkId)
  })
}

export async function createUser(data: typeof users.$inferInsert) {
  const [user] = await db.insert(users).values(data).returning()
  return user
}

// Barhol a kodban:
import { getUserByClerkId } from '@/db/queries/users'
const user = await getUserByClerkId(clerkId)
```

**Relaciok definialasa** -- ha hasznalni akarod a `db.query` relational API-t:

```ts
// db/schema/relations.ts
import { relations } from 'drizzle-orm'
import { users, posts } from './index'

export const usersRelations = relations(users, ({ many }) => ({
  posts: many(posts)
}))

export const postsRelations = relations(posts, ({ one }) => ({
  author: one(users, {
    fields: [posts.authorId],
    references: [users.id]
  })
}))

// Ezutan mukodik:
const userWithPosts = await db.query.users.findFirst({
  where: eq(users.id, 1),
  with: { posts: true }  // automatikus join
})
```

### Biztonsag

> [!danger] SQL injection -- soha ne fuzz ossze stringeket
> Ha user inputot kozvetlenul beleirsz a query-be, az adatbazisod feltörheto. Mindig a Drizzle query buildert vagy a `sql` template tag-et hasznald.

```ts
// ❌ SOHA -- SQL injection
db.execute(`SELECT * FROM users WHERE email = '${email}'`)

// ✅ Drizzle query builder -- automatikusan parameterezett
db.select().from(users).where(eq(users.email, email))

// ✅ Ha muszaj raw SQL, hasznald a sql template tag-et
import { sql } from 'drizzle-orm'
db.execute(sql`SELECT * FROM users WHERE email = ${email}`)
//                              ez parameterezett ^^^^^^^^
```

**DB credentials:**

- `.env`-ben tartsd, soha ne commitold
- Docker-ben env variable-kent add at, ne bake-eld az image-be
- Production-ben hasznalj connection pooler-t (pl. PgBouncer, Supabase pooler, Neon pooler)

**Jogosultsagok** -- a DB user-nek csak annyi joga legyen, amennyire az appnak szuksege van. Ne `superuser`-rel csatlakozz production-ben.

### Teljesitmeny

**Csak azt kerd le amit kell:**

```ts
// ❌ Minden oszlop (SELECT *)
const allUsers = await db.select().from(users)

// ✅ Csak ami kell
const emails = await db.select({
  email: users.email,
  name: users.name
}).from(users)
```

**Indexek -- a sema fajlban definialhatod:**

```ts
import { pgTable, text, serial, index } from 'drizzle-orm/pg-core'

export const users = pgTable('users', {
  id: serial('id').primaryKey(),
  clerkId: text('clerk_id').unique().notNull(),
  email: text('email').unique().notNull(),
}, (table) => [
  index('clerk_id_idx').on(table.clerkId),  // gyakran keresel clerkId-ra
])
```

**Batch insert:**

```ts
// ❌ N db hivas
for (const user of userList) {
  await db.insert(users).values(user)
}

// ✅ Egy db hivas
await db.insert(users).values(userList)
```

**Prepared statements** -- ha ugyanazt a query-t sokszor futtatod:

```ts
const getUserStmt = db
  .select()
  .from(users)
  .where(eq(users.clerkId, sql.placeholder('clerkId')))
  .prepare('get_user_by_clerk_id')

// Tobbszor hivhato, a DB cache-eli a query plan-t
const user = await getUserStmt.execute({ clerkId: 'user_123' })
```

**Connection pooling:**

```ts
// Dev-ben rendben
const client = postgres(process.env.DATABASE_URL!)

// Production-ben limit + pool
const client = postgres(process.env.DATABASE_URL!, {
  max: 10,           // max connections
  idle_timeout: 20,  // sec
})
```

### Koltsegoptimalizalas

- Drizzle maga **ingyenes es open source** -- nincs licence koltseg
- A koltseg a **DB hosting** -- Neon, Supabase, Railway, vagy sajat Postgres Docker-ben
- **Neon free tier:** 0.5 GB storage, auto-sleep -- tokeletes dev/kis projektekhez
- **Supabase free tier:** 500 MB, 2 projektig
- Ha Docker-ben sajat Postgres-t futtatsz, a koltseg = a szerver koltsege, semmi extra
- **Connection pooling** csokkenti a DB load-ot → kisebb instance is eleg


---

## Buktatok es hibak amiket elkerulj


---

## Hasznos parancsok / kodreszletek

```
```

---

## Hasznos linkek

- **Docs:** [orm.drizzle.team](https://orm.drizzle.team)
- **Drizzle Kit (migration tool) docs:** [orm.drizzle.team/kit-docs](https://orm.drizzle.team/kit-docs/overview)
- **Drizzle Studio (vizualis DB bongeszo):** `npx drizzle-kit studio` -- bongeszöben megnyitja a DB-t
- **GitHub:** [github.com/drizzle-team/drizzle-orm](https://github.com/drizzle-team/drizzle-orm)
- **Discord:** [driz.link/discord](https://driz.link/discord)
- **X/Twitter:** [@DrizzleORM](https://x.com/DrizzleORM)

---

## Kapcsolodo anyagok
- Next.js Data Cache es revalidacio -- Drizzle query eredmenyek cache-elese Next.js-ben
- [[database/prisma|Prisma]]
- [[database/sql-adatbazisok|SQL adatbazisok]]
- [[database/sql-index-szabalyok|SQL Index szabalyok]]
- [[database/supabase|Supabase]]
- [[cloud/cloudflare|Cloudflare]] -- D1 (SQLite) driver Drizzle-lel
- [[backend/hono|Hono]] -- edge API framework, Drizzle-lel hasznalhato
- [[cloud/railway|Railway]]
- [[backend/clerk|Clerk]]
- [[frontend/nextjs|Next.js]]
- [[cloud/docker-alapok|Docker alapok]]
- [[cloud/docker-compose|Docker Compose]]
- Env valtozok Next.js-ben
- [[_moc/moc-database|MOC - Database]]
