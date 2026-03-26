---
tags:
  - ai
  - ide
  - eszkoz
datum: 2026-03-26
szint: "🧱 Brick"
kapcsolodo:
  - "[[toolbox/claude-code-projekt-setup|Claude Code]]"
  - "[[toolbox/claude-code-agent-teams|Claude Code Agent Teams]]"
  - "[[toolbox/tmux|tmux]]"
---

# Collaborator - végtelen vászon IDE

**URL:** https://github.com/collaborator-ai/collab-public
**Stars:** ~860
**Platform:** macOS (Apple Silicon)

> [!tldr] Egy mondatban
> Végtelen vászon alapú fejlesztői környezet, ahol terminálok, kódfájlok és futó AI ágensek egymás mellett élnek - nincs tab-váltogatás, minden egy helyen.

---

## Mi ez és mire jó?

A Collaborator egy natív macOS desktop app (Electron), ami egy **infinite canvas**-ra helyezi a fejlesztési workflow-t. A fő ötlet: ahelyett, hogy IDE + terminál + böngésző + fájlkezelő között ugrálsz, minden egyszerre látható és rendezhető egy vizuális felületen.

| Tile típus | Mire jó |
|---|---|
| **Terminal** | Dupla klikk a vásznon -> új terminál ([[toolbox/tmux|tmux]] session-nel, tehát perzisztens) |
| **Note** | Markdown fájl drag és drop -> rich editor inline szerkesztéssel |
| **Code** | Bármilyen kódfájl -> szintaxis kiemeléssel, Monaco editor |
| **Image** | Kép fájl -> olvasható megjelenítés |

A tile-ok szabadon mozgathatók, átméretezhetők, z-index-szel rétegezhetők. Az egész vászon panelelhető és zoomolható.

---

## Mikor hasznos?

- **Multi-agent session-ök vizualizálása** - ha 3-4 [[toolbox/claude-code-projekt-setup|Claude Code]] terminált futtatsz párhuzamosan (pl. [[toolbox/claude-code-agent-teams|Agent Teams]]-szel), a Collaborator-ban egymás mellett látod őket kontextus-fájlokkal
- **Komplex projekt overview** - architektúra diagram, README, terminál, és kód egyszerre a képernyőn
- **Térbeli gondolkodás** - ha az elrendezés segít az átlátásban (nem mindenkinek jön be)

### Mikor NE használd

- Ha a terminál + editor (VSCode / Cursor) elegendő - a Collaborator extra layer, ami overkill lehet egyszerű feladatokra
- Linux / Windows - egyelőre **csak macOS Apple Silicon**
- Ha a tmux + többablakos terminál workflow-d kialakult, a Collaborator nem ad hozzá annál többet

---

## Alternatívák

| | Collaborator | Cursor / VSCode | [[toolbox/tmux|tmux]] + terminál |
|---|---|---|---|
| **Vizuális elrendezés** | Végtelen vászon | Tab/split | Panel grid |
| **AI integráció** | Bármilyen CLI agent | Beépített (Cursor) | Bármilyen CLI agent |
| **Perzisztencia** | tmux session-ök | Editor state | tmux session-ök |
| **Ár** | Ingyenes | Ingyenes / fizetős | Ingyenes |

---

## AI-natív fejlesztés

A Collaborator az AI-natív fejlesztés vizuális rétege: amikor több Claude Code session-t futtatsz párhuzamosan (Agent Teams), a végtelen vászon segít átlátni melyik agent min dolgozik. A tile-alapú elrendezés lehetővé teszi, hogy a specifikációs fájlok (PRD, Architecture, API spec) a terminálok mellett legyenek.

> [!tip] Hogyan használd AI-val
> - *"Nyiss 3 terminált a Collaborator vásznon: @backend, @frontend, @tests - mindegyik a saját subagent feladatát kapja"*
> - *"Rendezd a vásznon: bal oldalon a spec fájlok (PRD.md, API_spec.md), jobb oldalon a futó Claude Code session-ök"*

---

## Kapcsolódó

- [[toolbox/claude-code-projekt-setup|Claude Code]] - a fő AI agent ami a terminálokban fut
- [[toolbox/claude-code-agent-teams|Claude Code Agent Teams]] - párhuzamos agent session-ök, amiket a Collaborator vizualizálhat
- [[toolbox/tmux|tmux]] - a terminál session-ök amikre a Collaborator épül
