---
tags:
  - github
  - ai
  - memory
datum: 2026-03-26
szint: "🧱 Scout"
url: https://github.com/thedotmack/claude-mem
kapcsolodo:
  - "[[toolbox/claude-code|Claude Code]]"
  - "[[toolbox/claude-agent-sdk|Claude Agent SDK]]"
  - "[[toolbox/mcp-model-context-protocol|MCP]]"
  - "[[_moc/moc-ai-tooling|MOC - AI Tooling]]"
---

# claude-mem

**Kategória:** `Claude Code plugin` / `persistent memory`
**URL:** https://github.com/thedotmack/claude-mem

---

## Mi ez és mire jó?

> [!tldr] Egy mondatban
> [[toolbox/claude-code|Claude Code]] plugin ami **session-ök között megőrzi a kontextust** - automatikusan rögzíti amit csinálsz, AI-val tömöríti, és a következő session-ben visszaadja a releváns kontextust.

A probléma amit megold: [[toolbox/claude-code|Claude Code]] session-ök között elvész a kontextus. A CLAUDE.md segít a projekt konvencióknál, de a **"mit csináltam tegnap, hol tartok, milyen döntéseket hoztam"** típusú tudás elvész.

A claude-mem ezt automatikusan megoldja:
1. **Capture** - minden tool használatot megfigyel (file read, edit, bash) és rögzít
2. **Compress** - AI-val tömöríti az observation-öket (nem nyers log, hanem értelmes összefoglaló)
3. **Inject** - a következő session-ben a releváns kontextust automatikusan betölti

---

## Mikor hasznos?

- **Többnapos fejlesztés** - ugyanazon a feature-ön dolgozol napokig, és nem akarsz mindent újra elmagyarázni
- **Debugging session-ök** - "tegnap kipróbáltam X-et, nem működött" típusú kontextus megőrzés
- **Csapatban** - ha több ember használ [[toolbox/claude-code|Claude Code]]-ot ugyanazon a projekten, mindenki látja a korábbi session-ök kontextusát
- **Komplex refactoring** - ahol a döntések láncolata fontos (miért választottam A-t B helyett)

### Mikor NE használd

- Rövid, egyszeri feladatoknál - ott a CLAUDE.md elegendő
- Ha a token költség kritikus - a kontextus injection extra token-öket fogyaszt
- Ha érzékeny kód/adat van a projektben - a `<private>` tag segít, de gondold át

---

## CLAUDE.md vs claude-mem

| | CLAUDE.md | claude-mem |
|---|---|---|
| **Tartalom** | Konvenciók, szabályok, gotcha-k | Session history, döntések, kontextus |
| **Ki írja** | Te (kézzel) | Automatikus capture |
| **Mikor frissül** | Manuálisan | Minden session-ben |
| **Mire jó** | "Hogyan dolgozz ezen a projekten" | "Mit csináltam eddig" |
| **Token költség** | Fix (fájl méret) | Változó (retrieval mélységtől függ) |

Nem helyettesítik egymást - **kiegészítik**. A CLAUDE.md a statikus projekt tudás, a claude-mem a dinamikus session kontextus.

---

## AI-natív fejlesztés

[[toolbox/mcp-model-context-protocol|MCP]] server-ként integrálódik - a `.claude/settings.json`-ban konfiguráld. Telepítés után automatikusan működik.

**4 MCP tool-t ad:**
- `search` - természetes nyelvű keresés a korábbi session-ökben
- `timeline` - időalapú visszanézés
- `get_observations` - specifikus observation lekérés ID alapján
- 3-layer retrieval - progresszív kontextus betöltés (token költség kijelzéssel)

> [!tip] Hogyan használd AI-val
> "Keress a claude-mem-ben, hogy milyen döntéseket hoztam a DB schema-ról az elmúlt héten." - természetes nyelvű keresés a korábbi session-ökben.

---

## Kapcsolódó

- [[toolbox/claude-code|Claude Code]] - az eszköz amihez a plugin készült
- [[toolbox/claude-agent-sdk|Claude Agent SDK]] - a claude-mem az Agent SDK-t is használja a háttérben
- [[github/napkin|napkin]] - alternatív megközelítés a projekt-szintű tudáskezelésre
