---
tags:
  - ai
  - claude-code
  - konfig
datum: 2026-03-26
szint: "🧱 Scout"
kapcsolodo:
  - "[[toolbox/claude-code-projekt-setup|Claude Code projekt setup]]"
  - "[[toolbox/claude-code-rejtett-beallitasok|Claude Code rejtett beállítások]]"
  - "[[toolbox/claude-code-best-practice|Claude Code best practice]]"
  - "[[toolbox/claude-code-building-structure|Claude Code Building Structure]]"
---

# A .claude/ mappa anatómiája

> [!tldr] Miért releváns
> A `.claude/` mappa a [[toolbox/claude-code-projekt-setup|Claude Code]] viselkedésének vezérlőközpontja. A legtöbb ember black box-ként kezeli - pedig ez a highest-leverage konfigurációs pont.

---

## Két mappa, nem egy

| Mappa | Scope | Git | Tartalom |
|---|---|---|---|
| `projekt/.claude/` | Projekt + team | Commitold | commands, rules, skills, agents, settings.json |
| `~/.claude/` | Személyes, minden projektben | NEM | CLAUDE.md, commands, skills, agents, session history, auto-memory |

A projekt-szintű közös, a globális személyes. Mindkettőben lehet CLAUDE.md, commands, skills, agents.

## CLAUDE.md - 200 sor alatt tartsd

A legfontosabb szabály: **200 sor felett a Claude instruction adherence csökken** - túl sok context-et eszik. Ami bele kell:

- Build/test/lint parancsok
- Architektúra döntések (stack, monorepo, stb.)
- Nem-nyilvánvaló gotchas
- Import és naming konvenciók

Ami **NEM** kell: linter/formatter config (az a tool dolga), hosszú elméleti magyarázatok, dokumentáció amit linkelni is lehet.

## Commands vs Skills vs Agents - döntési fa

| | Command | Skill | Agent |
|---|---|---|---|
| **Trigger** | Te hívod (`/review`) | Claude automatikusan felismeri | Claude spawn-olja ha kell |
| **Fájl** | Egy .md fájl | Mappa + SKILL.md + kiegészítő fájlok | Egy .md fájl |
| **Scope** | Egy prompt template | Workflow csomag (több fájl) | Izolált context window |
| **Mikor használd** | Ismétlődő feladat amit te indítasz | Komplex workflow ami automatikusan kell | Specializált munka ami sok context-et igényel |

**Gyakorlati szabály:**
- Ha naponta 2x csinálod -> **command**
- Ha Claude-nak magától kell felismernie -> **skill**
- Ha izolált specialist kell (code review, security audit) -> **agent**

## rules/ mappa - path-scoped instrukciók

A `.claude/rules/` mappába rakott .md fájlok automatikusan betöltődnek. A frontmatter-ben `paths:` mezővel szűrhetsz:

```yaml
---
paths:
  - "src/api/**/*.ts"
---
```

Ez a rule csak akkor aktív amikor Claude API fájlokat szerkeszt. **Ez a pattern ha a CLAUDE.md túl hosszú lesz.**

## settings.json - háromszintű permission

| Lista | Hatás |
|---|---|
| `allow` | Kérdezés nélkül fut |
| `deny` | Teljesen blokkolva |
| *(se allow, se deny)* | Megkérdezi - ez a safety net |

A "middle ground" a lényeg: nem kell mindent előre megmondani, a nem-listázott dolgokra rákérdez.

---

## Hogyan alkalmazd

**Progresszív setup - ne egyszerre:**

1. **CLAUDE.md** -> `/init`-tel generálj, szerkeszd 20-30 sorra
2. **settings.json** -> allow: run commands + git read; deny: rm -rf, .env
3. **1-2 command** -> code review + issue fix a legértékesebb
4. **rules/** -> csak ha a CLAUDE.md 200+ sor lesz, path-scope-pal
5. **Skills/agents** -> csak ha ismétlődő komplex workflow van

> [!tip] Kulcs gondolat
> A .claude/ mappában van settings.json permission control, rules/ mappában path-scoped instrukciók, és commands/ mappában slash command-ok. Progresszíven építsd fel - nem kell mindent az első napon.

---

## AI-natív fejlesztés

A .claude/ mappa helyes konfigurálása az AI-natív fejlesztés alapja: a CLAUDE.md a projekt memóriája, a commands/ az ismétlődő feladatok automatizálása, a rules/ a célzott instrukciók, és az agents/ a specializált subagent-ek. A progresszív setup megközelítés biztosítja, hogy ne legyen túl sok overhead az elején.

> [!tip] Hogyan használd AI-val
> - *"Nézd át a CLAUDE.md-t és ha 200 sornál hosszabb, bontsd ki a path-specific szabályokat .claude/rules/ mappába"*
> - *"Készíts egy /review slash command-ot ami code review-t futtat a staged változásokra"*

---

## Kapcsolódó

- [[toolbox/claude-code-projekt-setup|Claude Code]] - az eszköz amihez a .claude/ mappa tartozik
- [[toolbox/claude-code-building-structure|Claude Code Building Structure]] - a projekt-szintű CLAUDE.md és pipeline felépítése
- [[toolbox/claude-code-best-practice|Claude Code best practice]] - 84 tipp amik részben a .claude/ konfigurációról szólnak
- [[toolbox/claude-code-rejtett-beallitasok|Claude Code rejtett beállítások]] - settings.json flag-ek, context window optimalizálás, hook exit code-ok
