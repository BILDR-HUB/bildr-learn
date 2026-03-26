---
tags:
  - moc
datum: 2026-03-26
---

# MOC - Claude Code

> [!tldr]
> Ez a Map of Content összegyűjti a repo összes Claude Code-hoz kapcsolódó jegyzetét. Bevezetőtől a haladó konfigurációkon át az agent teamekig — minden ami Anthropic CLI AI coding tool.

---

## Bevezető és áttekintés

| Jegyzet | Leírás |
|---------|--------|
| [[toolbox/claude-code\|Claude Code]] | Átfogó bevezető — mi ez, mire képes, telepítés, alaphasználat |
| [[toolbox/everything-claude-code\|Everything Claude Code]] | Teljes referencia — minden feature egy helyen, gyors áttekintés |

## Konfiguráció

| Jegyzet | Leírás |
|---------|--------|
| [[toolbox/claude-code-projekt-setup\|Claude Code projekt setup]] | CLAUDE.md, settings, hooks, MCP konfiguráció új projekthez |
| [[toolbox/claude-mappa-anatomiaja\|A .claude mappa anatómiája]] | A `.claude/` könyvtár felépítése — settings, commands, mcp, permissions |
| [[toolbox/claude-code-rejtett-beallitasok\|Claude Code rejtett beállítások]] | Haladó konfiguráció — settings.json, hook-ok, model override, env variable-ök |

## Best practice és módszertanok

| Jegyzet | Leírás |
|---------|--------|
| [[toolbox/claude-code-best-practice\|Claude Code best practice]] | Hatékony használat — kontextus kezelés, prompt minták, tipikus hibák elkerülése |
| [[toolbox/claude-code-building-structure\|Claude Code Building Structure]] | Nagyobb projektek építése — top-down pipeline, fázisos építés, scope management |

## Haladó funkciók

| Jegyzet | Leírás |
|---------|--------|
| [[toolbox/claude-code-skills-es-plugins\|Claude Code Skills és Plugins]] | Skill rendszer, custom plugin-ek, slash command-ok, testreszabás |
| [[toolbox/claude-code-agent-teams\|Claude Code Agent Teams]] | Párhuzamos agent munka — worktree-k, team orchestrálás, task elosztás |
| [[toolbox/claude-agent-sdk\|Claude Agent SDK]] | Saját agent-ek építése TypeScript SDK-val — programmatic Claude Code |

---

## Kapcsolódó note-ok

| Jegyzet | Kapcsolat |
|---------|-----------|
| [[toolbox/mcp-model-context-protocol\|MCP — Model Context Protocol]] | Szabványos interfész LLM-ek és eszközök között — Claude Code MCP-n keresztül bővíthető |
| [[toolbox/ai-first-fejlesztoi-workflow\|AI-first fejlesztői workflow]] | Hogyan strukturáld a munkádat Claude Code-dal — worktree-k, agent teamek |
| [[toolbox/get-shit-done\|Get Shit Done (GSD)]] | AI-natív fejlesztési workflow — Claude Code-ra optimalizált módszertan |
| [[toolbox/bmad-method\|BMAD-METHOD]] | Business Model Aligned Development — üzleti célokból kiinduló AI fejlesztés |
| [[toolbox/cursor-es-claude-konfiguracio\|Cursor és Claude konfiguráció]] | Claude Code + Cursor párhuzamos használat |
| [[toolbox/ai-coding-agentek-osszehasonlitasa\|AI coding agentek összehasonlítása]] | Claude Code vs Codex vs Antigravity vs Cursor |

---

## Tanulási útvonal

Ajánlott sorrend ha nulláról kezded:
1. [[toolbox/claude-code\|Claude Code]] — mi ez, telepítés, első lépések
2. [[toolbox/claude-code-projekt-setup\|Claude Code projekt setup]] — CLAUDE.md, settings, hooks beállítása
3. [[toolbox/claude-mappa-anatomiaja\|A .claude mappa anatómiája]] — értsd meg a könyvtár felépítését
4. [[toolbox/claude-code-best-practice\|Claude Code best practice]] — hatékony használat, tipikus hibák
5. [[toolbox/claude-code-building-structure\|Building Structure]] — nagyobb projektek struktúrált építése
6. [[toolbox/claude-code-rejtett-beallitasok\|Rejtett beállítások]] — haladó konfiguráció, finomhangolás
7. [[toolbox/claude-code-skills-es-plugins\|Skills és Plugins]] — custom skill-ek és plugin-ek
8. [[toolbox/claude-code-agent-teams\|Agent Teams]] — párhuzamos agent munka, team orchestrálás
9. [[toolbox/everything-claude-code\|Everything Claude Code]] — teljes referencia, ha bármit keresel

> [!tip] Hol kezdd?
> Ha most telepítetted a Claude Code-ot: [[toolbox/claude-code\|Claude Code]] bevezető → [[toolbox/claude-code-projekt-setup\|projekt setup]]. Ha már használod és hatékonyabb akarsz lenni: [[toolbox/claude-code-best-practice\|best practice]] → [[toolbox/get-shit-done\|GSD workflow]].

---

## Hézagok

- [ ] Claude Code + Git workflow — worktree-k, branch stratégia, PR automatizálás
- [ ] Claude Code debugging — hogyan debugolj hatékonyan agent-tel
- [ ] MCP szerver építés — saját MCP tool-ok fejlesztése Claude Code-hoz
- [ ] Claude Code team onboarding — csapat bevezetése az AI-first workflow-ba
