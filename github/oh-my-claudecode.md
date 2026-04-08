---
tags:
  - ai
  - plugin
  - multi-agent
datum: 2026-04-05
szint: "🧱 Brick"
kapcsolodo:
  - "[[github/get-shit-done|GSD]]"
  - "[[github/oh-my-codex|OMX]]"
  - "[[github/claw-code|Claw Code]]"
  - "[[toolbox/tmux|tmux]]"
  - "[[github/claude-code-best-practice|claude-code-best-practice]]"
  - "[[github/bmad-method|BMAD-METHOD]]"
---

# oh-my-claudecode (OMC)

**Kategória:** `agentic runtime` / `Claude Code plugin`
**URL:** https://github.com/yeachan-heo/oh-my-claudecode
**Stars:** ~24K

---

## Mi ez és mire jó?

> [!tldr] Egy mondatban
> Multi-agent orchestration plugin a [[toolbox/claude-code|Claude Code]]-hoz — specializált agent-eket koordinál párhuzamosan, smart model routing-gal és verify/fix loop-okkal.

Ahelyett, hogy egyetlen Claude session dolgozik a feladaton, az OMC **19 specializált agent-et** koordinál (executor, architect, verifier, code-reviewer, debugger, stb.). A fő execution model a **Team Mode** — egy staged pipeline:

```
team-plan → team-prd → team-exec → team-verify → team-fix (loop)
```

A smart model routing automatikusan választ modellt a feladat komplexitása alapján (Haiku egyszerű task-okra, Opus komplex reasoning-re).

---

## Mikor hasznos?

- **Komplex feature dev** — párhuzamos agent-ek gyorsabban haladnak mint egyetlen session
- **Kód review és QA** — dedikált reviewer és verifier agent-ek
- **"Just get it done"** — autopilot mód, nincs ceremony
- **Multi-AI workflow** — `ccg` paranccsal Claude + Codex + Gemini együtt dolgozik
- **AI slop cleanup** — beépített AI Slop Cleaner skill, dead code eltávolítás

### Mikor NE használd

- Ha a feladat egyszerű (bugfix, landing page) — overkill, a sima Claude Code elég
- Ha project lifecycle management kell (roadmap, milestones, UAT tracking) — arra a [[github/get-shit-done|GSD]] jobb
- Ha nem akarsz [[toolbox/tmux|tmux]]-ot — a Team mode tmux-ra épül

---

## Execution módok

| Mód | Keyword | Jelleg |
|---|---|---|
| **Team** | `/team 3:executor "..."` | Koordinált agent pipeline (ajánlott) |
| **Autopilot** | `autopilot: ...` | Autonóm single-agent végrehajtás |
| **Ultrawork** | `ulw ...` | Maximum párhuzamosság |
| **Ralph** | `ralph: ...` | Persistent verify/fix loop |
| **CCG** | `/ccg ...` | Claude + Codex + Gemini szintézis |

Egyéb magic keyword-ök: `deep-interview` (Socratic clarification), `deepsearch` (codebase search routing), `ultrathink` (deep reasoning), `ralplan` (iterative planning).

---

## Telepítés

```bash
# Plugin marketplace (ajánlott)
/plugin marketplace add https://github.com/Yeachan-Heo/oh-my-claudecode
/plugin install oh-my-claudecode
/setup

# VAGY npm
npm i -g oh-my-claude-sisyphus@latest
/omc-setup
```

**Követelmények:** Claude Code CLI, `brew install tmux`, opcionálisan Gemini CLI + Codex CLI a multi-AI workflow-hoz.

**Teams engedélyezése** (`~/.claude/settings.json`):
```json
{
  "env": {
    "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"
  }
}
```

---

## Skill rendszer

Az OMC skill-eket pointer-ként kezeli — rövid (50-70 sor) fájlok amik agent chain-eket indítanak:

- **Projekt scope:** `.omc/skills/` (version-controlled)
- **User scope:** `~/.omc/skills/` (személyes könyvtár)
- Auto-inject: ha a skill trigger match-el, automatikusan betöltődik

---

## OMC vs GSD

| | OMC | [[github/get-shit-done|GSD]] |
|---|---|---|
| **Fókusz** | Task execution orchestration | Project lifecycle management |
| **Erőssége** | Párhuzamos agent-ek, model routing | Roadmap, milestones, fázisok, UAT |
| **Mikor jó** | Gyors feature dev, komplex task-ok | Nagyobb projekt, tracking kell |
| **Learning curve** | Alacsony (magic keywords) | Közepes (sok command) |

---

## Kapcsolódó

- [[github/get-shit-done|GSD]] — alternatíva, strukturáltabb project lifecycle management
- [[github/oh-my-codex|OMX]] — testvérprojekt, ugyanez Codex CLI-hoz
- [[github/claw-code|Claw Code]] — Rust CLI amit OMX-szel építettek, az OMC/OMX ökoszisztéma showcase-e
- [[toolbox/tmux|tmux]] — szükséges a Team mode split-pane működéséhez
- [[github/claude-code-best-practice|claude-code-best-practice]] — Claude Code framework összehasonlítások
- [[github/bmad-method|BMAD-METHOD]] — másik agentic framework, persona-alapú megközelítéssel
