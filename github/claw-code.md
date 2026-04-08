---
tags:
  - ai
  - cli
  - alternativa
datum: 2026-04-05
szint: "🧱 Brick"
kapcsolodo:
  - "[[github/oh-my-claudecode|OMC]]"
  - "[[github/oh-my-codex|OMX]]"
  - "[[github/get-shit-done|GSD]]"
  - "[[github/claude-code-best-practice|claude-code-best-practice]]"
---

# Claw Code

**Kategória:** `Claude Code alternatíva` / `Rust CLI`
**URL:** https://github.com/ultraworkers/claw-code
**Stars:** ~170K (24 óra alatt — leggyorsabban növő GitHub repo a történelemben)

---

## Mi ez és mire jó?

> [!tldr] Egy mondatban
> Open-source Rust reimplementáció a Claude Code CLI-nak — NEM plugin, hanem önálló alternatív CLI tool ami a Claude API-val kommunikál.

> [!warning] Nem Anthropic affiliated
> Ez a repo **nem Anthropic által készített vagy támogatott**. Közösségi projekt.

A Claw Code nem az [[github/oh-my-claudecode|OMC]] vagy [[github/oh-my-codex|OMX]] kategóriájába esik (azok plugin-ek). Ez egy **teljes CLI alkalmazás** ami a Claude Code helyettesítésére törekszik, Rust-ban írva.

### 3 réteg

| Réteg | Szerepe |
|---|---|
| **OmX** | Directives → structured workflow-ok (plan, execute, verify) |
| **ClawHip** | Event routing és notification gateway, context pollution nélkül |
| **OmO** | Multi-agent coordination, disagreement resolution |

---

## Parity a Claude Code-dal

| Feature | Státusz |
|---|---|
| File/bash/search ops | Kész |
| Permission enforcement | Kész |
| Task/team/cron registries | Kész |
| MCP/LSP bridge | Kész |
| Interactive tools (AskUser, Remote) | Stub |
| Subprocess orchestration | Hiányos |
| CI stabilitás | Nem stabil |

**40 tool spec** van definiálva, de sok még shallow implementáció. **Nem production ready.**

---

## Hogyan készült?

Ez a repo az egyik legjobb **OMX showcase**: a készítők repülőn építették, airplane WiFi-n, **telefonról irányítva** OpenClaw-on keresztül. Az OpenClaw-ok egymással kommunikáltak és [[github/oh-my-codex|OMX]] session-öket spawn-oltak egy home szerveren.

3 óra alatt volt kész az alap scaffolding az OMX-szel.

---

## Mikor hasznos?

- **Open-source kell** a Claude Code CLI helyett
- **Rust-ban** akarod customizálni a CLI-t
- **API key** módban dolgozol (nem subscription)
- **Tanulmányozásra** — hogyan épül fel egy agentic CLI tool

### Mikor NE használd

- Ha Claude Code-ot használsz Max subscription-nel — nincs rá szükséged
- Ha production-ready tool kell — sok feature még stub
- Ha plugin-t keresel a Claude Code-hoz — az az [[github/oh-my-claudecode|OMC]]

---

## Kapcsolódó

- [[github/oh-my-claudecode|OMC]] — Claude Code plugin, az OMC/OMX ökoszisztéma része
- [[github/oh-my-codex|OMX]] — Codex CLI plugin amivel a Claw Code épült
- [[github/get-shit-done|GSD]] — alternatív agentic framework, más megközelítéssel
- [[github/claude-code-best-practice|claude-code-best-practice]] — Claude Code optimalizálási referencia
