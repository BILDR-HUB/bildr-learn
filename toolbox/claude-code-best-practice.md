---
tags:
  - ai
  - claude-code
  - referencia
datum: 2026-03-26
szint: "🧱 Brick"
kapcsolodo:
  - "[[toolbox/claude-code-projekt-setup|Claude Code]]"
  - "[[toolbox/everything-claude-code|Everything Claude Code]]"
  - "[[toolbox/bmad-method|BMAD-METHOD]]"
  - "[[toolbox/get-shit-done|Get Shit Done (GSD)]]"
  - "[[toolbox/claude-code-agent-teams|Claude Code Agent Teams]]"
  - "[[toolbox/mcp-model-context-protocol|MCP]]"
---

# Claude Code best practice

**URL:** https://github.com/shanraisshan/claude-code-best-practice
**Stars:** ~20.5K

> [!tldr] Egy mondatban
> A legátfogóbb [[toolbox/claude-code-projekt-setup|Claude Code]] best-practice gyűjtemény - 84 tipp, fejlesztési workflow-k összehasonlítása (Superpowers, BMAD, gstack, stb.), és az összes Claude Code feature legjobb használati mintái egy helyen.

---

## Mi ez és mire jó?

Ez a repó nem tool és nem skill-gyűjtemény, hanem **referencia-wiki**: hogyan használd a Claude Code minden egyes feature-jét a leghatékonyabban. A tippek nagy része a Claude Code alkotójától és más power user-ektől származik.

## Struktúra - mit találsz benne

| Szekció | Tartalom |
|---|---|
| **Concepts** | Subagents, Commands, Skills, Hooks, [[toolbox/mcp-model-context-protocol|MCP]], Plugins, Memory, Settings - mindhez best practice + implementáció |
| **Development Workflows** | 8+ agentic framework összehasonlítása (star count, egyediség, agent/command/skill arány) |
| **Tips és Tricks (84)** | Prompting, planning, CLAUDE.md, agents, commands, skills, hooks, debugging - kategorizálva |
| **Orchestration Workflow** | Command -> Agent -> Skill pattern implementáció |

---

## Mikor hasznos?

- **Egy adott Claude Code feature optimalizálása** - pl. hogyan írd meg a CLAUDE.md-t, hogyan használj subagent-eket, milyen hook-ok hasznosak
- **Framework összehasonlítás** - ha [[toolbox/bmad-method|BMAD-METHOD]], [[toolbox/everything-claude-code|everything-claude-code]], Superpowers, gstack vagy más workflow-t mérlegelsz
- **Onboarding** - ha most kezded a Claude Code-ot, ez a legjobb kiindulópont

### Mikor NE használd

- Mint kész install - ez nem egy telepíthető rendszer, hanem olvasni/böngészni való referencia
- Ha a saját workflow-d jól működik - ne változtass mindent egyszerre, válogass célzottan

---

## Top 5 takeaway

1. **Plan mode mindig** - "always start with plan mode"
2. **Skills > általános agent-ek** - feature-specifikus subagent-ek skill-ekkel, ne általános "backend engineer" típusúak
3. **CLAUDE.md 200 sor alatt** - ha hosszabb, spliteld `.claude/rules/`-ba
4. **Slash command mindenre amit naponta 2x csinálsz** - ha ismételsz, automatizáld
5. **Esc Esc / /rewind** - ha Claude eltéved, ne próbáld javítani, hanem tekerd vissza

> [!tip] Hogyan használd
> Nem promptban használod - böngészd a repót, és ami releváns, azt adaptáld a saját `~/.claude/skills/`, `.claude/commands/`, vagy CLAUDE.md-be.

---

## Összehasonlítás hasonló referencia repókkal

| | claude-code-best-practice | [[toolbox/everything-claude-code|everything-claude-code]] |
|---|---|---|
| **Fókusz** | Tippek + framework összehasonlítás | Skill + instinct gyűjtemény |
| **Használat** | Referencia - olvasd és válogass | Referencia - skill-ek adaptálhatók |
| **Forrás** | Közösség + core team tippjei | Community open-source |

---

## AI-natív fejlesztés

Ez a repó maga IS az AI-natív fejlesztés referenciája - a 84 tipp, framework összehasonlítások és orchestration pattern-ek mind arról szólnak, hogyan építs szoftvert AI-val hatékonyan. A legértékesebb szekciók a CLAUDE.md optimalizálás, a subagent orchestráció és a skill/command/agent döntési fa.

> [!tip] Hogyan használd AI-val
> - *"Nézd át a claude-code-best-practice repó 'Development Workflows' szekcióját és hasonlítsd össze a BMAD és GSD framework-öt a mi projektünk kontextusában"*
> - *"A repó 84 tippjéből válogasd ki a CLAUDE.md optimalizálásra vonatkozókat és adaptáld a mi projektünkre"*

---

## Kapcsolódó

- [[toolbox/claude-code-projekt-setup|Claude Code]] - az eszköz amiről a repó szól
- [[toolbox/everything-claude-code|Everything Claude Code]] - másik nagy Claude Code referencia repó
- [[toolbox/claude-code-agent-teams|Claude Code Agent Teams]] - a repóban részletezett feature
- [[toolbox/mcp-model-context-protocol|MCP]] - az MCP best practice szekció
- [[toolbox/bmad-method|BMAD-METHOD]] - a repóban összehasonlított framework
- [[toolbox/get-shit-done|Get Shit Done (GSD)]] - a repóban összehasonlított framework
