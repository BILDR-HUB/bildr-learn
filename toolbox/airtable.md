---
tags:
  - eszkoz
  - no-code
datum: 2026-03-26
szint: "🌱 Newcomer"
kapcsolodo:
  - "[[toolbox/n8n|n8n]]"
  - "[[database/supabase|Supabase]]"
---

# Airtable

**Kategória:** `adatbázis` / `spreadsheet` / `no-code`
**URL:** https://airtable.com

---

## Mi ez és mire jó?

Spreadsheet kinézetű adatbázis - Google Sheets egyszerűségével, de relációs adatbázis képességekkel, API-val és automatizálásokkal.

Az **Airtable** egy hibrid eszköz: úgy néz ki mint egy táblázat, de valójában relációs adatbázis rejtőzik mögötte. Táblákat köthetsz össze foreign key-ekkel, különböző nézetek (Kanban, naptár, Gantt, galéria) vannak, és minden táblához automatikusan generál REST API-t.

A fő ereje: **non-tech csapattagok is tudják használni** (úgy érzik, spreadsheet-ben dolgoznak), miközben a fejlesztő API-n keresztül programozottan is eléri az adatokat. Ez teszi ideálissá olyan projektek adatkezeléséhez, ahol nem-technikai embereknek is szerkeszteniük kell az adatot.

---

## Mikor hasznos?

- **Ügyfél-kezelt tartalom** - az ügyfél spreadsheet-szerűen szerkeszti, te API-n olvasod a frontendről
- **Projekt management** - Kanban, naptár, Gantt nézetek, nem kell külön PM tool
- **CRM light** - ügyfélnyilvántartás, sales pipeline, ha a Salesforce overkill
- **Tartalom pipeline** - blog post-ok, social media ütemezés, content calendar
- **Prototípus backend** - gyorsan van API mögé adat, mielőtt igazi DB-t építenél

### Mikor NE használd

- **Production app adatbázis** - lassú API (rate limit: 5 req/s), nincs tranzakció, nincs RLS. Használj inkább [[database/supabase|Supabase]]-t vagy PostgreSQL-t
- **Sok adat (100K+ sor)** - Airtable limitek: 125K sor/bázis (Pro), lassul felette
- **Komplex relációk** - max 1 szint mélységű link, nincs JOIN, nincs nested query
- **Ha az ügyfél nem szerkeszt** - ha csak te használod, egy igazi DB jobb

---

## Árazás

| Csomag | Ár | Limit |
|--------|-----|-------|
| **Free** | $0 | 1000 sor/bázis, 1 GB attachment |
| **Team** | $20/hó/user | 50K sor/bázis, 20 GB |
| **Business** | $45/hó/user | 125K sor/bázis, 100 GB |
| **Enterprise** | Egyedi | Korlátlan, SSO, audit |

---

## AI-val való használat

Airtable API-t [[toolbox/claude-code-projekt-setup|Claude Code]]-dal könnyen integrálod: megadod a base ID-t és a tábla nevét, és az AI megírja a fetch/create/update hívásokat. Az [[toolbox/n8n|n8n]]-ben is van beépített Airtable node, workflow automatizáláshoz.

> [!tip] Hogyan használd promptban
> - *"Írj egy Next.js API route-ot ami az Airtable 'Projektek' táblából lekéri az aktív rekordokat és cacheli 5 percre"*
> - *"Készíts n8n workflow-t ami webhook-ra Airtable-be ír új sort"*

---

## Alternatívák

| Tool | Erőssége | Gyengesége |
|------|----------|------------|
| **Airtable** | Legjobb DX non-tech usereknek, API | Drága, rate limit, sor limit |
| **Notion databases** | Jobb jegyzetelés, olcsóbb | Lassabb API, kevesebb nézet |
| **Google Sheets** | Ingyenes, mindenki ismeri | Nincs igazi relációs adat, nincs API-first |
| **NocoDB** | Open-source Airtable klón | Kevesebb integráció, self-hosted |
