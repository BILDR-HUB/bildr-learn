---
tags:
  - eszkoz
  - ai
datum: 2026-03-26
szint: "🌱 Newcomer"
kapcsolodo:
  - "[[toolbox/claude-code-projekt-setup|Claude Code]]"
  - "[[_moc/moc-ai-tooling|MOC - AI Tooling]]"
---

# Gemini

**Kategória:** `AI` / `LLM`
**URL:** https://gemini.google.com

---

## Mi ez és mire jó?

Google multimodális AI modellje - szöveg, kép, kód, hang és videó megértése és generálása, hatalmas kontextus ablakkal (1-2M token).

A **Gemini** a Google DeepMind AI modellcsaládja. A fő erőssége a **multimodalitás** (szöveg + kép + videó + hang egyetlen modellben), a **nagy kontextus ablak** (2M token a legnagyobb modelleknél), és a Google ökoszisztéma integráció (Search, Docs, Gmail).

Három fő modell tier van: **Flash** (gyors és olcsó, hétköznapi feladatokra), **Pro** (kiegyensúlyozott, komplex feladatokra), és **Ultra/Advanced** (legokosabb, nehéz reasoning feladatokra).

---

## Mikor hasznos?

- **Hosszú dokumentumok elemzése** - 1-2M token context: teljes könyveket, kódbázisokat, long PDF-eket tud feldolgozni
- **Multimodális feladatok** - képek, videók, diagramok megértése és elemzése szöveggel együtt
- **Google Workspace integráció** - Gmail, Docs, Sheets-ben natívan elérhető
- **ML/AI research kérdések** - paper-ek összefoglalása, architektúrák összehasonlítása
- **Gyors prototípus kód** - Gemini Code Assist IDE-integrációval

### Mikor NE használd

- **Agentic kódolásra** - [[toolbox/claude-code-projekt-setup|Claude Code]] jobb a fájlrendszer-tudatos, multi-step kódolási feladatokra
- **Ha privacy kritikus** - Google training data policy-ja kevésbé átlátható mint más providereké
- **Konzisztens API output** - Claude/GPT API-k stabilabb structured output-ot adnak production-ben

---

## Modell összehasonlítás

| Szempont | Gemini Flash | Gemini Pro | Claude Opus | GPT-4o |
|----------|-------------|------------|-------------|--------|
| **Sebesség** | Nagyon gyors | Közepes | Lassabb | Közepes |
| **Context** | 1M token | 2M token | 200K/1M | 128K |
| **Ár (API)** | Nagyon olcsó | Közepes | Drágább | Közepes |
| **Kódolás** | Jó | Nagyon jó | Legjobb | Nagyon jó |
| **Multimodal** | Kép + video | Kép + video | Kép | Kép + audio |
| **Reasoning** | Alap | Erős | Legerősebb | Erős |

---

## Claude Code kontextus

A Gemini és a [[toolbox/claude-code-projekt-setup|Claude Code]] különböző feladatokra optimális. Claude Code az agentic kódolásban (fájlok olvasása, szerkesztése, CLI parancsok futtatása) erősebb, míg Gemini a multimodális és hosszú kontextusú elemzésben.

> [!tip] Hogyan használd promptban
> - *"Használj Gemini-t ennek a 200 oldalas PDF-nek az összefoglalásához, majd a lényeget add át Claude Code-nak a kód implementációhoz"*
> - *"Gemini-vel nézd át ezt a képernyőképet és írd le mit látsz, majd Claude Code-dal implementáld"*

---

## Alternatívák

| Tool | Erőssége | Gyengesége |
|------|----------|------------|
| **Gemini** | Multimodal, nagy context, Google integráció | Kódolásban gyengébb mint Claude |
| **Claude** | Legjobb kódolás, agentic workflow | Kisebb context (de 1M elérhető) |
| **GPT-4o** | Jó all-rounder, nagy ökoszisztéma | Drágább, kisebb context |
| **Llama** | Open-source, self-hosted | Kisebb capability |
