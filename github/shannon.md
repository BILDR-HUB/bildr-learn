---
tags:
  - github
  - ai
  - security
datum: 2026-03-26
szint: "🏗️ Builder"
url: https://github.com/KeygraphHQ/shannon
kapcsolodo:
  - "[[toolbox/claude-code|Claude Code]]"
  - "[[toolbox/claude-code-building-structure|Claude Code Building Structure]]"
  - "[[toolbox/aikido|Aikido]]"
  - "[[_moc/moc-ai-tooling|MOC - AI Tooling]]"
---

# Shannon

**Kategória:** `pentesting` / `security`
**URL:** https://github.com/KeygraphHQ/shannon

---

## Mi ez és mire jó?

> [!tldr] Egy mondatban
> Autonóm AI pentester - forráskódot elemez, attack vector-okat azonosít, és valódi exploit-okat futtat a futó alkalmazás ellen, egyetlen paranccsal.

A Shannon white-box security tesztelő: megkapja a forráskódot ÉS a futó app URL-jét, és mindkettőt kombinálja. Először a kódból azonosítja a potenciális sebezhetőségeket, aztán [[toolbox/playwright|Playwright]]-tal és CLI tool-okkal **valóban megpróbálja kihasználni** őket. Csak az jelenik meg a reportban, amihez van működő proof-of-concept exploit.

OWASP lefedettség: injection, XSS, SSRF, broken auth, authorization bypass.

---

## Mikor hasznos?

- **Projekt átadás előtt** - security audit automatikusan, mielőtt élesbe megy
- **Saját app biztonsági ellenőrzése** - különösen auth, API endpoint-ok, form handling
- **Compliance requirement** - ha security audit-ot kérnek, ez az első szűrő
- **Tanulás** - a report megmutatja a konkrét sebezhetőséget és az exploit-ot

### Mikor NE használd

- Production rendszeren engedély nélkül - ez valódi exploit-okat futtat
- Nem a te alkalmazásodon - csak saját vagy engedélyezett target-en
- Mint egyetlen security megoldás - ez automatizált teszt, nem helyettesít emberi security review-t

---

## AI-natív fejlesztés

Nem [[toolbox/claude-code|Claude Code]] skill, hanem önálló Docker-alapú tool. Viszont a fejlesztési workflow-ba illeszthető: build, test, **Shannon security scan**, deploy. A [[toolbox/claude-code-building-structure|Claude Code Building Structure]] pipeline végén, a subagent-ek munkája után érdemes futtatni.

Az [[toolbox/aikido|Aikido]]-val kiegészítik egymást: az Aikido folyamatos monitoring és SAST/DAST, a Shannon egyszeri, mély pentesting scan.

> [!tip] Hogyan használd AI-val
> "Az app kész, futtass egy Shannon scan-t a staging URL-en a forráskóddal mielőtt deploy-olunk." - az AI agent a Docker container-ben futtatja a scan-t és összefoglalja az eredményeket.

---

## Kapcsolódó

- [[toolbox/claude-code|Claude Code]] - a fejlesztési workflow-ba illeszkedik mint pre-deploy security check
- [[toolbox/claude-code-building-structure|Claude Code Building Structure]] - a pipeline végén használható
- [[toolbox/aikido|Aikido]] - folyamatos security monitoring (Shannon az egyszeri scan)
