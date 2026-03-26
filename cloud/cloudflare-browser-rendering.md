---
tags:
  - cloudflare
  - api
  - scraping
datum: 2026-03-26
szint: "🧱 Brick"
kapcsolodo:
  - "[[cloud/cloudflare|Cloudflare]]"
  - "[[cloud/cloudflare-r2|Cloudflare R2]]"
  - "[[cloud/cloudflare-monorepo-mappastruktura|Cloudflare Monorepo Mappastruktúra]]"
---

# Cloudflare Browser Rendering

**Kategória:** `API` / `scraping` / `dev tool`
**URL:** https://developers.cloudflare.com/browser-rendering/
**Ár/Terv:** Workers Free (10 perc/nap) / Workers Paid ($5/hó alap + $0.09/óra)

---

## Mi ez és mire jó?

A **[[cloud/cloudflare|Cloudflare]] Browser Rendering** a Cloudflare headless Chrome szolgáltatása REST API-n keresztül. Lényegében egy felhőben futó böngészőt ad, amit HTTP kérésekkel vezérelsz - nem kell saját infrastruktúra, Puppeteer szerver vagy Docker konténer.

> [!tldr]
> **Headless Chrome as a Service** - REST API endpointok, amikkel weboldal tartalmat, linkeket, screenshotot, PDF-et kérsz le. Markdown és HTML formátumban, opcionális JS renderezéssel.

### Mikor jó választás?

- **SSR oldalak** scrapelése (ahol nincs anti-bot védelem)
- **Dokumentáció crawl** a `/crawl` endpointtal
- **Screenshot / PDF generálás** API-ból
- Ha már **Cloudflare ökoszisztémában** vagy (Workers, R2, D1)
- **Költségoptimalizálás** - $0.09/óra vs. más scraping szolgáltatások credit-jei

### Mikor NE használd?

- Anti-bot védelemmel ellátott oldalak (CAPTCHA, bot detection)
- Load-more gombos oldalak (nincs `actions` feature)
- Ha magas volumenű, megbízható scrapingre van szükséged proxy rotation-nel

---

## REST API Endpointok

| Endpoint | Metódus | Mit csinál | Válasz |
|----------|---------|-----------|--------|
| `/markdown` | POST | Weboldal -> Markdown | `{ success, result: "# ..." }` |
| `/content` | POST | Weboldal -> raw HTML | `{ success, result: "<html>..." }` |
| `/links` | POST | Összes link kinyerése | `{ success, result: ["url1", ...] }` |
| `/screenshot` | POST | Képernyőkép (PNG) | Binary |
| `/pdf` | POST | PDF renderelés | Binary |
| `/scrape` | POST | CSS selector-alapú HTML kinyerés | JSON |
| `/json` | POST | AI-alapú strukturált adat kinyerés | JSON schema |
| `/crawl` | POST | Async crawl job (link-követéssel) | Job ID -> polling |

### Alap URL

```
https://api.cloudflare.com/client/v4/accounts/{account_id}/browser-rendering/{endpoint}
```

---

## Setup - lépésről lépésre

### 1. Cloudflare account

Regisztráció: https://dash.cloudflare.com - ingyenes, Workers Free plan automatikusan aktív.

### 2. API Token létrehozás

1. **dash.cloudflare.com** -> Profile -> **API Tokens**
2. **Create Custom Token**
3. Permissions:
   - Account -> **Browser Rendering** -> Edit
   - Account -> **Workers AI** -> Edit (ha `/json` endpoint kell)
   - Account -> **Workers Scripts** -> Edit
   - Account -> **Account Settings** -> Read
4. **Create Token** -> másold ki azonnal (többet nem mutatja!)

### 3. Env vars

```bash
CLOUDFLARE_ACCOUNT_ID=abc123...  # Dashboard URL-ből: dash.cloudflare.com/{account_id}/...
CLOUDFLARE_API_TOKEN=your-token-here
```

### 4. Tesztelés

Minden endpoint ugyanazt a mintát követi - POST request az alap URL-re + endpoint név:

```bash
curl -X POST "https://api.cloudflare.com/client/v4/accounts/${CLOUDFLARE_ACCOUNT_ID}/browser-rendering/markdown" \
  -H "Authorization: Bearer ${CLOUDFLARE_API_TOKEN}" \
  -H "Content-Type: application/json" \
  -d '{"url": "https://example.com"}'
```

---

## Best Practices

### Fontos request opciók

| Opció | Mikor kell | Hatás |
|---|---|---|
| `"gotoOptions": { "waitUntil": "networkidle0" }` | SPA/CSR oldalak (JS renderel) | Megvárja amíg nincs network activity |
| `"rejectResourceTypes": ["image", "font", "media"]` | Költség/sebesség optimalizálás | Kiszűri a felesleges resource-okat |
| `"render": false` | SSR oldalak (nincs JS szükséges) | Egyszerű HTTP fetch, Chrome nélkül |

Ezeket a body JSON-ban adod meg az `url` mellé. Kombinálhatók:

```json
{
  "url": "https://spa-app.com",
  "gotoOptions": { "waitUntil": "networkidle0" },
  "rejectResourceTypes": ["image", "font", "media"]
}
```

> [!tip] Költségcsökkentés
> A `render: false` a beta alatt **ingyenes** (nem számít bele a böngészőidőbe). SSR oldalaknál érdemes kipróbálni.

### `/crawl` endpoint - async job

A crawl endpoint aszinkron: POST-ra kapsz egy job ID-t, amit aztán pollingolsz:

```bash
# 1. Job indítása
curl -X POST ".../crawl" -d '{"url": "https://docs.example.com", "limit": 50, "formats": ["markdown"]}'
# -> { "result": "job-id-here" }

# 2. Eredmény lekérdezés
curl -X GET ".../crawl/job-id-here"
# -> { "result": { "status": "completed", "records": [...] } }
```

---

## Limitek

### Workers Free

| Limit | Érték |
|-------|-------|
| Böngészőidő | 10 perc/nap |
| Rate limit | 6 req/perc (1/10 sec) |
| Crawl job/nap | 5 |
| Max oldalak/crawl | 100 |

### Workers Paid ($5/hó)

| Limit | Érték |
|-------|-------|
| Böngészőidő | 10 óra/hó ingyenes, utána $0.09/óra |
| Rate limit | 600 req/perc (10/sec) |
| Crawl job | Korlátlan |
| Max oldalak/crawl | 100,000 |

---

## Buktatók amiket kerülj el

- **Anti-bot védett oldalak nem működnek** - nincs proxy rotation, a Cloudflare IP range-et sok oldal felismeri
- **Nincs `actions` (click, type) support a REST API-ban** - load-more gombos oldalak nem kezelhetők, ehhez Workers Binding + Puppeteer kell
- **SPA oldalak alapértelmezetten üres HTML-t adnak** - mindig használj `gotoOptions: { waitUntil: "networkidle0" }` JS-heavy oldalaknál
- **Free plan rate limit nagyon alacsony** - 6 req/perc = 10 másodpercenként 1 kérés, delay kell a kérések közé
- **Az API token-t csak egyszer mutatja** - ha elveszted, újat kell generálni

---

## TypeScript client minta

```typescript
// Minden endpoint ugyanezzel a mintával hívható
const CF_BASE = `https://api.cloudflare.com/client/v4/accounts/${ACCOUNT_ID}/browser-rendering`;

async function cfFetch(endpoint: string, url: string, opts?: Record<string, unknown>) {
  const res = await fetch(`${CF_BASE}/${endpoint}`, {
    method: "POST",
    headers: {
      Authorization: `Bearer ${API_TOKEN}`,
      "Content-Type": "application/json",
    },
    body: JSON.stringify({ url, ...opts }),
  });
  const data = await res.json();
  return data.result;
}

// Használat:
// await cfFetch("markdown", url, { rejectResourceTypes: ["image", "font", "media"] })
// await cfFetch("links", url)
// await cfFetch("content", url, { gotoOptions: { waitUntil: "networkidle0" } })
```

---

## AI-natív fejlesztés

A Cloudflare BR REST API egyszerű, jól dokumentált - Claude Code-dal gyorsan építhetsz scraping pipeline-t ami a BR endpoint-okat hívja. Különösen hasznos, ha a scrape-elt markdown-t közvetlenül AI-nak adod feldolgozásra (pl. structured data extraction).

> [!tip] Hogyan használd AI-val
> - *"Írj TypeScript wrapper-t a Cloudflare Browser Rendering API-hoz: markdown, links, screenshot endpoint-ok, error handling-gel"*
> - *"Scrape pipeline: Cloudflare BR markdown endpoint -> AI extraction (structured JSON) -> DB insert"*

---

## Hasznos linkek

- Docs: https://developers.cloudflare.com/browser-rendering/
- API Reference: https://developers.cloudflare.com/api/resources/browser_rendering/
- Pricing: https://developers.cloudflare.com/browser-rendering/pricing/
- Limits: https://developers.cloudflare.com/browser-rendering/limits/
