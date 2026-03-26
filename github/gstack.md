---
tags:
  - github
  - ai
  - workflow
  - skill
datum: 2026-03-26
szint: "🧱 Scout"
url: https://github.com/garrytan/gstack
kapcsolodo:
  - "[[toolbox/claude-code|Claude Code]]"
  - "[[toolbox/claude-code-agent-teams|Claude Code Agent Teams]]"
  - "[[toolbox/claude-code-skills-es-plugins|Claude Code Skills]]"
  - "[[_moc/moc-ai-tooling|MOC - AI Tooling]]"
---

# gstack

**Kategória:** `Claude Code workflow` / `skill pack`
**URL:** https://github.com/garrytan/gstack

---

## Mi ez és mire jó?

> [!tldr] Egy mondatban
> Y Combinator CEO Claude Code skill pack-je - 13 slash command ami egy teljes engineering csapatot szimulál: CEO review, QA, design audit, release management.

A gstack abból indul ki, hogy egy solo developer + [[toolbox/claude-code|Claude Code]] = egy csapat, ha a megfelelő **szerepek** és **folyamatok** meg vannak határozva. Minden parancs egy konkrét engineering szerep: `/plan-ceo-review` a product stratégiához, `/qa` a valódi browser-teszteléshez, `/ship` a release pipeline-hoz.

A legérdekesebb feature: a `/browse` és `/qa` parancsok **valódi Chromium-ot** nyitnak, kattintanak, és valódi bugokat találnak - nem csak kódot néznek.

---

## Mikor hasznos?

- **Solo founder / technikai alapító** - csapat-szintű review és QA nélkül szállítasz
- **Projekt release előtt** - `/qa` + `/ship` kombó: tesztel, auditál, PR-t nyit
- **Design minőség-ellenőrzés** - `/design-review` 80 pontos audit, AI slop detekció
- **Dokumentáció frissen tartás** - `/document-release` automatikusan szinkronizálja a README-t és ARCHITECTURE-t

### Mikor NE használd

- Ha már van saját skill/workflow rendszered ami jól működik - túl sok átfedés lehet
- Csapatban ahol valódi code review van - a `/review` nem helyettesít emberi reviewer-t komplex döntéseknél

---

## Telepítés

```bash
git clone https://github.com/garrytan/gstack.git ~/.claude/skills/gstack && cd ~/.claude/skills/gstack && ./setup
```

---

## AI-natív fejlesztés

Ez egy közvetlen [[toolbox/claude-code|Claude Code]] skill pack - a `~/.claude/skills/gstack/` mappába települ. A slash command-ok automatikusan elérhetők a session-ben.

> [!tip] Hogyan használd AI-val
> "Futtass egy /qa-t a landing page-en, aztán /ship-pelj ha minden zöld." - a gstack parancsai valódi böngészőt nyitnak és tesztelnek.

---

## Kapcsolódó

- [[toolbox/claude-code|Claude Code]] - az eszköz amire épül
- [[toolbox/claude-code-agent-teams|Claude Code Agent Teams]] - a gstack solo-ra optimalizált, Agent Teams csapatmunkára
- [[toolbox/claude-code-skills-es-plugins|Claude Code Skills]] - skill rendszer amire épül
