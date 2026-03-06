---
tags:
  - eszkoz
  - terminal
  - multiplexer
datum: 2026-03-06
szint: "🌱 Newcomer"
kapcsolodo:
  - "[[foundations/bash-es-linux-parancssor|Bash es Linux parancssor]]"
  - "[[toolbox/tailscale|Tailscale]]"
  - "[[toolbox/claude-agent-sdk|Claude Agent SDK]]"
  - "[[toolbox/claude-code-agent-teams|Claude Code Agent Teams]]"
---

# tmux

**Kategória:** `dev tool` / `terminal`
**URL:** https://github.com/tmux/tmux
**Ár/Terv:** Ingyenes, open source

---

## Mi ez és mire jó?

> [!tldr] Egy mondatban
> A tmux egy **terminál multiplexer** — egyetlen terminálablakban több session-t, window-t és pane-t kezelsz egyszerre, és a session-ök a terminál bezárása után is életben maradnak.

A normál terminálban ha bezárod az ablakot, minden meghal. A tmux-szal ez nem probléma: a session a háttérben fut, bármikor visszacsatolhatsz hozzá.

### A Claude Code kontextus

Ha Claude Code-dal dolgozol, a tmux nem csak kényelmi eszköz — **alapvető szükséglet**:

- A Claude Code hosszú feladatokat futtat (kódgenerálás, refaktorálás, tesztek) — nem akarsz mellette ülni
- Több projektet egyszerre vezetsz — minden projektnek saját session
- A terminál bezárása vagy VPN-dropout **nem szakítja meg** a Claude Code futást
- Párhuzamos context: Claude Code fut az egyik pane-ben, `git log` / `tail -f` a másikban

```
Tmux nélkül:                    Tmux-szal:
──────────────────────          ──────────────────────────────────
Terminal 1: claude              session: work
Terminal 2: git log               window 1: claude (fut a háttérben)
Terminal 3: npm run dev           window 2: git + logs
Ha bezárod → minden meghal        window 3: npm dev server

                                session: private-project
                                  window 1: claude
                                  window 2: editor commands
```

---

## Setup — lépésről lépésre

### 1. Telepítés

```bash
brew install tmux
```

### 2. Alap konfiguráció (~/.tmux.conf)

A tmux alapból `Ctrl+b` prefixet használ. Érdemes a prefix-et `Ctrl+a`-ra váltani (kevésbé kell nyúlni), és bekapcsolni az egérkezelést:

```bash
# ~/.tmux.conf

# Prefix: Ctrl+a (ergonomikusabb mint Ctrl+b)
unbind C-b
set-option -g prefix C-a
bind-key C-a send-prefix

# Egérkezelés: kattintással válts pane-t, görgess
set -g mouse on

# Pane split: | = vízszintes, - = függőleges (intuitívabb)
bind | split-window -h
bind - split-window -v
unbind '"'
unbind %

# Ablak számozása 1-től (nem 0-tól)
set -g base-index 1
setw -g pane-base-index 1

# Megnövelt scroll history
set -g history-limit 50000

# Gyors panel navigáció Alt+nyíl
bind -n M-Left select-pane -L
bind -n M-Right select-pane -R
bind -n M-Up select-pane -U
bind -n M-Down select-pane -D

# Megjelenés: status bar
set -g status-bg colour234
set -g status-fg white
set -g status-left '[#S] '
set -g status-right '%H:%M %d-%m'
```

Konfig betöltése futó session-ben:
```bash
tmux source-file ~/.tmux.conf
```

### 3. Session setup Claude Code-hoz

```bash
# Új session indítása projekt névvel
tmux new-session -s myproject

# Belül: Claude Code indítása
claude

# Új window megnyitása (Ctrl+a c) és logs nézés
tail -f /var/log/app.log

# Visszaváltás Claude Code-ra (Ctrl+a 1)
```

---

## Claude Code workflow-k tmux-szal

### Workflow 1: Hosszú feladat + detach

Ez a legfontosabb pattern. Claude Code elindít valamit ami percekig fut:

```bash
# Session indítása
tmux new-session -s myproject

# Claude Code-ban elindítasz egy hosszú refaktorálást
claude
# > "Írj teszteket az összes API endpoint-hoz"

# Miközben Claude dolgozik, lecsatolod a session-t
# Ctrl+a d  (detach)

# Mész csinálsz mást, kávét iszol, stb.
# Visszacsatolás amikor kész:
tmux attach -t myproject
```

> [!tip] Claude Code + detach
> A `Ctrl+a d` után a Claude Code session teljesen életben marad. Nem kell várni mellette — a feladat fut a háttérben. Ez különösen hasznos nagy kódbázis-elemzésnél vagy hosszú tesztfutásnál.

### Workflow 2: Több projekt párhuzamosan

```bash
# Session per projekt
tmux new-session -d -s project-1      # -d = detached indítás (nem ugrik rá)
tmux new-session -d -s project-2
tmux new-session -d -s project-3

# Listázás
tmux ls
# project-1: 2 windows
# project-2: 1 window
# project-3: 1 window

# Átváltás session-ök között
tmux switch-client -t project-1
# vagy: Ctrl+a s  (interaktív lista)
```

### Workflow 3: Split pane — Claude Code + monitoring

```bash
# Pane felosztás: Claude Code bal, log jobb
tmux split-window -h    # vagy Ctrl+a |

# Bal pane: claude fut
# Jobb pane:
git log --oneline -20
# vagy:
npm run dev
# vagy:
watch -n2 'git status'
```

```
┌──────────────────────┬───────────────────────┐
│                      │                       │
│  claude              │  git log --oneline    │
│                      │                       │
│  > Refaktorálom az   │  a3f1b2c fix auth    │
│    auth middleware-t │  d4e5f6a add tests   │
│    ...               │  7b8c9d0 init        │
│                      │                       │
└──────────────────────┴───────────────────────┘
```

### Workflow 4: Worktree + tmux kombó

A Claude Code worktree-ket hoz létre párhuzamos fejlesztéshez. Minden worktree-nek saját tmux window:

```bash
# Claude Code worktree-t csinál:
# .claude/worktrees/feature-auth/

# Külön window a worktree-hez
tmux new-window -n auth-feature
cd .claude/worktrees/feature-auth
claude

# Visszaváltás a fő window-ra (Ctrl+a 1)
# A két claude session egymástól függetlenül fut
```

> [!info] Miért hasznos?
> Ha Claude Code `isolation: "worktree"` módban futtat egy agent-et, az egy teljesen izolált git másolaton dolgozik. A tmux window-ok segítenek átlátni melyik window melyik worktree-t kezeli.

---

## Legfontosabb billentyűparancsok

> [!note] Prefix
> Az alábbi parancsok mind `Ctrl+a`-val kezdődnek (ha az ajánlott konfigot használod), az alapértelmezett `Ctrl+b` helyett.

### Session kezelés

| Parancs | Leírás |
|---|---|
| `Ctrl+a d` | Detach — lecsatolás, session fut tovább |
| `Ctrl+a s` | Session lista (interaktív) |
| `Ctrl+a $` | Session átnevezése |
| `tmux ls` | Minden session listázása (terminálból) |
| `tmux attach -t <név>` | Visszacsatolás session-höz |
| `tmux new -s <név>` | Új session létrehozása |
| `tmux kill-session -t <név>` | Session törlése |

### Window (tab) kezelés

| Parancs | Leírás |
|---|---|
| `Ctrl+a c` | Új window |
| `Ctrl+a ,` | Window átnevezése |
| `Ctrl+a 1-9` | Ugrás adott window-ra |
| `Ctrl+a n / p` | Következő / előző window |
| `Ctrl+a w` | Window lista (interaktív) |

### Pane (split) kezelés

| Parancs | Leírás |
|---|---|
| `Ctrl+a \|` | Függőleges split (bal/jobb) |
| `Ctrl+a -` | Vízszintes split (fel/le) |
| `Alt+nyíl` | Pane váltás nyíllal |
| `Ctrl+a z` | Pane zoom (teljes képernyő toggle) |
| `Ctrl+a x` | Pane bezárása |

### Görgetés (Claude output olvasásához)

| Parancs | Leírás |
|---|---|
| `Ctrl+a [` | Scroll mode belépés |
| `PgUp / PgDn` | Görgetés |
| `q` | Kilépés scroll mode-ból |

> [!tip] Scroll mode Claude Code-hoz
> Ha Claude Code hosszú outputot ír (pl. egy nagy refaktorálás eredménye), a `Ctrl+a [` scroll mode-ban tudsz visszagörgetni és elolvasni. Ez a normál terminálnál sokkal tovább megy vissza (`history-limit 50000` beállítással).

---

## Buktatók és hibák amiket elkerülj

- **Ne `exit`-elj ha detach-elni akarsz** — az `exit` megszünteti a session-t. Mindig `Ctrl+a d` (detach)
- **Session névütközés** — `tmux new -s myproject` hibát dob ha már van ilyen nevű session. Előbb `tmux ls`
- **Copy-paste macOS-en** — a tmux scroll mode-ban `Ctrl+a [` után a szöveg kijelölés Space-szel történik, nem egérrel (hacsak `set -g mouse on` nincs). Egér módban Shift+kattintás az OS clipboard-ot használja
- **Nested tmux** (tmux a tmux-ban) — ha SSH-n csatlakozol egy szerverhez ami szintén tmux-ot futtat, a prefix parancsok összekeverednek. Kétszeres prefix (`Ctrl+a Ctrl+a`) küldi a belső session-nek

---

## Hasznos parancsok / kódrészletek

```bash
# Claude Code projekt indítása tmux-szal (egysoros)
tmux new-session -s myproject -c ~/Projects/myproject \; send-keys 'claude' Enter

# Létező session-höz csatolás, ha nem létezik, létrehozza
tmux new-session -A -s work

# Minden session listázása
tmux ls

# Adott session window-jainak listázása
tmux list-windows -t myproject

# Session küldése háttérbe (nem interaktív session)
tmux new-session -d -s build \; send-keys -t build 'npm run build' Enter

# Session tartalmának mentése fájlba (debug)
tmux capture-pane -t myproject -p > /tmp/claude-output.txt
```

---

## Hasznos linkek

- Docs: https://github.com/tmux/tmux/wiki
- Cheatsheet: https://tmuxcheatsheet.com
- tmux Plugin Manager: https://github.com/tmux-plugins/tpm
- tmux Resurrect (session mentés reboot után): https://github.com/tmux-plugins/tmux-resurrect

---

## Kapcsolódó

- [[foundations/bash-es-linux-parancssor|Bash es Linux parancssor]] — terminál alapok, amelyeket tmux-on belül használsz
- [[toolbox/tailscale|Tailscale]] — ha SSH-n keresztül csatlakozol egy VPS-re, tmux-szal a session ott is életben marad kapcsolatszakadás után
- [[toolbox/claude-agent-sdk|Claude Agent SDK]] — hosszú futású agent workflow-k tmux session-ben
- [[toolbox/claude-code-agent-teams|Claude Code Agent Teams]] — tmux szükséges a split-pane módhoz, ahol minden teammate saját pane-t kap
