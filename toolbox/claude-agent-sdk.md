---
tags:
  - eszkoz
  - ai
  - sdk
datum: 2026-03-06
szint: "🧱 Scout"
kapcsolodo:
  - "[[cloud/docker-alapok|Docker alapok]]"
  - "[[cloud/kubernetes-bevezeto|Kubernetes bevezeto]]"
  - "[[toolbox/claude-code-agent-teams|Claude Code Agent Teams]]"
  - "[[toolbox/mcp-model-context-protocol|MCP — Model Context Protocol]]"
  - "[[_moc/moc-ai-tooling|MOC - AI Tooling]]"
---

## Mi a Claude Agent SDK es miert jobb mint kezzel megirni

A lenyeg: **te definiálod a tool-okat (MCP-n keresztül), Claude pedig eldönti mikor, mit, milyen sorrendben hívjon**.
Nem kell `if/else` logikát írnod az orchestráláshoz – az LLM az agent loop.

```
Kézzel írt orchestrator:          Claude Agent SDK:
─────────────────────             ─────────────────────
if (user wants X)                 "Csináld meg X-et"
  → call Clerk SDK                   ↓
  → then call Stripe SDK         Claude maga eldönti:
  → then call Resend SDK           → melyik tool kell
  → handle errors                  → milyen sorrendben
  → handle edge cases              → retry ha kell
                                   → kérdez ha kétértelmű
```
## Telepítés

```bash
# TypeScript
npm install @anthropic-ai/claude-agent-sdk

# Python
pip install claude-agent-sdk
```

**Anthropic API key**  kell (nem claude.ai login, hanem API key).

## Alap működés – TypeScript

```ts
import { query } from "@anthropic-ai/claude-agent-sdk"

// Legegyszerűbb: Claude kap egy promptot, és csinál amit kell
for await (const message of query({
  prompt: "List the files in the current directory",
  options: {
    allowedTools: ["Read", "Bash", "Glob"],  // built-in tools
    permissionMode: "acceptEdits"
  }
})) {
  if (message.type === "assistant") {
    for (const block of message.content) {
      if (block.type === "text") console.log(block.text)
    }
  }
}
```

Claude megkapja a tool-okat, és ő hívja amit relevánsnak lát – te nem mondod meg neki, hogy "most hívd a Bash-t".

## Mikor NE használd
- **Egyszerű, fix flow-k** – ha mindig A→B→C, az egy sima függvényhívás, nem kell AI
- **Költségérzékeny** – minden agent turn API hívás, ami pénzbe kerül
- **Latency-kritikus** – egy LLM hívás 1-5 sec, kézi kód ms-ek
- **Determinisztikus kell** – ha mindig pontosan ugyanazt kell csinálnia, ne bízd AI-ra

## MCP - SDK kapcsolat

```
Claude Agent SDK
  └── MCP server (interfész Claude felé)
        └── Te bent hívod az SDK-kat (implementáció)
```

**Az MCP server nem helyettesíti a szolgáltatás SDK-kat.** Az MCP a közvetítő nyelv Claude és a te kódod között. A te kódod bent továbbra is azt az SDK-t használja amit akar – Stripe, Supabase, Resend, bármi.

Gondolj rá úgy:

- **MCP** = Claude számára érthető menükártya ("ezek a tool-ok amiket hívsz")
- **SDK-k** = a konyha, ahol a munka ténylegesen történik

## Kapcsolódó

- [[cloud/docker-alapok|Docker alapok]] — agent-eket Docker konténerben futtatva izolálhatod
- [[cloud/kubernetes-bevezeto|Kubernetes bevezeto]] — skálázás: több agent példány orchestrálása klaszterben
- [[toolbox/claude-code-agent-teams|Claude Code Agent Teams]] — CLI-s agent koordináció (az SDK a programmatic megoldás, az Agent Teams a CLI-s interaktív)
