---
tags:
  - cloudflare
  - queue
  - async
datum: 2026-03-26
szint: "🧱 Scout"
kapcsolodo:
  - "[[cloud/cloudflare|Cloudflare]]"
  - "[[cloud/cloudflare-r2|Cloudflare R2]]"
  - "[[cloud/cloudflare-kv|Cloudflare KV]]"
  - "[[backend/hono|Hono]]"
---

# Cloudflare Queues

> [!tldr] Mi ez és mire jó?
> Message queue szolgáltatás a [[cloud/cloudflare|Cloudflare]] ökoszisztémában. Szétválasztja a Worker-eket: az egyik üzenetet küld, a másik aszinkron feldolgozza. Guaranteed at-least-once delivery, natív CF integráció, minimális config.

## Hogyan működik

1. **Producer Worker** - üzenetet küld a queue-ba (pl. "szinkronizáld ezt a rekordot")
2. **Cloudflare** - batch-eli az üzeneteket (max 100 db vagy 30 sec, amelyik előbb jön)
3. **Consumer Worker** - megkapja a batch-et és feldolgozza

Ez a minta tökéletes rate limit védelemre: ahelyett, hogy 500 API hívást egyszerre küldesz egy külső API-nak, a queue szépen adagolja őket.

## Mikor használd

| Helyzet | Queues jó választás? |
|---------|---------------------|
| API rate limit védelem (külső API-k) | Igen |
| Background job-ok (email, PDF generálás) | Igen |
| Megbízható delivery kell (ne vesszen el üzenet) | Igen |
| Real-time válasz kell a user-nek | Nem - szinkron Worker jobb |
| Komplex routing, dead letter queue | Részben - egyszerűbb, mint SQS |

## Összehasonlítás

| | Cloudflare Queues | AWS SQS | BullMQ / Redis |
|---|---|---|---|
| Komplexitás | Minimális, CF-natív | Enterprise szintű | Self-hosted, több config |
| Ár | $0.40/M művelet | $0.40/M + transfer | Redis hosting költség |
| Integráció | Workers-szel natív | Lambda trigger | Saját infra kell |
| Mikor válaszd | CF stack-ben dolgozol | AWS ökoszisztéma | Már van Redis, kell prioritás/delay |

## Konfiguráció

A `wrangler.toml`-ban producer és consumer binding-ot kell felvenni:

## Árazás

- **Free tier:** 1M művelet/hó
- **Paid:** $0.40/millió művelet (write + read egyaránt számít)
- Nincs egress díj a Worker-ek között

---

## AI-natív fejlesztés

A Cloudflare Queues setup és a producer/consumer Worker minta ismétlődő boilerplate - Claude Code gyorsan generálja a wrangler.toml binding-okat és a TypeScript handler kódot. Különösen hasznos rate limit védelmre külső API-k hívásánál.

> [!tip] Hogyan használd AI-val
> - *"Adj hozzá egy Cloudflare Queue-t a projekthez. A /api/sync endpoint legyen a producer, a consumer pedig batch-elje a külső API hívásokat max 10/sec rate-tel."*
> - *"Cloudflare Queue + R2: a producer feltöltési feladatokat küld, a consumer Worker feldolgozza és R2-be menti az eredményt"*

## Kapcsolódó

- [[cloud/cloudflare|Cloudflare]] - a Cloudflare ökoszisztéma része
- [[cloud/cloudflare-r2|Cloudflare R2]] - queue-ból triggerelt fájlműveletek
- [[cloud/cloudflare-kv|Cloudflare KV]] - egyszerű state tárolás a queue feldolgozás mellé
- [[backend/hono|Hono]] - a Worker framework, amiben a producer/consumer endpoint-okat írod
