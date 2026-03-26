---
tags:
  - github
  - ai
  - browser
datum: 2026-03-26
szint: "🧱 Scout"
url: https://github.com/pasky/chrome-cdp-skill
kapcsolodo:
  - "[[toolbox/claude-code|Claude Code]]"
  - "[[toolbox/playwright|Playwright]]"
  - "[[_moc/moc-ai-tooling|MOC - AI Tooling]]"
---

# chrome-cdp-skill

**Kategória:** `browser automation` / `AI skill`
**URL:** https://github.com/pasky/chrome-cdp-skill

---

## Mi ez és mire jó?

> [!tldr] Egy mondatban
> Hozzáférést ad az AI agent-nek a te élő Chrome session-ödhöz - bejelentkezett állapottal, meglévő tab-okkal, extra böngésző nélkül.

A legtöbb browser automation ([[toolbox/playwright|Playwright]], Puppeteer) egy **új, üres** böngészőt indít. A chrome-cdp-skill viszont a te **már futó** Chrome-odhoz csatlakozik a Chrome DevTools Protocol-on (CDP) keresztül. Ez azt jelenti: bejelentkezett Gmail, GitHub, belső admin felületek - mind elérhetők az AI számára.

100+ nyitott tab-ot is kezel timeout nélkül, és daemon process-eket tart fenn tab-onként.

---

## Mikor hasznos?

- **Bejelentkezett oldalak scrapelése** - belső dashboard-ok, admin panelek, CRM adatok kinyerése
- **Debugging élő oldalon** - "nézd meg mi van a console-ban ezen az oldalon"
- **E2E tesztelés saját session-nel** - nem kell külön test account, a te bejelentkezésed használja
- **Screenshot és vizuális feedback** - az agent látja amit te látsz a böngészőben

### Mikor NE használd

- CI/CD pipeline-ban - ott nincs élő Chrome session, használj headless megoldást
- Ha izolált, reprodukálható tesztek kellenek - a CDP az élő session-t használja, nem determinisztikus

---

## AI-natív fejlesztés

Skill-ként telepíthető [[toolbox/claude-code|Claude Code]]-ba (`skills/chrome-cdp/` mappa). A [[toolbox/playwright|Playwright]] kiegészítése: a Playwright a programmatic/CI használatra való, a chrome-cdp az interaktív, session-alapú hozzáférésre.

> [!tip] Hogyan használd AI-val
> "Használd a chrome-cdp-t és olvasd ki a bejelentkezett Stripe dashboard-ról az utolsó 10 tranzakciót." - az AI agent a te élő bejelentkezéseddel dolgozik, nem kell credentials-t átadni.

---

## Kapcsolódó

- [[toolbox/playwright|Playwright]] - headless browser automation (chrome-cdp a live session alternatíva)
- [[toolbox/claude-code|Claude Code]] - skill-ként integrálódik
