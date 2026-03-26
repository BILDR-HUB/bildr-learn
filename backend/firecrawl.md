---
tags: [backend, scraping, api]
datum: 2026-03-26
szint: "🧱 Brick"
kapcsolodo:
  - "[[backend/cloudflare-vs-firecrawl|Cloudflare vs Firecrawl]]"
  - "[[backend/headless-chrome|Headless Chrome]]"
  - "[[cloud/cloudflare|Cloudflare]]"
  - "[[toolbox/claude-code-projekt-setup|Claude Code]]"
---

# Firecrawl

**Kategória:** `scraping` / `API`
**URL:** https://www.firecrawl.dev
**Ár/Terv:** Free (500 credit) - Hobby ($16/hó) - Standard ($83/hó) - Growth ($333/hó)

---

## Mi ez és mire jó?

A **Firecrawl** egy **web scraping API szolgáltatás** - URL-t adsz neki, és visszakapod az oldal tartalmát tiszta markdown, HTML, linkek vagy screenshot formában. A háttérben headless Chrome-ot futtat proxy rotation-nel és anti-bot bypass-szal.

> [!tldr] Egy mondatban
> A Firecrawl = "web - clean text" API. Nem kell Puppeteer/Playwright-ot üzemeltetned, proxy-kat konfigurálnod, CAPTCHA-kat kezelned - egy API hívás és megvan az oldal tartalma.

### Miért jobb mint saját scraper?

| Te csinálod | Firecrawl |
|-------------|-----------|
| Puppeteer/Playwright telepítés + karbantartás | Egy API hívás |
| Proxy rotation beállítás (Bright Data, IPRoyal) | Beépítve |
| Anti-bot bypass (CAPTCHA, bot detection) | Beépítve |
| JavaScript renderezés kezelése | Automatikus |
| Retry logika, rate limiting | Beépítve |
| Markdown konverzió (readability) | Beépítve |

**Mikor NE használd:**
- Egyszerű SSR oldalak anti-bot nélkül - [[cloud/cloudflare|Cloudflare]] Browser Rendering olcsóbb
- Ha teljes kontrollt akarsz a böngésző felett - Playwright/Puppeteer
- Ha a költség kritikus és sok oldalt scrape-elsz - self-hosted Firecrawl vagy Cloudflare BR

---

## Fő képességek

| Endpoint | Mit csinál | Credit |
|----------|-----------|--------|
| **scrape** | Egy URL - markdown/HTML/screenshot | ~1/oldal |
| **batchScrape** | Több URL egyszerre, hatékonyan | ~1/oldal |
| **crawl** | Egy URL + link-követés (site-wide) | ~1/oldal |
| **map** | Site térkép - URL-ek listája (gyors) | Kevés |
| **extract** | AI-alapú strukturált adat kinyerés | Több |

### Interaktív oldalak - `actions`

A Firecrawl képes JavaScript interakciókat futtatni scrape közben (click, scroll, wait) - ez különösen hasznos **load-more gombos** vagy **infinite scroll** oldalaknál. Részletesen lásd: [[backend/cloudflare-vs-firecrawl|Cloudflare vs Firecrawl]].

---

## Setup

### API kulcs

1. https://www.firecrawl.dev - regisztráció
2. Dashboard - API Keys - másolás
3. Env változóba: `FIRECRAWL_API_KEY=fc-xxxxxxxx`

### Claude Code integráció

A Firecrawl [[toolbox/claude-code-projekt-setup|Claude Code]] skill-ként is elérhető - bármilyen webes tartalmat lekérhetsz a session-ből. A `firecrawl` skill automatikusan lecseréli a beépített `WebFetch` és `WebSearch` tool-okat.

### Self-hosted opció

A Firecrawl open source - saját szerveren is futtatható Docker-rel:

> [!warning] Self-hosted proxy
> A self-hosted Firecrawl **NEM tartalmaz proxy rotation-t** - az csak a Cloud verzióban van. Self-hosted esetben külön proxy szolgáltató kell, vagy etikus rate limiting proxy nélkül.

---

## Árazás és credit rendszer

| Terv | Ár | Credit/hó | Rate limit |
|------|----|-----------|------------|
| **Free** | $0 | 500 | Alacsony |
| **Hobby** | $16/hó | 3,000 | Közepes |
| **Standard** | $83/hó | 100,000 | Magas |
| **Growth** | $333/hó | 500,000 | Legmagasabb |

- **1 credit = 1 oldal** (markdown formátum)
- `batchScrape` hatékony - nem számít külön-külön hívásnak
- `actions` (click, scroll) nem költenek extra creditet

---

## Buktatók és hibák amiket elkerülj

- **Credit pazarlás** - ha nincs szükséged a teljes oldalra, használd a `onlyMainContent: true` opciót (kiszűri a navigációt, footert)
- **Rate limit** - a Free tier nagyon korlátozott. Fejlesztéshez Hobby tier minimum
- **Timeout** - komplex JS-es oldalak renderelése időbe telik. Állítsd be a `waitFor` paramétert ha az oldal lassan töltődik
- **Self-hosted =/= Cloud** - a proxy rotation, anti-bot bypass csak a Cloud verzióban van. Self-hosted = te kezeled az IP blokkolást

---

## Hasznos linkek

- **Docs:** https://docs.firecrawl.dev
- **Dashboard:** https://firecrawl.dev/app
- **GitHub:** https://github.com/mendableai/firecrawl
- **SDK (JS):** `@mendable/firecrawl-js`
- **SDK (Python):** `firecrawl-py`

---

## AI-natív fejlesztés

A Firecrawl SDK és a Claude Code firecrawl skill-je együtt erős kombó - Claude Code-ból közvetlenül scrape-elhetsz weboldalt és az eredményt azonnal felhasználhatod kódgeneráláshoz, adatelemzéshez vagy dokumentáció íráshoz.

> [!tip] Hogyan használd AI-val
> - *"Scrape-eld le ezt az URL-t Firecrawl-lal markdown-ban és készíts belőle TypeScript típusokat"*
> - *"Add meg az oldal jellegét: SSR vagy SPA, van-e anti-bot védelem, kell-e interakció (click, scroll) - ez segít eldönteni hogy Firecrawl kell-e vagy olcsóbb alternatíva is elég"*
