---
tags:
  - github
  - open-source
  - erp
datum: 2026-03-26
szint: "🧱 Scout"
url: https://github.com/puemos/craftplan
kapcsolodo:
  - "[[toolbox/n8n|n8n]]"
  - "[[toolbox/claude-code|Claude Code]]"
---

# craftplan

**Kategória:** `ERP` / `üzleti szoftver`
**URL:** https://github.com/puemos/craftplan

---

## Mi ez és mire jó?

> [!tldr] Egy mondatban
> Self-hosted, open-source ERP kézműves/kistermelő D2C (Direct-to-Consumer) vállalkozásoknak - receptúra, gyártás, készlet, rendelés, számlázás egy helyen.

**D2C = Direct-to-Consumer:** a gyártó közvetlenül a végfelhasználónak ad el, nincs köztes kiskereskedő. Pl. kávépörkölő saját webshoppal, kézműves szappanos, kisüzemi lekvárgyártó.

A craftplan ezt a teljes workflow-t kezeli:

| Modul | Mit csinál |
|---|---|
| **Receptúra (BOM)** | Verziókezelt alapanyag-listák, automatikus költségkalkuláció |
| **Gyártás** | Batch kezelés, anyagfelhasználás tracking, munkaóra követés |
| **Készlet** | Lot traceability, allergen/tápérték info, reorder ajánlás |
| **Rendelés** | Ügyfél rendelés, batch allokáció, naptár integráció |
| **Számlázás** | Invoice generálás, ügyfél adatbázis |

Tech stack: Elixir + Phoenix LiveView + PostgreSQL. Docker Compose-zal telepíthető.

---

## Mikor hasznos?

- **Kézműves/kistermelő ügyfél** aki közvetlenül értékesít - kész megoldás, nem kell nulláról ERP-et építeni
- **Élelmiszeripari ügyfél** - allergen/tápérték tracking beépítve (compliance)
- **Inspiráció** - ERP modulok felépítése, ha saját rendszert építenél

### Mikor NE használd

- Nem D2C vállalkozáshoz - nagyker/disztribútor workflow-ra nincs optimalizálva
- Ha Shopify/WooCommerce kell - a craftplan a gyártási oldalra fókuszál, nem webshop
- Ha Elixir-ben nem akarsz fejleszteni - a testreszabás Elixir tudást igényel

---

## AI-natív fejlesztés

Nem [[toolbox/claude-code|Claude Code]] tool, hanem önálló alkalmazás. Viszont a testreszabást [[toolbox/claude-code|Claude Code]]-dal végezheted (Elixir/Phoenix kód módosítás), és az [[toolbox/n8n|n8n]] segítségével automatizálhatod az integrációkat.

> [!tip] Hogyan használd AI-val
> "Klónozd a craftplan repót és add hozzá egy egyedi riport modult a gyártási batch-ekhez." - az AI agent képes az Elixir/Phoenix kódbázisban navigálni és módosításokat végezni.

---

## Kapcsolódó

- [[toolbox/n8n|n8n]] - automatizáció integráláshoz (pl. rendelés beérkezik, n8n, craftplan API)
- [[toolbox/claude-code|Claude Code]] - a testreszabáshoz használható
