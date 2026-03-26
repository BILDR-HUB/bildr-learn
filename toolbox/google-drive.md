---
tags:
  - eszkoz
  - cloud-storage
datum: 2026-03-26
szint: "🌱 Newcomer"
kapcsolodo:
  - "[[toolbox/n8n|n8n]]"
  - "[[cloud/cloudflare|Cloudflare]]"
---

# Google Drive

**Kategória:** `cloud storage` / `együttműködés`
**URL:** https://drive.google.com

---

## Mi ez és mire jó?

Google felhő-alapú tárolás és együttműködési platform. Dev szempontból: Sheets mint könnyű adatforrás, Docs specifikációkhoz, Drive API automatizáláshoz.

---

## Dev use case-ek

A Google Drive nem csak fájltárolás - fejlesztőként ezekre jó:

- **Google Sheets mint lightweight DB** - konfigurációs adatok, fordítások, listák tárolása. API-val olvasható, nem-tech emberek szerkeszthetik
- **Google Docs specifikációkhoz** - megosztott spec dokumentumok, kommentelés, verziókezelés
- **Drive API fájlszinkronhoz** - automatikus fájl feltöltés/letöltés pipeline-okban
- **Service account-ok** - szerver-oldali hozzáférés emberi beavatkozás nélkül

---

## Összehasonlítás

| Eszköz | Mikor válaszd |
|--------|--------------|
| **Google Drive** | Együttműködés csapattal, Sheets mint adatforrás, ismert platform |
| **Notion** | Strukturáltabb tudásbázis, projekt management |
| **[[cloud/cloudflare|Cloudflare]] R2** | Object storage alkalmazásokhoz, S3-kompatibilis, olcsó egress |
| **S3** | AWS ökoszisztéma, nagy volumen, infrastruktúra-szintű tárolás |

---

## Integrációs lehetőségek

| Integráció | Mire jó |
|------------|---------|
| **Drive API** | Fájlok CRUD, megosztás automatizálás |
| **Sheets API** | Sheets olvasás/írás kódból - lightweight backend |
| **[[toolbox/n8n|n8n]] workflow** | Drive trigger - feldolgozás - válasz (no-code automatizálás) |
| **Service account** | Szerver-oldali hozzáférés, nincs OAuth popup |

---

## Gyakorlati tippek

- **Service account setup**: Google Cloud Console - Service Account - JSON key - env variable-ként tárold
- **Sheets mint config**: ha az ügyfél akar szerkeszteni adatot, Sheets + API egyszerűbb mint admin panel építés
- **Megosztás**: service account email-jét add hozzá a Drive mappához, nem kell OAuth

---

## Hogyan használd promptban

> [!tip] Claude Code kontextus
> Drive/Sheets API integrációhoz [[toolbox/claude-code-projekt-setup|Claude Code]] jól tud segíteni - a service account JSON-t env variable-ként kezeld, és a Google API client library-t használd. Automatizálási workflow-okhoz az n8n + Drive kombó erős.
