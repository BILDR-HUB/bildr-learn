---
tags:
  - ai
  - browser-automation
  - eszkoz
datum: 2026-03-30
szint: "🧱 Brick"
kapcsolodo:
  - "[[toolbox/claude-code|Claude Code]]"
  - "[[toolbox/playwright|Playwright]]"
  - "[[toolbox/mcp-model-context-protocol|MCP]]"
---

# dev-browser

**Kategória:** `AI agent browser automation`
**URL:** https://github.com/SawyerHood/dev-browser
**Stars:** ~5.1K | **Nyelv:** TypeScript/Rust (QuickJS WASM)

---

## Mi ez és mire jó?

> [!tldr] Egy mondatban
> Böngésző automatizálási eszköz AI agenseknek — sandboxolt JavaScript scripteket futtat QuickJS WASM-ban, teljes [[toolbox/playwright|Playwright]] API-val, persistent page state-tel.

Az AI agent "szemét és kezét" adja webes feladatokhoz, biztonságosan. A két fő előny a meglévő megoldásokkal szemben:

**Sandbox** — QuickJS WASM-ban futnak a scriptek, nincs host fájlrendszer/hálózat hozzáférés. Az agent bármilyen Playwright scriptet írhat anélkül, hogy a gépedhez hozzáférne.

**Persistent pages** — egyszer navigál, többször interaktál. A page state megmarad hívások között, ami drastikusan csökkenti a token használatot.

| | Dev Browser | Playwright MCP | Chrome Extension |
|---|---|---|---|
| **Sandbox** | QuickJS WASM (izolált) | Node.js (host access) | Böngésző context |
| **Persistent pages** | Igen | Nem (minden lépés új) | Igen |
| **Auto-connect** | Igen (futó Chrome-hoz) | Nem | N/A |
| **Benchmark** | 3m 53s, $0.88, 100% | lassabb, drágább | — |

---

## Mikor hasznos?

- **Web scraping** AI agent-tel — az agent maga navigál és gyűjt adatot
- **E2E testing** — az agent ír és futtat teszteket iteratívan
- **Form kitöltés, admin panel automatizálás** — komplex webes interakció sandboxolva
- **Visual debugging** — screenshot-ok alapján az agent "látja" az oldalt

### Mikor NE használd

- Ha a [[toolbox/playwright|Playwright]] MCP elég (egyszerű, egyirányú automatizálás)
- Ha nem kell sandbox (saját gépen, trusted scriptek)
- Ha a target oldal nem Chrome-kompatibilis

---

## Kapcsolódó

- [[toolbox/claude-code|Claude Code]] — az elsődleges target agent
- [[toolbox/playwright|Playwright]] — az API amit a Dev Browser wrappel
- [[toolbox/mcp-model-context-protocol|MCP]] — alternatív böngésző automatizálás Playwright MCP-vel
