---
tags:
  - ai
  - plugin
  - multi-agent
datum: 2026-04-05
szint: "🧱 Brick"
kapcsolodo:
  - "[[github/oh-my-claudecode|OMC]]"
  - "[[github/claw-code|Claw Code]]"
  - "[[toolbox/tmux|tmux]]"
  - "[[github/get-shit-done|GSD]]"
---

# oh-my-codex (OMX)

**Kategória:** `agentic runtime` / `Codex CLI plugin`
**URL:** https://github.com/yeachan-heo/oh-my-codex
**Stars:** ~16.5K

---

## Mi ez és mire jó?

> [!tldr] Egy mondatban
> Az [[github/oh-my-claudecode|OMC]] testvérprojektje, de az **OpenAI Codex CLI**-hoz — ugyanaz a multi-agent orchestration filozófia, más execution engine.

Workflow layer a Codex CLI köré, ami team mode-ot, skill-eket, auto-swarm-ot és event routing-ot ad hozzá. A vanilla Codex CLI-nak nincs HUD-ja, teammate pane-je, hook rendszere — az OMX mindezt pótolja [[toolbox/tmux|tmux]] alapokon.

### A fő különbség az OMC-hez képest

- **OMC** = Claude Code plugin → Claude modellek futtatják
- **OMX** = Codex CLI plugin → GPT-5.4 modellek futtatják
- **OMX SDK** = event extraction/injection Codex session-ökbe (amit a vanilla Codex nem tud)

---

## Mikor hasznos?

- **Ha Codex CLI-t is használsz** a Claude Code mellett (multi-AI workflow)
- **Ha az OMC `ccg` parancsát** akarod használni — az OMX-en keresztül hívja a Codex-et
- **GPT-5.4 specifikus task-ok** — ha egy feladathoz a Codex modell jobb eredményt ad

### Mikor NE használd

- Ha csak Claude Code-dal dolgozol — az [[github/oh-my-claudecode|OMC]] a te eszközöd
- Ha nincs Codex CLI / ChatGPT előfizetés

---

## Workflow

```
$deep-interview "..." → $ralplan "..." → $ralph "..." vagy $team N:executor "..."
```

| Lépés | Parancs | Célja |
|---|---|---|
| **Clarify** | `$deep-interview` | Intent, boundary-k, non-goal-ok tisztázása |
| **Plan** | `$ralplan` | Implementációs terv és trade-off-ok jóváhagyása |
| **Execute** | `$ralph` | Persistent completion loop egyetlen owner-rel |
| **Team** | `$team 3:executor` | Koordinált párhuzamos végrehajtás |

### Team mode működése

Az OMX [[toolbox/tmux|tmux]]-ban spawnolja a Codex session-öket. A team-plan session teammate Codex session-öket indít — **auto-swarm**: nem kell kézzel konfigurálni, az OMX maga koordinálja.

---

## Telepítés

```bash
npm install -g @openai/codex oh-my-codex
omx setup

# Ajánlott indítás
omx --madmax --high
```

**Szükséges:** Node.js 20+, Codex CLI, ChatGPT Plus ($20/hó) vagy Pro ($200/hó) előfizetés.
**Opcionális:** `brew install tmux` a team runtime-hoz.

---

## State management

A `.omx/` mappa tárolja: plans, logs, memory, runtime state. Projekt-szintű — version-controlled lehet.

---

## Multi-AI workflow

Ha multi-AI workflow-t akarsz (Claude + Codex együtt):

```bash
# Claude Code + OMC telepítés
/plugin marketplace add https://github.com/Yeachan-Heo/oh-my-claudecode
/plugin install oh-my-claudecode

# Codex CLI + OMX telepítés
npm i -g @openai/codex oh-my-codex

# Multi-AI használat OMC-ből
/ccg review this PR
```

---

## Kapcsolódó

- [[github/oh-my-claudecode|OMC]] — testvérprojekt, Claude Code-hoz
- [[github/claw-code|Claw Code]] — OMX-szel épített Rust CLI, az ökoszisztéma showcase-e
- [[toolbox/tmux|tmux]] — szükséges a team runtime működéséhez
- [[github/get-shit-done|GSD]] — alternatíva, más filozófia (project lifecycle vs runtime)
