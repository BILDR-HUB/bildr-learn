---
tags:
  - cloudflare
  - cache
  - edge
datum: 2026-03-26
szint: "🧱 Scout"
kapcsolodo:
  - "[[cloud/cloudflare|Cloudflare]]"
  - "[[cloud/cloudflare-r2|Cloudflare R2]]"
  - "[[cloud/cloudflare-queues|Cloudflare Queues]]"
  - "[[backend/hono|Hono]]"
---

# Cloudflare KV

> [!tldr] Mi ez és mire jó?
> Globális key-value store a [[cloud/cloudflare|Cloudflare]] edge-en. Eventually consistent, olvasásra optimalizált. Tökéletes config-ok, feature flag-ek, cache és session adatok tárolására - de nem való real-time state-re.

## Hogyan működik

- Íráskor az adat a központi store-ba kerül, majd **másodpercek alatt** propagál 300+ edge lokációra
- Olvasás mindig a legközelebbi edge-ről jön -> **ultra gyors** (< 10ms)
- Eventually consistent: írás után rövid ideig a régi értéket kaphatod más régiókból

## Cloudflare tárolók összehasonlítása

| | KV | D1 | R2 | Durable Objects |
|---|---|---|---|---|
| Típus | Key-value | SQLite (relációs) | Object/file storage | Stateful singleton |
| Konzisztencia | Eventually consistent | Strong | Strong | Strong |
| Optimalizált | Olvasás-heavy | Lekérdezések | Nagy fájlok | Real-time state |
| Mikor használd | Config, cache, session | User adatok, CRUD | Képek, backup, fájlok | WebSocket, counter, lock |

## KV vs külső alternatívák

| | Cloudflare KV | Redis | DynamoDB |
|---|---|---|---|
| Latency | < 10ms (edge) | < 1ms (same region) | ~10ms (single region) |
| Konzisztencia | Eventually | Strong | Configurable |
| Hosting | Managed, zero config | Self-hosted vagy managed | AWS managed |
| CF integráció | Natív binding | Fetch kell | Fetch kell |
| Mikor válaszd | CF stack, read-heavy | Real-time, pub/sub kell | AWS ökoszisztéma |

## Tipikus use case-ek

| Use case | Miért KV? |
|----------|-----------|
| Feature flags | Ritkán írod, gyakran olvasod - tökéletes fit |
| Session / auth token | Edge-en gyors lookup, TTL-lel lejár |
| API response cache | Drága API hívás eredményét tárolod |
| Rate limit counter | Egyszerű esetben OK, de strict limit-hez Durable Objects jobb |
| Config / settings | Alkalmazás beállítások, ritkán változnak |

## Mikor NE használd

- **Real-time state** (chat, cursor pozíció) -> Durable Objects
- **Relációs adatok** (user-ek, rendelések) -> D1
- **Nagy fájlok** (képek, PDF) -> R2
- **Strict consistency kell** (pénzügyi tranzakciók) -> D1 vagy Durable Objects

## Árazás

- **Free:** 100K olvasás/nap, 1K írás/nap, 1 GB tárhely
- **Paid:** $0.50/M olvasás, $5.00/M írás

---

## AI-natív fejlesztés

A KV namespace setup és a Worker binding konfiguráció jól generálható AI-val. Claude Code ismeri a wrangler.toml KV binding szintaxist és a Worker-ből való KV használat mintáit.

> [!tip] Hogyan használd AI-val
> - *"Adj hozzá KV namespace-t a Worker-hez. A `/api/config` endpoint olvassa ki belőle a feature flag-eket, a `/api/admin/config` pedig írja. TTL legyen 3600 sec."*
> - *"Cloudflare KV cache réteg: drága külső API hívás eredményét tárold KV-ben 1 órás TTL-lel, és a Worker először a cache-ből próbálja kiszolgálni"*

## Kapcsolódó

- [[cloud/cloudflare|Cloudflare]] - a Cloudflare ökoszisztéma része
- [[cloud/cloudflare-r2|Cloudflare R2]] - fájl/object tárolás (KV helyett nagy adatokra)
- [[cloud/cloudflare-queues|Cloudflare Queues]] - aszinkron feldolgozás KV-vel kombinálva
- [[backend/hono|Hono]] - Worker framework, amiben a KV binding-okat használod
