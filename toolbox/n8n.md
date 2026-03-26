---
tags:
  - eszkoz
  - automatizalas
datum: 2026-03-26
szint: "🧱 Scout"
kapcsolodo:
  - "[[cloud/docker-alapok|Docker]]"
  - "[[toolbox/claude-code-projekt-setup|Claude Code]]"
  - "[[cloud/cloudflare|Cloudflare]]"
---

# n8n

**Kategória:** `workflow automatizálás` / `integráció`
**URL:** https://n8n.io

---

## Mi ez és mire jó?

Self-hosted workflow automatizálási platform - Zapier/Make alternatíva, de a te szerveredről fut, kóddal bővíthető, és nincs per-execution díjazás.

Az **n8n** (ejtsd: "nodemation") egy vizuális workflow builder, ahol node-okat kötsz össze drag-and-drop módon. Minden node egy lépés: trigger (webhook, cron, email), feldolgozás (JavaScript/Python kód, IF/switch, loop), vagy akció (API hívás, DB írás, Slack üzenet).

A lényeges különbség a Zapier-hez képest: **self-hosted** opcióval a te infrastruktúrádon fut ([[cloud/docker-alapok|Docker]]), nincs task limit, és JavaScript/Python kódot is írhatsz a node-okba. Ez teszi alkalmassá komplex, dev-közeli automatizálásra.

400+ beépített integráció van (Google Sheets, Slack, PostgreSQL, Airtable, OpenAI, stb.), és bármelyik REST API-t eléred HTTP Request node-dal.

---

## Mikor hasznos?

- **Backend workflow-ok automatizálása** - email küldés user regisztrációnál, webhook feldolgozás, adatszinkron rendszerek között
- **AI pipeline-ok** - OpenAI/Claude API hívás + feldolgozás + eredmény mentése
- **Cron job-ok vizuálisan** - rendszeres adatgyűjtés, report generálás, cleanup taskok
- **Integráció bridge** - két rendszer összekötése (pl. form beküldés - CRM - email)
- **Prototípus automatizálás** - gyorsabban összerakni mint kézzel kódolni

### Mikor NE használd

- **Egyszerű cron job** - ha egy scriptet kell futtatni időzítve, elég egy sima cron, nem kell n8n
- **Real-time, alacsony latency** - n8n nem real-time event processor, queue-ra alkalmasabb
- **Ha nincs szervered** - self-hosted verziónál kell infra; ha nem akarsz hostolni, a cloud verzió drágább mint a Zapier

---

## Árazás

| Verzió | Ár | Mit ad |
|--------|-----|--------|
| **Community (self-hosted)** | Ingyenes | Korlátlan workflow, korlátlan execution, Docker-ben fut |
| **Cloud Starter** | EUR24/hó | 2500 execution/hó, 5 workflow |
| **Cloud Pro** | EUR60/hó | 10K execution/hó, korlátlan workflow |
| **Enterprise** | Egyedi | SSO, audit log, dedikált support |

> [!tip] A self-hosted verzió tényleg ingyenes és korlátlan - ez az n8n legnagyobb előnye a Zapier/Make felett.

---

## Claude Code kontextus

Az n8n és a [[toolbox/claude-code-projekt-setup|Claude Code]] jól kiegészítik egymást: Claude Code megírja a kódot és az API-kat, n8n pedig összeköti őket automatizált workflow-kba. Az n8n Code node-jában JavaScript/Python kódot futtathatsz - ezt is Claude Code-dal írathatod.

> [!tip] Hogyan használd promptban
> - *"Írj egy n8n workflow JSON-t ami webhook trigger-re OpenAI API-t hív, és az eredményt PostgreSQL-be menti"*
> - *"Készíts [[cloud/docker-compose|Docker Compose]]-t ami n8n-t futtat PostgreSQL backend-del és Traefik reverse proxy-val"*

---

## Alternatívák

| Tool | Erőssége | Gyengesége |
|------|----------|------------|
| **n8n** | Self-hosted, korlátlan, kóddal bővíthető | Kell hozzá szerver/Docker |
| **Zapier** | Legegyszerűbb, legtöbb integráció | Drága, per-task árazás |
| **Make (Integromat)** | Vizuálisan erős, jó ár/érték | Nem self-hosted |
| **Temporal** | Production-grade orchestration | Fejlesztői tool, nem no-code |
