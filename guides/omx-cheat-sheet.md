---
tags:
  - ai
  - workflow
  - agentic-coding
datum: 2026-04-08
szint: "🧱 Brick"
kapcsolodo:
  - "[[github/oh-my-codex|OMX]]"
  - "[[github/oh-my-claudecode|OMC]]"
  - "[[github/claw-code|Claw Code]]"
  - "[[toolbox/tmux|tmux]]"
  - "[[github/get-shit-done|GSD]]"
---

# OMX Cheat Sheet

> [!tldr]
> Az [[github/oh-my-codex|OMX]] (és [[github/oh-my-claudecode|OMC]]) 4 fő parancsának használati útmutatója — mikor melyiket válaszd, milyen kombinációkban, és hogyan vigyél végig egy teljes projektet velük.

---

## Alapindítás

| Mód | Parancs | Mikor |
|-----|---------|-------|
| **Biztonságos default** | `omx --high` | Normál munka |
| **Ne kérdezzen sokat** | `omx --madmax --high` | Napi rutin — Claude-szerű autonómia |
| **Nagyon nehéz task** | `omx --madmax --xhigh` | Komplex refactor, nagy feature |

---

## A 4 fő OMX eszköz

| Eszköz | Mikor használd | Egy mondat |
|--------|---------------|------------|
| `$deep-interview` | Homályos scope | Socratic clarification — kérdez, amíg nem kristálytiszta |
| `$ralplan` | Approved terv kell | Implementációs terv tradeoff-okkal és kockázatokkal |
| `$ralph` | Egy owner vigye végig | Végrehajtás + verifikáció, amíg kész nincs |
| `$team` | Párhuzamos munka | Több executor egyszerre, [[toolbox/tmux|tmux]] split-pane |

### Egyszerű döntési szabály

```
"Nem teljesen tiszta, mit kell építeni" → $deep-interview
"Legyen normális terv"                  → $ralplan
"Most már csináld végig"                → $ralph
"Osszuk szét több workerre"             → $team
```

---

## Napi cheat sheet — 3 szcenárió

### 1. Kis-közepes task

```bash
omx --madmax --high
```

```
$ralph "Implement the requested change, run the relevant checks, and stop only when the result is verified."
```

### 2. Új feature, de még nem teljesen tiszta

```bash
omx --madmax --high
```

```
$deep-interview "Clarify the requested feature, constraints, edge cases, and non-goals."
$ralplan "Turn the clarified scope into an implementation plan with tradeoffs, risks, and rollout order."
$ralph "Execute the approved plan end to end and verify the result."
```

### 3. Nagy feature, több jól szétválasztható rész

```bash
omx --madmax --high
```

```
$deep-interview "Clarify the feature and split it into independently executable workstreams."
$ralplan "Produce an approved implementation plan with explicit parallel workstreams."
$team 3:executor "Execute the approved plan in parallel. Keep integration risk low and verify each stream."
$ralph "Integrate remaining gaps, resolve inconsistencies, and finish verification."
```

---

## Teljes projekt workflow — 5 fázis

Nagyobb projekteknél (SaaS app, belső tool) nem egy prompttal mész végig, hanem 5 fázisban:

### 1. Discovery

```bash
cd /path/to/repo
omx --madmax --high
```

```
$deep-interview "Understand this product, current architecture, business goals, user flows, technical constraints, and the next highest-leverage milestone. Be concrete."
```

**Elvárt kimenet:**
- Mi a termék célja
- Mi a következő milestone
- Mik a fő modulok
- Hol vannak a kockázatok
- Javasolt fejlesztési sorrend

### 2. Terv jóváhagyása

```
$ralplan "Create an implementation plan for the next milestone. Include architecture decisions, affected modules, data model changes, API changes, UI surfaces, validation, testing, and rollout risks."
```

**Elvárt kimenet:**
- Approved milestone plan
- Szeletekre bontott implementáció
- Dependency order
- Verification checklist

### 3. Implementáció

Ha a milestone egyben tartható:

```
$ralph "Execute the approved milestone plan end to end. Do not stop at partial implementation. Verify all changed surfaces."
```

Ha jól bontható (pl. admin UI + backend + data model + tesztek):

```
$team 4:executor "Execute the approved milestone plan in parallel across backend, frontend, data, and verification workstreams."
```

### 4. Stabilizálás

```
$ralph "Review the integrated result for regressions, consistency issues, missing validation, and incomplete edge cases. Fix and verify everything."
```

### 5. Zárás

```
$ralph "Prepare this milestone for handoff: ensure the code is clean, checks are run, risks are documented, and the remaining follow-up items are explicit."
```

---

## Tipikus milestone-ok SaaS appnál

Egy SaaS jellegű appnál tipikusan így érdemes bontani a milestone-okat:

1. Auth és role model
2. Core domain entities
3. Dashboard és listing felületek
4. Create/edit flow
5. Filters/search
6. Billing vagy subscription
7. Analytics / reporting
8. Admin / ops tooling
9. Hardening: validation, permissions, logs, tests

Minden milestone-ra ugyanaz a minta:
```
$deep-interview "Clarify the milestone and success criteria."
$ralplan "Create the approved implementation plan."
$ralph "Execute it fully and verify."
```

---

## Mikor használj `$team`-et?

**Használd ha a munka tényleg szétválasztható:**
- Frontend dashboard
- Backend API
- Schema/migration
- Test + verification

```
$team 4:executor "Implement the approved search milestone in parallel across filters UI, search API, indexing/query logic, and verification."
```

**NE használd ha:**
- Előbb még sokat kell gondolkodni
- Nagyon csatolt a kód
- Egyetlen kritikus refactor van

Ilyenkor jobb:
```
$ralph "Refactor the pricing engine safely and verify no regressions."
```

---

## Session-szintű minták

### A. Meglévő appban új feature

```
$deep-interview "Clarify the new feature request in the context of the existing codebase."
$ralplan "Turn it into an implementation plan with minimal-risk rollout."
$ralph "Execute the plan end to end and verify."
```

### B. Bug + regresszió

```
$deep-interview "Clarify the bug, expected behavior, reproduction path, and likely fault domain."
$ralph "Investigate, fix the bug, add the smallest useful regression protection, and verify."
```

### C. Nagy refactor

```bash
omx --madmax --xhigh
```

```
$deep-interview "Clarify the refactor scope, invariants, and rollback constraints."
$ralplan "Create a staged refactor plan with safety boundaries and verification gates."
$ralph "Execute the refactor stage by stage and verify each stage."
```

---

## Ajánlott default rutin

Minden komolyabb session elején:

```bash
omx --madmax --high
```

Első prompt:
```
$deep-interview "Understand the current project state, the next milestone, and the safest execution plan before making changes."
```

Utána:
- Ha világos és közepes task → `$ralph`
- Ha sok a tradeoff → `$ralplan`
- Ha bontható nagy task → `$team`

---

## Kapcsolódó

- [[github/oh-my-codex|OMX]] — az OMX tool maga, telepítés és feature-ök
- [[github/oh-my-claudecode|OMC]] — testvérprojekt Claude Code-hoz
- [[github/claw-code|Claw Code]] — Rust CLI amit OMX-szel építettek
- [[toolbox/tmux|tmux]] — szükséges a team mode split-pane működéséhez
- [[github/get-shit-done|GSD]] — alternatív framework, project lifecycle fókusszal
