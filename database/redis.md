---
tags:
  - adatbazis
  - cache
  - redis
datum: 2026-03-06
szint: "🧱 Brick"
kapcsolodo:
  - "[[database/supabase|Supabase]]"
  - "[[cloud/docker-alapok|Docker alapok]]"
  - "[[frontend/nextjs|Next.js]]"
  - "[[cloud/vercel|Vercel]]"
  - "[[cloud/railway|Railway]]"
  - "[[_moc/moc-database|MOC - Database]]"
---

## Mi ez?

A **Redis** (Remote Dictionary Server) egy in-memory adatbázis, amit elsősorban cache-kent, session store-kent, message broker-kent és real-time adatkezeloként használnak. Az adatokat RAM-ban tarolja, ezert **1000-10000x gyorsabb** mint a hagyomanyos disk-alapu adatbázisok.

Nem csak egy egyszerű key-value store -- gazdag adatstrukturakat tamogat (listak, halmazok, sorted set-ek, hash-ek, stream-ek), ezert szinte bármilyen real-time problémara megoldás.

> [!info] Forrás
> Ez a jegyzet a [ByteByteGo Redis video](https://www.youtube.com/watch?v=z_NbVtbgBJw) és a kapcsolodo ByteByteGo cikkek alapján készült.

## Miért ilyen gyors?

```mermaid
graph LR
    subgraph "Miert gyors a Redis?"
        A[RAM-alapu tarolas<br/>1000-10000x gyorsabb<br/>mint disk I/O] --> D[~100K req/sec<br/>throughput]
        B[Single-threaded<br/>event loop +<br/>IO multiplexing] --> D
        C[Optimalizalt<br/>adatstrukturak<br/>nincs szerializacio] --> D
    end
```

1. **In-memory storage** -- az összes adat RAM-ban van, nincs disk I/O bottleneck
2. **Single-threaded event loop + IO multiplexing** -- egyetlen szal kezeli az összes connection-t (epoll/kqueue), nincs context switching, nincs lock contention
3. **Optimalizált adatstrukturak** -- SDS (Simple Dynamic Strings), ziplist/listpack, skiplist, intset -- mind memoriara optimalizálva, nincs szerializacio/deszerializacio overhead

> [!tip] Miért single-threaded?
> Meglepo, de a single-threaded modell gyorsabb mint a multi-threaded, mert eliminal minden lock-ot, mutex-ot és context switch-et. A bottleneck nem a CPU, hanem a hálózat és a memoria -- Redis 6.0+ ota az I/O reszt már multi-threaded kezeli, de az execution marad single-threaded.

## Adatstrukturak

| Struktúra | Mire jó | Példa parancs | Use case |
|-----------|---------|---------------|----------|
| **String** | Egyszerű key-value | `SET user:1:name "User"` | Cache, counter, session token |
| **Hash** | Objektum-szeru tárolás | `HMSET user:bob name Bob location "Budapest"` | User profile, settings |
| **List** | Sorba rendezett elemek | `LPUSH`, `RPUSH`, `LRANGE` | Message queue, activity feed |
| **Set** | Egyedi elemek halmaza | `SADD`, `SMEMBERS`, `SINTER` | Tag-ek, egyedi visitor-ok, baratlista |
| **Sorted Set** | Pontszammal rendezett halmaz | `ZADD leaderboard 100 "player1"` | Leaderboard, timeline, priority queue |
| **Stream** | Append-only log | `XADD`, `XREAD`, `XGROUP` | Event stream, message queue (Kafka-lite) |
| **Bitmap** | Bit-szintű műveletek | `SETBIT`, `BITCOUNT` | Feature flag-ek, napi aktiv user tracking |
| **HyperLogLog** | Közelito kardinalitas | `PFADD`, `PFCOUNT` | Egyedi latogatok szamlalasa (nagyon keves memoria) |
| **Geospatial** | Foldrajzi koordinatak | `GEOADD`, `GEODIST`, `GEORADIUS` | Közeli helyek keresese, ride-hailing |

## Fo use case-ek

### 1. Caching (leggyakoribb)

Az alkalmazás és az adatbázis koze kerul:

```mermaid
graph LR
    APP[Alkalmazas] -->|1. GET key| REDIS[Redis Cache]
    REDIS -->|2a. Cache HIT| APP
    REDIS -.->|2b. Cache MISS| DB[(PostgreSQL)]
    DB -->|3. Adat| APP
    APP -->|4. SET key TTL| REDIS
```

- **Pareto-elv**: a lekérdezések ~80%-a az adatok ~20%-ara vonatkozik -- ezt érdemes cache-elni
- **Eviction policy-k**: LRU (Least Recently Used), LFU (Least Frequently Used), TTL-alapu
- Többszintu cache: app-szintű (in-process HashMap) → Redis (distributed) → DB

### 2. Session Store

Elosztott környezetben a user session-oket Redis-ben tartjuk, nem a szerveren:

1. User bejelentkezik → session ID generalodik, Redis-be mentodik
2. Client megkapja a session ID-t (cookie)
3. Következo request-nel a session ID alapján bármelyik szerver ki tudja olvasni az adatot
4. Nincs szükség sticky session-re a load balancer-nel

### 3. Rate Limiting

```
INCR api:user:123:minute
EXPIRE api:user:123:minute 60
```
Ha az érték > limit → 429 Too Many Requests. Redis atomi műveletei garantaljak a konzisztenciat.

### 4. Leaderboard

Sorted Set-tel nativan megoldhato:
```
ZADD game:leaderboard 1500 "player_a"
ZADD game:leaderboard 2300 "player_b"
ZREVRANGE game:leaderboard 0 9 WITHSCORES  # Top 10
```

### 5. Pub/Sub és Message Queue

- **Pub/Sub**: valós ideju üzenetkuldés subscriber-eknek (chat, notification)
- **Streams**: tartos message queue, consumer group-okkal (Kafka-lite, de egyszerűbb)
- **List**: egyszerű queue `LPUSH` + `BRPOP` komboval

### 6. Distributed Lock

```
SET lock:resource "owner" NX EX 30
```
`NX` = only if not exists, `EX` = expire. Elosztott környezetben ez a legegyszerubb lock mechanizmus (Redlock algoritmus a robosztusabb verzió).

## Perzisztencia

Redis alapvetően in-memory, de az adatok nem vesznek el ujrainditaskor:

| Mod | Hogyan működik | Trade-off |
|-----|---------------|-----------|
| **RDB** (Snapshot) | Idoszakos point-in-time snapshot a teljes adatbázisrol | Gyors recovery, de az utolso snapshot ota elveszhet adat |
| **AOF** (Append Only File) | Minden iras műveletet logol (mint egy commit log) | Biztonságosabb, de nagyobb fájl, lassabb recovery |
| **RDB + AOF** | Mindketto egyutt | Legjobb kombo production-ben |
| **Nincs** | Perzisztencia kikapcsolva | Tisztan cache use case, kis adatmennyiseg |

> [!warning] Redis ≠ primary database
> Redis-t nem szabad egyeduli adatbáziskent használni (bar vannak akik így csinálják). A legjobb minta: **[[database/supabase|Supabase]]** / PostgreSQL mint source of truth + Redis mint cache/session layer.

## High Availability

```mermaid
graph TD
    CLIENT[Client] --> SENTINEL[Redis Sentinel]
    SENTINEL --> LEADER[Leader / Primary]
    LEADER -->|replikacio| R1[Replica 1]
    LEADER -->|replikacio| R2[Replica 2]
    R1 --> CLIENT
    R2 --> CLIENT

    style LEADER fill:#e74c3c,color:white
    style R1 fill:#3498db,color:white
    style R2 fill:#3498db,color:white
```

- **Leader-Follower replikacio**: a leader kezeli az irasokat, a replica-k a read-eket
- **Redis Sentinel**: monitoring + automatikus failover -- ha a leader meghal, egy replica-t eloleptet
- **Redis Cluster**: horizontalis sharding -- az adatok elosztasa több node kozott (16384 hash slot)

## Mikor használd / Mikor NE

| Mikor IGEN | Mikor NE |
|-----------|----------|
| Cache layer a DB ele | Primary database (egyedul) |
| Session management | Nagy meretu blob tárolás (>512MB value) |
| Real-time leaderboard | Komplex relacios query-k (JOIN, GROUP BY) |
| Rate limiting | Ha az adat nem fer a RAM-ba |
| Pub/Sub, event streaming | Ha nem kell sub-millisecond latency |
| Distributed lock | ACID tranzakciok kellenek (bank) |

## Hosting opciok

| Szolgáltatas | Mikor jó |
|-------------|----------|
| **Redis Cloud** (redis.com) | Managed, Redis Ltd. hivatalos |
| **Upstash** | Serverless Redis, pay-per-request -- [[cloud/vercel|Vercel]] Edge-hez ideális |
| **AWS ElastiCache** | Enterprise, nagy volumen |
| **Railway** | Redis konténer egyszerűen -- [[cloud/railway|Railway]] |
| **Docker** | Saját gep / VPS -- `docker run redis:alpine` |

> [!tip] Serverless-hez Upstash
> Ha [[cloud/vercel|Vercel]]-en vagy [[frontend/nextjs|Next.js]]-sel dolgozol és serverless function-okbol akarsz Redis-t elerni, az **Upstash** a legjobb választas: HTTP-alapu API, nincs persistent connection probléma, pay-per-request arazas.

## Kapcsolodo

- [[database/supabase|Supabase]] -- PostgreSQL mint primary DB, Redis mint cache layer melle
- [[cloud/docker-alapok|Docker alapok]] -- Redis konténerben futtatasa lokálisan
- [[frontend/nextjs|Next.js]] -- server-side cache, session store Next.js API route-okbol
- [[cloud/vercel|Vercel]] -- Edge Function + Upstash Redis kombo
- [[cloud/railway|Railway]] -- managed Redis hosting
- [[_moc/moc-database|MOC - Database]]
