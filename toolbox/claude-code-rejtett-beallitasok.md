---
tags:
  - ai
  - claude-code
  - konfig
datum: 2026-03-26
szint: "🏗️ Builder"
kapcsolodo:
  - "[[toolbox/claude-code-projekt-setup|Claude Code]]"
  - "[[toolbox/claude-mappa-anatomiaja|A .claude mappa anatómiája]]"
  - "[[toolbox/claude-code-agent-teams|Claude Code Agent Teams]]"
  - "[[github/claude-code-best-practice|Claude Code best practice]]"
---

# Claude Code rejtett beállítások

> [!tldr] Miért releváns
> A [[toolbox/claude-code-projekt-setup|Claude Code]] `settings.json`-jében és CLI flag-jeiben rengeteg konfig van ami alapból nem optimális - a legtöbb az eredeti 200K context window-hoz volt beállítva. Az 1M token context-tel ezek újrahangolhatók.

A beállítások helye: `~/.claude/settings.json` (globális) vagy `projekt/.claude/settings.json` (projekt-szintű). Ahogy [[toolbox/claude-mappa-anatomiaja|a .claude mappa anatómiájában]] láttuk, a kettő közül a globális az ami mindig érvényes.

---

## Context window optimalizálás

Az alábbi beállítások mind a régi 200K window-ra voltak szabva. Az 1M context-tel érdemes felnyitni.

| Beállítás | Alapértelmezett | Javasolt | Mit csinál |
|---|---|---|---|
| `terminalOutputLimit` | 30 000 karakter | 150 000 | Bash output limit - test suite, build log, migration output csonkulás megszűnik |
| `readTokenLimit` | 25 000 token | 100 000 | Fájl olvasási limit per Read hívás - nagyobb fájlok egyben beolvashatók |
| `autoCompactPercentOverride` | 95% | 75% | Mikor triggerelődik az auto-compact - 70% felett már romlik az output minőség |
| `cleanupPeriodDays` | 30 nap | 365 | Session megőrzés - `/insights` és `--resume` régebbi session-ökkel is működik |

> [!warning] 2000 soros Read limit
> A Read tool max **2000 sort** olvas egyszerre, és nem is tudja hogy lemaradt a többi. Ez nem konfigurálható. Workaround: CLAUDE.md-be instrukció, hogy nagy fájloknál ellenőrizze a sorszámot és `offset` + `limit` paraméterekkel olvassa végig. Hook-kal is kikényszeríthető.

---

## Privacy és attribution

| Beállítás | Hatás |
|---|---|
| `disableTelemetry: true` | Statsig analytics kikapcsolás (feature AB teszt, usage tracking) |
| `disableErrorReporting: true` | Sentry error logging kikapcsolás |
| `disableFeedbackDisplay: true` | Feedback prompt-ok elrejtése |
| `attribution: { commit: "", pr: "" }` | Co-author eltávolítása git commitokból és PR-ekből |

> [!info] CLI flag vs settings.json
> A `--disable-non-essential-traffic` CLI flag ugyanezt csinálja, de **az auto-update-eket is blokkolja** - a fenti 3 settings.json beállítás jobb, mert az update-ek megmaradnak.

---

## Sub-agent haladó konfigok

Az agent YAML-ben (`.claude/agents/`) ezek is beállíthatók a `model` és `tools` mellett:

| Config | Leírás |
|---|---|
| `skill` | Skill öröklés - alapból agent-ek NEM örökölnek skill-eket |
| `effort` | Token/thinking intenzitás (`low`, `medium`, `high`) |
| `hooks` | Agent-specifikus hook-ok - a globális hook-októl függetlenek |
| `background: true` | Mindig háttérben fut, nem szakítja meg a fő session-t |
| `isolation: worktree` | Izolált git worktree-ben dolgozik - merge/review utána |
| `tools` (agent nevek) | Mely más agent-eket spawn-olhat - megakadályozza a végtelen agent cascade-et |

---

## Path-specific rules

Ahogy [[toolbox/claude-mappa-anatomiaja|a .claude mappa anatómiájában]] benne van: a `.claude/rules/` mappába path-scoped `.md` fájlokat rakhatsz. Ezek **csak akkor töltődnek be** amikor Claude az adott fájl-mintára illeszkedő fájlt szerkeszti.

Mikor hasznos:
- Ha a CLAUDE.md túl hosszú lesz (200+ sor) és Claude elkezdi ignorálni az instrukciókat
- Frontend vs backend vs infra szabályok szétválasztására
- Specifikus library-k használati konvencióira (pl. "API route-okban mindig [[backend/hono|Hono]] middleware pattern-t használj")

---

## Hook exit code-ok

A hook-ok nem csak "lefutott / nem futott le" - az exit code-dal irányíthatod Claude viselkedését:

| Exit code | Hatás |
|---|---|
| `0` | Sikeres - az output általában nem kerül a context-be |
| `2` | **Blokkoló hiba** - az output visszamegy Claude-nak és kénytelen reagálni rá |
| Bármi más | Nem-blokkoló hiba - verbose módban látható, de Claude figyelmen kívül hagyja |

Az **exit code 2** a legértékesebb: ezzel kényszeríthetsz viselkedést. Például ha Claude mindig pip-et akar használni uv helyett -> PreToolUse Bash hook ami matchel a `pip install`-ra, kiírja "Használj uv-t" és `exit 2`-vel kilép.

Ez az alapja a **RALF loop-oknak** is (Recursive Agent Loop Feedback): hook-ok amik exit code 2-vel iteráltatják Claude-ot amíg a minőségi kritériumok nem teljesülnek.

---

## Egyéb hasznos dolgok

### `--agent` flag - közvetlen sub-agent futtatás

```bash
claude --agent security-reviewer
```

Közvetlenül az adott agent-ként indul Claude - nem kell a fő session-nek delegálni. Gyorsabb, kevesebb overhead.

### Prompt stashing (`Ctrl+S`)

Ha épp írsz egy promptot de előbb mást akarsz küldeni -> `Ctrl+S` elmenti az aktuális promptot. Miután elküldted a másikat, automatikusan visszajön. Hasznos "közben eszembe jutott valami" helyzetekben.

### [[toolbox/claude-code-agent-teams|Agent Teams]]

A lényeg: nem subagent, hanem **független Claude instance-ok** amik kommunikálnak egymással. Bekapcsolás: `"CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"` a settings.json `env` blokkjában.

### Claude CTX - profil váltó

Open-source tool ami `~/.claude/profiles/` mappában profil-onként kezeli a settings.json-t, CLAUDE.md-t és [[toolbox/mcp-model-context-protocol|MCP]]-t. Hasznos ha projektek között váltogatsz. Telepítés: `brew install claude-ctx`.

---

## AI-natív fejlesztés

A rejtett beállítások ismerete közvetlenül javítja az AI-natív fejlesztési élményt: a context window optimalizálás jobb output minőséget ad, a hook exit code-ok automatikus minőségellenőrzést tesznek lehetővé, és a path-specific rules célzott instrukciókkal segítik a subagent-eket.

> [!tip] Hogyan használd AI-val
> - *"Állítsd be a settings.json-t 1M context window-ra optimalizálva: terminalOutputLimit 150K, readTokenLimit 100K, autoCompact 75%"*
> - *"Írj egy PreToolUse hook-ot ami exit code 2-vel blokkolja ha Claude pip-et akar használni uv helyett"*

---

## Kapcsolódó

- [[toolbox/claude-code-projekt-setup|Claude Code]] - az eszköz maga, képességek és architektúra
- [[toolbox/claude-mappa-anatomiaja|A .claude mappa anatómiája]] - a .claude/ mappa felépítése, rules/, commands/, settings.json
- [[toolbox/claude-code-agent-teams|Claude Code Agent Teams]] - multi-agent koordináció részletesen
- [[github/claude-code-best-practice|Claude Code best practice]] - 84 tipp gyűjtemény
- [[toolbox/claude-code-building-structure|Claude Code Building Structure]] - CLAUDE.md és pipeline felépítés
