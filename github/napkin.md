---
tags:
  - github
  - ai
  - memory
datum: 2026-03-26
szint: "🧱 Scout"
url: https://github.com/Michaelliv/napkin
kapcsolodo:
  - "[[github/claude-mem|claude-mem]]"
  - "[[toolbox/claude-code|Claude Code]]"
  - "[[toolbox/mcp-model-context-protocol|MCP]]"
  - "[[_moc/moc-ai-tooling|MOC - AI Tooling]]"
---

# napkin

**Kategória:** `Knowledge system` / `AI memory`
**URL:** https://github.com/Michaelliv/napkin

---

## Mi ez és mire jó?

> [!tldr] Egy mondatban
> Lokális, fájl-alapú tudásrendszer AI agent-eknek - **progressive disclosure**-rel adagolja a kontextust ahelyett hogy az egész vault-ot beöntené a context window-ba.

A napkin egy CLI tool ami `.napkin/` mappát hoz létre a projektedben. Ez lényegében egy mini Obsidian-kompatibilis vault, de **agent-first** szemlélettel tervezve.

A kulcs különbség a sima vault-tól: **progressive disclosure** - 4 szinten adagolja az információt:

| Szint | Parancs | Token | Mit kap az agent |
|-------|---------|-------|-----------------|
| 0 | `NAPKIN.md` | ~200 | Projekt kontextus |
| 1 | `napkin overview` | ~1-2k | Vault térkép + TF-IDF kulcsszavak |
| 2 | `napkin search <query>` | ~2-5k | Rangsorolt találatok snippet-ekkel |
| 3 | `napkin read <file>` | ~5-20k | Teljes fájl tartalom |

Ez megoldja a "mindenféle context dumpolás" problémát - az agent csak annyit lát amennyire szüksége van.

---

## Mikor hasznos?

- Ha egy **projekt-szintű** tudásbázist akarsz ami az agent-ek számára optimalizált
- Ha a CLAUDE.md + auto-memory rendszer nem elég (túl sok döntés, architektúra dokumentáció)
- Ha **több agent** dolgozik egy projekten és közös tudásbázisra van szükségük
- Ha Obsidian-kompatibilis vault-ot akarsz **a projekt mappáján belül** (nem a globális vault-ban)

### Mikor NE használd

- Ha a globális vault + session memory rendszer elegendő (a legtöbb esetben az)
- Ha nem agent-first a cél - sima jegyzetelésre az Obsidian jobb
- Ha nem akarod a `.napkin/` mappát a repóban tartani

---

## Memory stack kontextus

A napkin kiegészíti (nem helyettesíti) a meglévő [[toolbox/claude-code|Claude Code]] memory stacket:

| Réteg | Eszköz | Mikor |
|-------|--------|-------|
| Globális instrukciók | `~/.claude/CLAUDE.md` | Mindig |
| Projekt instrukciók | projekt CLAUDE.md | Mindig |
| Auto-memory | `~/.claude/projects/memory/` | Mindig |
| **napkin vault** | `.napkin/` a projektben | Progressive disclosure-rel |

**Template-ek** vannak különböző projekt típusokra:

| Template | Mappák | Mikor |
|----------|--------|-------|
| `coding` | decisions/, architecture/, guides/, changelog/ | Szoftverfejlesztés |
| `company` | people/, projects/, runbooks/, infrastructure/ | Cég-szintű tudásbázis |
| `product` | features/, roadmap/, research/, specs/ | Termékfejlesztés |
| `research` | papers/, concepts/, questions/, experiments/ | Kutatás |

---

## AI-natív fejlesztés

A napkin kifejezetten agent-first szemlélettel készült. A progressive disclosure biztosítja, hogy az AI agent ne pazarolja a context window-t felesleges információval.

> [!tip] Hogyan használd AI-val
> "Ez a projekt napkin vault-ot használ a `.napkin/` mappában. Használd a `napkin overview`-t a struktúra áttekintéséhez, és `napkin search`-öt specifikus témák kereséséhez ahelyett hogy mindent beolvasnál."

---

## Alternatívák

- **[[github/claude-mem|claude-mem]]** - hasonló cél (session memory), de hook-alapú automatikus capture, nem kézi vault
- **CLAUDE.md + auto-memory** - beépített [[toolbox/claude-code|Claude Code]] megoldás, egyszerűbb de kevésbé strukturált
- **Obsidian vault + [[toolbox/mcp-model-context-protocol|MCP]]** - globális tudásbázis, nem projekt-szintű

---

## Kapcsolódó

- [[github/claude-mem|claude-mem]] - másik megközelítés a Claude Code session memory-ra
- [[toolbox/claude-code|Claude Code]] - az agent aminek ez a memory rétege
