---
tags:
  - eszkoz
  - testing
  - e2e
datum: 2026-03-26
szint: "🧱 Brick"
kapcsolodo:
  - "[[toolbox/vitest|Vitest]]"
  - "[[toolbox/claude-code-projekt-setup|Claude Code]]"
  - "[[frontend/nextjs|Next.js]]"
  - "[[frontend/react-vs-spa-vs-preact|React]]"
  - "[[cloud/vercel|Vercel]]"
  - "[[cloud/docker-alapok|Docker]]"
---

# Playwright

**Kategória:** `testing framework` (E2E)
**URL:** https://playwright.dev
**Ár:** Ingyenes, open source (Microsoft)

---

## Mi ez és mire jó?

End-to-end böngésző tesztelés - valódi böngészőben (Chromium, Firefox, WebKit) futtatod a tesztjeidet, pont úgy ahogy egy felhasználó használná az appot.

A **Playwright** az E2E tesztelés de facto standardja. Nem unit teszteket írsz vele (arra [[toolbox/vitest|Vitest]]), hanem **teljes user flow-kat**: bejelentkezés, form kitöltés, navigáció, többoldalas workflow-k.

---

## Playwright vs Cypress vs Selenium

| Szempont | Selenium | Cypress | **Playwright** |
|----------|----------|---------|---------------|
| **Böngészők** | Minden | Chromium-alapú (+ kísérleti FF) | Chromium, Firefox, WebKit |
| **Sebesség** | Lassú | Közepes | Gyors (párhuzamos) |
| **Auto-waiting** | Nincs (kézi wait-ek) | Van | Van (legjobb) |
| **Nyelv** | Java, Python, JS, C# | Csak JS/TS | JS/TS, Python, Java, C# |
| **Debugging** | Nehézkes | Time travel UI | Trace Viewer (kiváló) |
| **Több tab/ablak** | Nehézkes | Nem támogatja | Natív |
| **Érettség** | Régi, enterprise | Elterjedt | Gyorsan növekvő (2020~) |

> [!tip] Döntés
> **Új projekt - Playwright.** Cypress-ről érdemes migrálni. Selenium-ot csak ha Java/enterprise kell.

---

## Fő képességek

- **Auto-waiting** - automatikusan vár amíg az elem klikkelhetővé válik, nem kell `sleep()`
- **Trace Viewer** - minden lépés screenshottal, DOM snapshot-tal visszanézhető
- **Codegen** - `npx playwright codegen` böngészőt nyit, rögzíti az akcióidat tesztkódként
- **Multi-browser** - egy teszt, három motor (Chromium, Firefox, WebKit)
- **API testing** - HTTP kéréseket is tesztelhetsz (nem csak UI-t)
- **Visual comparison** - screenshot-alapú regressziós tesztek

---

## Gyors setup

```bash
# Telepítés + böngészők letöltése
npm init playwright@latest

# Tesztek futtatása
npx playwright test                    # headless
npx playwright test --headed           # látható böngészővel
npx playwright test --ui               # interaktív UI módban

# Debug és segédeszközök
npx playwright codegen localhost:3000  # teszt rögzítés
npx playwright show-trace trace.zip   # trace visszanézés
```

---

## Claude Code + Playwright [[toolbox/mcp-model-context-protocol|MCP]]

A [[toolbox/claude-code-projekt-setup|Claude Code]]-ban **MCP szerveren keresztül** közvetlenül elérhető a Playwright böngésző-automatizáció - az agent tud böngészőt nyitni, elemeket keresni, screenshotot készíteni.

Ez nem tesztelésre való, hanem **fejlesztési workflow-ra**: élő app megnézése, UI ellenőrzés, debug.

---

## Hogyan használd promptban?

> [!tip] Claude Code E2E tesztelés prompt
> - *"Írj Playwright E2E teszteket a login flow-ra - happy path + hibás jelszó"*
> - *"A Playwright codegen-nel generálj alap tesztet, aztán refaktoráld POM mintára"*
> - *"A [[cloud/ci-cd-pipelines|CI/CD]]-ben Playwright teszteket futtatunk - adj hozzá GitHub Actions workflow-t"*
>
> Jelezd ha Playwright MCP-t akarsz használni vs. tesztet írni - két különböző dolog.
