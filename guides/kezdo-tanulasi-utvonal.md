---
tags:
  - guide
  - onboarding
datum: 2026-03-07
szint: "🌱 Newcomer"
kapcsolodo:
  - "[[_moc/moc-environment-setup|Environment Setup MOC]]"
---

# Kezdő tanulási útvonal

> [!tldr] 18 jegyzet, 5 fázisban. Ha most kezded a fejlesztést, kövesd ezt a sorrendet - minden fázis az előzőre épít.

Ez az útvonal végigvezet a fejlesztői alapokon. Nem kell mindent egyszerre elolvasnod - haladj a saját tempódban, de tartsd be a sorrendet, mert a fázisok egymásra épülnek.

---

## 1. fázis: Terminál és verziókezelés

> Először meg kell tanulnod a géppel beszélni. A terminál és a Git a fejlesztés két legfontosabb eszköze - ezek nélkül semmi más nem megy.

1. **[[foundations/bash-es-linux-parancssor|Bash és Linux parancssor]]** — alapvető parancsok, fájlrendszer navigáció
2. **[[foundations/bash-es-linux-parancssor-2|Bash és Linux parancssor 2]]** — haladó shell műveletek, pipe-ok, scriptek
3. **[[foundations/linux|Linux]]** — az operációs rendszer, amin minden fut
4. **[[foundations/git-es-github|Git és GitHub]]** — verziókezelés, a fejlesztés gerince

---

## 2. fázis: Fejlesztői környezet

> Most, hogy tudsz terminált használni, állítsd be a fejlesztői környezeted. Ez a fázis arról szól, hogyan izoláld a projektjeidet és kezeld a függőségeket.

5. **[[foundations/projekt-szintu-izolacio|Projekt szintű izoláció]]** — miért és hogyan válaszd el a projekteket
6. **[[foundations/csomagkezelok-es-cli-toolok|Csomagkezelők és CLI toolok]]** — npm, brew, és társaik
7. **[[toolbox/fnm|fnm]]** — Node.js verziók kezelése
8. **[[foundations/python-venv|Python venv]]** — Python virtuális környezetek

---

## 3. fázis: Editorok és terminál eszközök

> A hatékony fejlesztéshez jól kell ismerni az eszközeidet. Ezek a terminál-alapú eszközök napi szinten hasznos tudást adnak.

9. **[[toolbox/vi-editor|vi editor]]** — a mindenhol elérhető szövegszerkesztő
10. **[[toolbox/tmux|tmux]]** — terminál multiplexer, több ablak egyben
11. **[[toolbox/ranger|ranger]]** — terminál-alapú fájlkezelő

---

## 4. fázis: Első technológiai lépések

> Az alapok megvannak - itt kezdesz "igazi" technológiákkal dolgozni. Docker, adatbázis, és a frontend világ első lépése.

12. **[[cloud/docker-alapok|Docker alapok]]** — konténerizáció, a modern deploy alapja
13. **[[database/sql-adatbazisok|SQL adatbázisok]]** — relációs adatbázisok, a legtöbb app mögött ez áll
14. **[[frontend/css-vs-nextjs-vs-react|CSS vs Next.js vs React]]** — frontend technológiák összehasonlítása
15. **[[frontend/env-valtozok-nextjs-ben|Env változók Next.js-ben]]** — környezeti változók kezelése

---

## 5. fázis: Python tooling

> Ha Python-nal is dolgozol (vagy fogsz), ezek az eszközök elengedhetetlenek a tiszta környezethez.

16. **[[toolbox/pyenv|pyenv]]** — Python verziók kezelése
17. **[[toolbox/direnv|direnv]]** — könyvtár-alapú környezeti változók
18. **[[toolbox/uj-mac-setup|Új Mac setup]]** — teljes fejlesztői környezet nulláról

---

## Mi következik?

Ha végeztél az útvonallal, válaszd ki a következő témát a MOC-ok közül:

- **[[_moc/moc-environment-setup|Environment Setup]]** — mélyebb merülés a dev környezetbe
- **[[_moc/moc-docker|Docker]]** — konténerizáció a következő szintre
- **[[_moc/moc-database|Database]]** — adatbázisok mélyebben

Vagy nézz szét a [[START-HERE|START-HERE]] oldalon a többi tanulási útvonalért.

---

*bildr.hub — Build. Learn. Grow.*
