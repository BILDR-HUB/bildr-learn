---
tags:
  - eszkoz
  - dev-tool
  - nodejs
datum: 2026-02-21
szint: "🌱 Newcomer"
kapcsolodo:
  - "[[_moc/moc-environment-setup|MOC - Environment Setup]]"
---

# fnm (Fast Node Manager)

**Kategória:** `dev tool`
**URL:** https://github.com/Schniz/fnm
**Ár/Terv:** Ingyenes

---

## Mi ez és mire jó?

Az **fnm** (Fast Node Manager) egy Rust-alapú Node.js verziókezelő, ami projekt-szinten kezeli a Node verziókat. Minden projektben lehet egy `.nvmrc` fájl, és az fnm automatikusan arra a verzióra vált amikor belépsz a mappába.

**Egyszerűen:** Több projekted van, mindegyikhez más Node verzió kell — az fnm ezt automatikusan megoldja.

**Mikor használd:**
- Több Node.js projekted van különböző verziókkal
- Nem akarod kézzel `brew link/unlink`-kel váltogatni
- CI/CD rendszerek is olvassák a `.nvmrc`-t (Cloudflare Pages, Vercel, GitHub Actions)
- Claude Code-dal dolgozol, ami a shell-t örökli — az fnm-mel mindig a helyes Node fut

**Mikor NE:**
- Ha egyetlen projekted van egyetlen Node verzióval — felesleges overhead (bár akkor sem árt)

**Alternatívák:**
- **nvm** — a klasszikus, de lassú (bash script, ~40x lassabb)
- **volta** — hasonló koncepció, de más megközelítés (package-szintű pin)
- **brew install node** — rendszerszintű, ha váltasz projektet, eltörik a másik

**Miért fnm:**
- ~40x gyorsabb mint nvm (Rust)
- `.nvmrc` kompatibilis (nvm-mel írt projektek is működnek)
- `--use-on-cd` flag automatikusan vált `cd`-nél

---

## Setup — lépésről lépésre

### 1. Telepítés

```bash
brew install fnm
```

### 2. Shell konfiguráció

```bash
# .zshrc-be hozzáadni (egyszer kell):
eval "$(fnm env --use-on-cd)"
```

A `--use-on-cd` a lényeg — ez figyeli a `cd` parancsot és ha `.nvmrc`-t talál a mappában, automatikusan vált.

### 3. Node verziók telepítése

```bash
fnm install 24
fnm install 22
fnm default 24    # globális default
fnm list          # telepített verziók
```

### 4. Projekt beállítás

Minden projektben legyen `.nvmrc`:

```bash
echo "24" > .nvmrc    # projekt-1
echo "22" > .nvmrc    # másik projekt
```

Ezután `cd`-vel belépve automatikusan vált.

---

## Best Practices

### Architektúra / Struktúra

- **Mindig commitold a `.nvmrc`-t** a repóba — így bárki (ember vagy agent) tudja milyen Node kell
- **Brew node eltávolítása** az fnm telepítés után, hogy ne legyen konfliktus:
  ```bash
  brew uninstall --ignore-dependencies node node@22 node@24
  ```
- **`fnm default`** beállítása a leggyakrabban használt verzióra

### Biztonság

- Nincs biztonsági kockázat — csak Node binárisokat kezel

### Teljesítmény

- Rust-ban írt, instant verzióváltás
- Nem terheli a shell indulást (ellentétben nvm-mel)

---

## Gyakori minták / Használati esetek

### 1. Projekt-szintű Node verzió

```
projekt-1/.nvmrc  → 24
másik-projekt/.nvmrc → 22
```

`cd projekt-1` → automatikusan Node 24
`cd másik-projekt` → automatikusan Node 22

### 2. CI/CD kompatibilitás

A `.nvmrc` fájlt a legtöbb CI natively olvassa:
- **Cloudflare Pages:** `NODE_VERSION` env var VAGY `.nvmrc`
- **Vercel:** automatikusan olvassa
- **GitHub Actions:** `actions/setup-node` `node-version-file: '.nvmrc'`

### 3. Új Node verzió kipróbálása

```bash
fnm install 25
fnm use 25         # ideiglenes, csak ebben a shell-ben
# ha tetszik:
fnm default 25     # legyen ez a default
```

---

## Buktatók és hibák amiket elkerülj

- **Ne hagyd a brew node-ot telepítve** az fnm mellett — a PATH konfliktus rejtett hibákat okoz
- **Új terminál kell** a `.zshrc` módosítás után (`source ~/.zshrc` vagy új tab)
- **Claude Code újraindítás** is kellhet Node váltás után, mert a saját shell-jét cache-eli
- **`npm ci` vs `npm install`** — Node verzióváltás után érdemes `npm ci`-t futtatni, mert a `node_modules` a régi Node-hoz épült

---

## Hasznos parancsok / kódrészletek

```bash
fnm list              # telepített verziók
fnm list-remote       # elérhető verziók
fnm install 24        # verzió telepítése
fnm use 24            # ideiglenes váltás
fnm default 24        # default beállítása
fnm current           # aktuális verzió
fnm uninstall 22      # verzió eltávolítása
```

---

## Hasznos linkek

- Docs: https://github.com/Schniz/fnm
- Releases: https://github.com/Schniz/fnm/releases

---

## Kapcsolódó

- [[_moc/moc-environment-setup|MOC - Environment Setup]]
