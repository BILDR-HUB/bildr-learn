---
tags:
  - eszkoz
  - ai
  - dev-tool
datum: 2026-03-04
szint: "🧱 Brick"
kapcsolodo:
  - "[[toolbox/tmux|tmux]]"
  - "[[toolbox/git-worktree-vs-branch|Git worktree vs branch]]"
  - "[[toolbox/claude-agent-sdk|Claude Agent SDK]]"
---

# Claude Code Agent Teams

**Kategória:** `dev tool` / `ai`
**Docs:** https://code.claude.com/docs/en/agent-teams

---

## Mi ez és mire jó?

> [!tldr] Egy mondatban
> Az Agent Teams több független Claude Code session-t koordinál párhuzamosan — egy **team lead** oszt ki feladatokat, a **teammate-ek** önállóan dolgoznak saját context window-ban, és közvetlen üzenetekkel kommunikálnak egymással.

Ez nem ugyanaz, mint a sima subagent (ami egyetlen session-ön belül fut és csak visszajelent). Az agent teams-ben minden teammate egy **teljes, független Claude Code instance** — saját context, saját tool hozzáférés, egymás közötti kommunikáció.

```
Subagent:                           Agent Teams:
─────────────────                   ──────────────────────────────
Main Claude                         Team Lead (te interaktálsz vele)
  └── subagent 1 → result              ├── @tier1-frontend (worktree)
  └── subagent 2 → result              ├── @tier2-backend  (worktree)
  └── subagent 3 → result              └── @tier3-tests    (worktree)
                                        ↕ üzenetek egymás között
Csak visszajelent.                  Koordinálnak, vitatkoznak, önállóak.
```

---

## Mikor használd?

### Agent Teams ideális esetei

- **Research + review párhuzamosan** — 3 reviewer más-más szempontból (security, performance, tests)
- **Új modulok / feature-ök** — minden teammate saját fájlkészleten dolgozik
- **Debugging versengő hipotézisekkel** — 5 agent 5 különböző elméletet vizsgál, egymás elméleteit cáfolják
- **Cross-layer koordináció** — frontend, backend, tesztek külön teammate-ek

### Mikor NE használd

- **Szekvenciális feladatokra** — ha A→B→C sorrendben kell, egy session elég
- **Ugyanazon fájl szerkesztése** — két teammate ugyanazt a fájlt írja → conflict
- **Kis, egyszerű feladatokra** — a koordinációs overhead nem éri meg
- **Költségérzékeny feladatoknál** — minden teammate saját context = lineárisan növekvő token költség

> [!warning] Token költség
> Az agent teams **lényegesen** több tokent használ mint egy sima session. Minden teammate egy különálló Claude instance saját context window-val. 3-5 teammate-nél érdemes elkezdeni, ne 10-zel.

---

## Bekapcsolás

Kísérleti funkció, alapból kikapcsolt. A `settings.json`-ba kell:

```json
{
  "env": {
    "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"
  }
}
```

---

## Használat

### Team indítása

Természetes nyelvvel mondd meg Claude-nak mit akarsz:

```
Create an agent team to refactor the auth system.
Spawn 3 teammates:
- One for the middleware refactor
- One for the API route updates
- One for writing integration tests
Use Sonnet for each teammate.
```

Claude létrehozza a team-et, spawn-olja a teammate-eket, és koordinálja a munkát.

### Teammate-ek közötti váltás

#### In-process mód (alapértelmezett)

Minden teammate a fő terminálban fut. Navigáció:

| Parancs | Leírás |
|---|---|
| `Shift+↓` | Következő teammate-re váltás (ciklikus) |
| `Enter` | Teammate session megtekintése |
| `Escape` | Teammate aktuális turn megszakítása |
| `Ctrl+T` | Task list megjelenítése |
| Gépelés | Közvetlen üzenet az aktív teammate-nek |

#### Split-pane mód (tmux / iTerm2)

Minden teammate **saját pane-t kap** — egyszerre látsz mindenkit. Ehhez kell:
- **tmux** (`brew install tmux`) — VAGY
- **iTerm2** az `it2` CLI-vel és Python API-val

Konfiguráció a `settings.json`-ban:

```json
{
  "teammateMode": "auto"   // "auto" | "in-process" | "tmux"
}
```

- `"auto"` — ha tmux session-ben vagy → split pane, egyébként in-process
- `"tmux"` — mindig split pane (tmux / iTerm2 auto-detect)
- `"in-process"` — mindig tab-váltós

Vagy session-szinten:

```bash
claude --teammate-mode tmux
```

> [!tip] cmux + tmux kombó
> A **cmux** (natív macOS terminál) NEM tmux-alapú — a Ghostty renderelő motort használja. Claude Code agent teams split-pane mód **nem működik közvetlenül cmux-ban**.
>
> **Workaround:** cmux-on belül indíts egy `tmux` session-t, és abban futtasd a `claude`-ot. Claude Code észleli a tmux-ot és automatikusan split pane-eket használ:
> ```bash
> # cmux tab-ban:
> tmux new-session -s team
> claude
> # > "Create an agent team..."
> ```

---

## Architektúra

```mermaid
graph TD
    TL[Team Lead<br/>fő Claude Code session] --> TK[Shared Task List<br/>~/.claude/tasks/team-name/]
    TL --> T1[@teammate-1<br/>saját context window]
    TL --> T2[@teammate-2<br/>saját context window]
    TL --> T3[@teammate-3<br/>saját context window]
    T1 --> TK
    T2 --> TK
    T3 --> TK
    T1 <-->|üzenetek| T2
    T2 <-->|üzenetek| T3
```

| Komponens | Szerep |
|---|---|
| **Team Lead** | Fő session, team létrehozása, feladat kiosztás, szintézis |
| **Teammates** | Független Claude Code instance-ok, saját context |
| **Task List** | Megosztott feladatlista (pending → in_progress → completed) |
| **Mailbox** | Üzenetrendszer az agentek közötti kommunikációhoz |

Fájlok helye:
- Team config: `~/.claude/teams/{team-name}/config.json`
- Task list: `~/.claude/tasks/{team-name}/`

---

## Task kezelés

A shared task list koordinálja a munkát. Feladatok állapotai: **pending** → **in_progress** → **completed**. Dependency-k is beállíthatók — egy blocked task nem claimelhető amíg a függősége nem kész.

```
Task #1: Refactor middleware        ← @frontend (in_progress)
Task #2: Update API routes          ← @backend (in_progress)
Task #3: Write integration tests    ← blocked by #1, #2 (pending)
Task #4: Code review                ← blocked by #1, #2, #3 (pending)
```

- **Lead assigns** — te mondod meg melyik task kinek
- **Self-claim** — teammate automatikusan felveszi a következő szabad, nem blokkolt taskot

> [!info] Task claiming
> File locking-ot használ race condition ellen — ha két teammate egyszerre akarja claimelni ugyanazt a taskot, csak az egyik kapja meg.

---

## Plan approval (opcionális)

Komplex vagy rizikós feladatoknál **kérhetsz plan jóváhagyást** — a teammate read-only plan módban dolgozik amíg a lead nem approve-olja:

```
Spawn an architect teammate to refactor the auth module.
Require plan approval before they make any changes.
```

A teammate tervez → lead review-olja → approve/reject → implementáció.

---

## Hooks minőségellenőrzéshez

A Claude Code hook rendszer integrálható:

- **`TeammateIdle`** — fut amikor egy teammate idle-be megy. Exit code 2 → feedback és folytatás
- **`TaskCompleted`** — fut amikor task completed-re vált. Exit code 2 → megakadályozza a befejezést és feedbacket küld

---

## Best practices

### Team méret

- **3-5 teammate** a legtöbb workflow-hoz
- **5-6 task per teammate** — elég munka hogy produktív legyen
- Ha 15 független task van → 3 teammate ideális

### Fájl konfliktusok elkerülése

Mindig úgy oszd el a munkát, hogy **minden teammate saját fájlkészleten** dolgozik. Két teammate ugyanazon a fájlon → felülírás.

### Kontextus a spawn prompt-ban

A teammate-ek **nem öröklik** a lead beszélgetés-történetét. A spawn prompt-ban adj meg minden szükséges kontextust:

```
Spawn a security reviewer with the prompt: "Review src/auth/ for
vulnerabilities. Focus on JWT token handling, session management.
The app uses httpOnly cookies with jose library."
```

---

## Leállítás és cleanup

```
# Egyedi teammate leállítása
Ask the researcher teammate to shut down

# Teljes team cleanup (lead-nek mondd!)
Clean up the team
```

> [!warning] Cleanup sorrendje
> Mindig a **lead**-nek mondd a cleanup-ot, ne a teammate-nek. Először minden teammate-et állíts le, aztán jöhet a team cleanup. Aktív teammate-ek mellett a cleanup fail-el.

---

## Korlátok

- **Nincs session resumption** in-process teammate-eknél — `/resume` nem állítja vissza őket
- **Task status lag** — teammate-ek néha elfelejtik completed-re állítani a taskot
- **Egy team per session** — nem lehet nested team
- **Lead fix** — nem lehet leadert váltani
- **Split pane** — csak tmux és iTerm2 (cmux, VS Code terminal, Ghostty nem támogatott)

---

## Subagent vs Agent Teams összehasonlítás

| Szempont | Subagent | Agent Teams |
|---|---|---|
| Context | Saját, result visszamegy a hívóhoz | Saját, teljesen független |
| Kommunikáció | Csak a fő agent felé | Egymással is közvetlenül |
| Koordináció | Fő agent kezeli | Shared task list, self-coordination |
| Ideális | Fókuszált feladatok, csak az eredmény kell | Komplex munka, vita, kollaboráció |
| Token költség | Alacsonyabb | Magasabb (minden teammate = Claude instance) |

---

## Hasznos use case-ek

### Parallel code review (3 reviewer)

```
Create an agent team to review PR #142. Spawn three reviewers:
- Security implications
- Performance impact
- Test coverage
Have them each review and report findings.
```

### Versengő hipotézisek debugging-hoz

```
Users report the app exits after one message. Spawn 5 teammates
to investigate different hypotheses. Have them talk to each other
to disprove each other's theories, like a scientific debate.
```

### Feature implementáció (3 tier)

```
Create an agent team for the pricing engine feature:
- @tier1: Pricing algorithm + DB schema
- @tier2: API endpoints + validation
- @tier3: Frontend components + tests
Each works in their own worktree.
```

---

## Kapcsolódó

- [[toolbox/tmux|tmux]] — split-pane módhoz szükséges, session-ök kezelése agent teams mellett
- [[toolbox/git-worktree-vs-branch|Git worktree vs branch]] — teammate-ek izolált worktree-kben dolgoznak
- [[toolbox/claude-agent-sdk|Claude Agent SDK]] — programmatic agent orchestration (ez a CLI-s megoldás, az SDK a kódba épített)
