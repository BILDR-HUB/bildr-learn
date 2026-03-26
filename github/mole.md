---
tags:
  - github
  - cli
  - macos
datum: 2026-03-26
szint: "🌱 Newcomer"
url: https://github.com/tw93/mole
kapcsolodo:
  - "[[foundations/bash-es-linux-parancssor|Bash és Linux parancssor]]"
  - "[[toolbox/uj-mac-setup|Új Mac setup]]"
  - "[[toolbox/ranger|Ranger]]"
---

# Mole

**Kategória:** `CLI` / `macOS karbantartás`
**URL:** https://github.com/tw93/mole

---

## Mi ez és mire jó?

> [!tldr] Egy mondatban
> All-in-one macOS rendszerkarbantartó CLI - cache törlés, app eltávolítás, lemezhasználat elemzés, rendszer monitoring, egyetlen binárisban.

A Mole kiváltja a CleanMyMac-et, AppCleaner-t, DaisyDisk-et és az iStat Menus-t - **egyetlen Go bináris**, terminálból futtatva. Fejlesztőknek különösen hasznos a **project purge** funkció, ami a `node_modules`, `target`, `.next` és hasonló build artifact-okat takarítja.

---

## Mikor hasznos?

- **Lemezterület felszabadítás** - ha a Mac lassan megtelik cache-ekkel, logokkal, régi installer-ekkel
- **node_modules takarítás** - project purge: az összes projektedben felszabadítja a build artifact-okat (GB-okat)
- **App eltávolítás maradványok nélkül** - smart uninstaller ami a ~/Library rejtett fájljait is törli
- **Rendszer monitoring** - gyors CPU/RAM/disk/network áttekintés a terminálból
- **[[toolbox/uj-mac-setup|Új Mac setup]] után** - rendszeres karbantartási rutin felépítése

### Mikor NE használd

- Kritikus rendszerfájlok törléséhez - a safety-first design véd, de légy óvatos
- Ha GUI-s megoldás kell - ez CLI-only, nincs grafikus felület

---

## Telepítés

```bash
brew install mole
```

Alternatíva: single binary install shell script-tel a repo-ból.

---

## AI-natív fejlesztés

Terminálból futtatható, így [[toolbox/claude-code|Claude Code]] session-ben közvetlenül használható karbantartásra. Dry-run preview-val biztonságos, JSON output-tal szkriptelhető.

Két fő integráció:
1. **Hook-ként** - `SessionStart` hook-ban futtathatod a disk check-et, és figyelmeztet ha kevés a hely
2. **Launcher integráció** - gyors hozzáférés a leggyakoribb parancsokhoz Raycast/Alfred-ből

> [!tip] Hogyan használd AI-val
> "Futtass egy mole clean-t dry-run módban, és mutasd meg mennyi helyet szabadítanánk fel." - az AI agent biztonságosan futtatja a dry-run-t és megjeleníti az eredményt.

---

## Kapcsolódó

- [[foundations/bash-es-linux-parancssor|Bash és Linux parancssor]] - CLI alapok amiket a Mole is használ
- [[toolbox/uj-mac-setup|Új Mac setup]] - Mole beilleszthető a Mac setup workflow-ba
- [[toolbox/ranger|Ranger]] - másik terminál-alapú fájlkezelő, kiegészítik egymást
