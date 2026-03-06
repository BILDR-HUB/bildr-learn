---
tags:
  - eszkoz
  - setup
  - environment
datum: 2026-03-06
szint: "🌱 Newcomer"
kapcsolodo:
  - "[[_moc/moc-environment-setup|MOC - Environment Setup]]"
---

# Uj Mac setup — fejlesztoi kornyezet

Checklist es utmutato uj Mac beallitasahoz. Ezt kovesd sorban.

---

## 1. Homebrew

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Apple Silicon után add hozzá a PATH-hoz (az installer mondja meg):
```bash
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zshrc && source ~/.zshrc
```

---

## 2. Claude Code

```bash
brew install claude
claude --version  # ellenőrzés
```

> [!warning] Ne használj npm-es telepítést
> A Homebrew natív verzió az ajánlott. Ne futtasd az `npm install -g @anthropic-ai/claude-code` parancsot mellé — ütközés lesz.

---

## 3. Projektek mappája

```bash
mkdir ~/Projects
```

**Fontos:** A `~/Projects` mappa **nincs iCloud-ban** — ez szándékos. Kód projektek ne legyenek iCloud-ban (lassú szinkron, `node_modules` probléma).

Projektek visszaállítása:
```bash
cd ~/Projects
git clone git@github.com:user/projekt-neve.git
# ismételd minden projektre
```

> [!tip] Ha nincs minden Git-ben
> Régi gépről Time Machine backup-ból vagy manuálisan másold át a `~/Projects` mappát.

---

## 4. Claude Code sessionök és memória

A `~/.claude/` mappa tartalmazza:
- **Sessionök** (`~/.claude/projects/`) — projekt-specifikus chat history
- **Globális memória** (`~/.claude/MEMORY.md`)
- **Beállítások** (`~/.claude/settings.json`)
- **Skills** (`~/.claude/skills/`)

Régi gépről átmásolás:
```bash
# Régi gépen: mentés
cp -r ~/.claude/ ~/Projects/claude-backup/

# Új gépen: visszaállítás
cp -r ~/Projects/claude-backup/ ~/.claude/
```

> [!info] Session path-ok
> A sessionök path alapján vannak indexelve. Ha a projektek ugyanott vannak (`~/Projects/`), minden session automatikusan felismeri őket.

---

## 5. Antigravity

1. Töltsd le és telepítsd az appot
2. Konfig visszaállítása régi gépről:

```bash
# Régi gépen
cp -r ~/Library/Application\ Support/Antigravity/ ~/Projects/antigravity-backup/

# Új gépen
cp -r ~/Projects/antigravity-backup/ ~/Library/Application\ Support/Antigravity/
```

> [!warning] Workspace path-ok
> Ha a projektek ugyanazon az útvonalon vannak (`~/Projects/`), a workspace-ek automatikusan működnek. Ha nem, a `~/Library/Application Support/Antigravity/User/workspaceStorage/` mappában lévő `workspace.json` és `state.vscdb` fájlokban kell a path-okat frissíteni.

---

## 6. Dotfiles

Ha van dotfiles repo:

```bash
cd ~/Projects && git clone git@github.com:user/dotfiles.git
cp dotfiles/.zshrc ~/.zshrc
cp dotfiles/.gitconfig ~/.gitconfig
source ~/.zshrc
```

Ha nincs ilyen repo még, hozd létre:
```bash
mkdir ~/Projects/dotfiles
cp ~/.zshrc ~/Projects/dotfiles/
cp ~/.gitconfig ~/Projects/dotfiles/
cd ~/Projects/dotfiles && git init
```

---

## 7. Homebrew bundle (appok visszaállítása)

Régi gépen mentés:
```bash
brew bundle dump --file=~/Projects/dotfiles/Brewfile
```

Új gépen visszaállítás:
```bash
brew bundle install --file=~/Projects/dotfiles/Brewfile
```

---

## 8. iCloud beállítások

> [!danger] Kapcsold ki az "Optimize Mac Storage"-t
> **System Settings → Apple ID → iCloud → iCloud Drive → Optimize Mac Storage → KI**
>
> Ha be van kapcsolva, az iCloud evikálja a ritkán használt fájlokat, és a fejlesztési fájlok elérhetetlenné válnak.

`.gitignore` minden projektbe — hogy `node_modules` véletlenül se kerüljön iCloud-ba:
```bash
find ~/Projects -name ".gitignore" | xargs grep -L "node_modules" | while read f; do
  echo "node_modules/" >> "$f"
done
```

---

## 9. Git SSH kulcs

```bash
ssh-keygen -t ed25519 -C "user@email.com"
cat ~/.ssh/id_ed25519.pub
# Másold be GitHub → Settings → SSH Keys
```

---

## Ellenőrző lista

- [ ] Homebrew telepítve
- [ ] Claude Code telepítve (`brew install claude`)
- [ ] `~/Projects` mappa létrehozva
- [ ] Projektek klónozva / visszamásolva
- [ ] `~/.claude/` visszaállítva
- [ ] Antigravity telepítve és konfig visszaállítva
- [ ] Dotfiles visszaállítva
- [ ] Brewfile alapján appok telepítve
- [ ] iCloud "Optimize Mac Storage" kikapcsolva
- [ ] SSH kulcs generálva és GitHub-ra feltöltve
