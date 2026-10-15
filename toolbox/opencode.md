---
tags:
  - ai
  - coding-assistant
  - cli
  - open-source
  - developer-tool
datum: 2026-06-14
szint: "🌱 Newcomer"
kapcsolodo:
  - "[[toolbox/claude-code|Claude Code]]"
  - "[[toolbox/ai-coding-agentek-osszehasonlitasa|AI coding agentek összehasonlítása]]"
  - "[[github/graphify|Graphify]]"
---

# OpenCode

Az SST csapat nyílt forráskódú AI kódolási agense — CLI és asztali alkalmazás egyben.

> [!tldr] Egy mondatban
> Az OpenCode egy MIT-licenszes, open-source AI coding agent, ami Claude Code-hoz hasonlóan terminálból és asztali alkalmazásból is használható, de bármely LLM-mel (Claude, GPT, Gemini, Ollama) működik, és a forráskódja teljesen nyílt.

---

## Mi ez és mire jó?

Az **OpenCode** az [SST](https://sst.dev) csapat fejlesztése — ugyanazok, akik az SST infrastruktúra-keretrendszert is készítik. A tool két formában érhető el:

- **CLI** — terminálból futtatható, hasonló élmény mint a [[toolbox/claude-code|Claude Code]]
- **Desktop app** (béta) — GUI-val rendelkező asztali alkalmazás

Fő különbség a [[toolbox/claude-code|Claude Code]]-tól: **teljesen open-source** (MIT licensz), és **nem köt egy LLM-hez** — bármely providert bekonfigurálhatod.

---

## Két agent mód

Az OpenCode két beépített agent között vált (Tab-bal):

| Mód | Hozzáférés | Mikor használd |
|---|---|---|
| **build** | Teljes hozzáférés — fájlok, terminal, git | Aktív fejlesztés, kódírás, refaktorálás |
| **plan** | Read-only — fájlolvasás igen, szerkesztés nem | Kódbázis megismerése, tervezés, analízis |

> [!tip] Plan mód
> A `plan` agent parancsfuttatás előtt engedélyt kér. Biztonságos ismeretlen repókban való exploráláshoz.

---

## Telepítés

```bash
# curl (ajánlott, gyors)
curl -fsSL https://opencode.ai/install | bash

# npm
npm install -g opencode-ai

# Homebrew (macOS)
brew install sst/tap/opencode

# Scoop (Windows)
scoop bucket add sst https://github.com/sst/scoop-bucket
scoop install opencode

# Nix
nix profile install github:sst/opencode
```

Desktop alkalmazás: DMG (macOS), EXE (Windows), vagy Homebrew Cask.

---

## OpenCode vs Claude Code

| Szempont | OpenCode | [[toolbox/claude-code|Claude Code]] |
|---|---|---|
| Licensz | MIT (teljesen nyílt) | Proprietary (Anthropic) |
| Forráskód | Nyilvános, fork-olható | Bundled, nem olvasható |
| LLM provider | Bármely (Claude, GPT, Gemini, Ollama) | Csak Anthropic modellek |
| Desktop app | Igen (béta) | Nem (csak CLI) |
| Agent módok | build + plan (Tab-váltás) | Nincs beépített váltás |
| MCP támogatás | Igen | Igen |
| Nyelv (forrás) | TypeScript | TypeScript (bundled) |

---

## Mikor hasznos?

- **Open-source követelmény** — ha a csapat vagy a projekt nem enged proprietary toolokat
- **Model flexibilitás** — ha Ollama-val lokálisan, vagy más LLM-mel szeretnél dolgozni
- **Desktop UI** — ha nem akarsz terminálban dolgozni
- **Kódbázis megismerés** — `plan` módban biztonságos exploráció

### Mikor NE használd

- Ha a csapat már [[toolbox/claude-code|Claude Code]]-ban standardizált — felesleges párhuzamos tool bevezetése
- Ha Anthropic-specifikus funkciókat (pl. extended thinking, Projects) használsz — ezek nem elérhetők
- Production kritikus workflow-kban, amíg a desktop app béta stádiumban van

---

## AI-natív fejlesztés

```bash
# Kódbázis megismerése plan módban
opencode  # majd Tab → plan mód
# "Mutasd be az autentikációs folyamatot ebben a projektben"

# Aktív fejlesztés build módban
opencode  # build mód (alapértelmezett)
# "Refaktoráld a UserService osztályt — vezess be repository pattern-t"
```

> [!info] Lokális LLM
> Ollama-val kombinálva teljesen offline, cost-free AI coding assistant építhető — ideális tanuláshoz vagy privát projekteknél.

---

## Kapcsolódó

- [[toolbox/claude-code|Claude Code]] - Anthropic-féle alternatíva, mélyebb Anthropic-integráció
- [[toolbox/ai-coding-agentek-osszehasonlitasa|AI coding agentek összehasonlítása]] - szélesebb összehasonlítás
- [[github/graphify|Graphify]] - tudásgráf tool, OpenCode-dal is integrálható
