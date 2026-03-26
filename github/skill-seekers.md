---
tags:
  - github
  - ai
  - skill
datum: 2026-03-26
szint: "🧱 Scout"
url: https://github.com/yusufkaraaslan/Skill_Seekers
kapcsolodo:
  - "[[toolbox/claude-code|Claude Code]]"
  - "[[toolbox/claude-code-skills-es-plugins|Claude Code Skills]]"
  - "[[github/superpowers|superpowers]]"
  - "[[github/gstack|gstack]]"
  - "[[_moc/moc-ai-tooling|MOC - AI Tooling]]"
---

# Skill Seekers

**Kategória:** `skill generátor`
**URL:** https://github.com/yusufkaraaslan/Skill_Seekers

---

## Mi ez és mire jó?

> [!tldr] Egy mondatban
> Dokumentációs oldalakat, GitHub repókat és PDF-eket automatikusan [[toolbox/claude-code|Claude Code]] skill-ekké konvertál, conflict detection-nel.

Ha találsz egy library-t vagy tool-t aminek jó a dokumentációja, a Skill Seekers automatikusan skill-t generál belőle. Ez azt jelenti: nem kell kézzel megírni a SKILL.md-t - a tool kinyeri a lényeget a docs-ból és strukturált skill formátumba önti.

A conflict detection fontos: ha már van hasonló skill, jelzi az átfedést.

---

## Mikor hasznos?

- **Új library/framework tanulása** - a docs-ból generált skill kontextust ad [[toolbox/claude-code|Claude Code]]-nak
- **Saját docs skill konverzió** - ha van belső dokumentációd, skill-lé alakíthatod
- **Skill gyűjtemény bővítése** - gyorsan generálhatsz skill-eket a gyakran használt tool-jaidhoz

### Mikor NE használd

- Ha a skill-nek sajátos logikára van szüksége - a generált skill csak a docs tartalmát tudja, nem custom workflow-t
- Ha már van official skill az adott tool-hoz - az karbantartottabb lesz

---

## AI-natív fejlesztés

A generált skill-ek a `~/.claude/skills/` vagy `.claude/skills/` mappába kerülnek. A [[toolbox/claude-code|Claude Code]] automatikusan felismeri és használja őket. Jól kombinálható a [[github/superpowers|superpowers]] és [[github/gstack|gstack]] skill-ekkel - azok a workflow-t adják, a Skill Seekers a domain tudást.

> [!tip] Hogyan használd AI-val
> "Generálj egy Claude Code skill-t a Hono framework dokumentációjából." - a Skill Seekers automatikusan kinyeri a releváns API referenciákat, best practice-eket és code example-öket.

---

## Kapcsolódó

- [[toolbox/claude-code|Claude Code]] - a generált skill-ek ide kerülnek
- [[toolbox/claude-code-skills-es-plugins|Claude Code Skills]] - a skill rendszer amibe integrálódik
- [[github/superpowers|superpowers]] - workflow skill (Skill Seekers a domain tudás skill)
- [[github/gstack|gstack]] - másik skill pack amihez kiegészítő
