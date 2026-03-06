---
tags:
  - cli
  - tooling
kapcsolodo:
  - "[[foundations/python-venv|Python venv]]"
  - "[[foundations/projekt-szintu-izolacio|Projekt-szintu izolacio]]"
  - "[[foundations/npm-run-dev-vs-build|npm run dev vs build]]"
  - "[[frontend/nextjs|Next.js]]"
  - "[[cloud/docker-alapok|Docker alapok]]"
  - "[[_moc/moc-environment-setup|MOC - Environment Setup]]"
datum: 2026-02-08
szint: "🌱 Newcomer"
---

# Csomagkezelok es CLI toolok

## Mi az a csomagkezelo?

Egy csomagkezelo (package manager) egy program, ami **mas programokat es konyvtarakat telepit, frissit es kezel** helyetted. Ahelyett, hogy kezzel letoltenel mindent egy weboldalrol, egy parancsot irsz es kesz.

Minden okoszisztemanak megvan a sajatja:

```
macOS alkalmazasok     -> brew
Node.js csomagok       -> npm / pnpm / bun
Python csomagok        -> pip / uv
Rust csomagok          -> cargo
```

---

## Az osszefugges: nyelv -> runtime -> csomagkezelo

Ez a retegzodes a lenyeg:

```
+-------------------------------------------------+
|  Te irsz kodot: TypeScript, Python, Rust         |  <- nyelv
+-------------------------------------------------+
|  Futtatja: Node.js, Python, Rust compiler        |  <- runtime
+-------------------------------------------------+
|  Fuggosegeket kezel: npm, pip, cargo             |  <- csomagkezelo
+-------------------------------------------------+
|  Rendszerszintu: brew                            |  <- OS csomagkezelo
+-------------------------------------------------+
```

Peldaul egy Next.js projektnel:
- **TypeScript**-ben irod a kodot (nyelv)
- **Node.js** futtatja (runtime)
- **npm** telepiti a Next.js-t, React-ot, GSAP-ot (csomagkezelo)
- **brew** telepitette magat a Node.js-t a gepedre (OS szintu) -- most mar fnm csinalja

---

## Rendszerszintu: brew

**Mi ez:** macOS csomagkezelo -- CLI programokat es alkalmazasokat telepit a gepedre.

```bash
brew install git           # git telepitese
brew install ffmpeg         # video konvertalo
brew install fnm            # Node verzikezelo
brew install --cask firefox # GUI alkalmazas
```

**Mit kezel:** Mindent ami a gepedre kerul rendszerszinten (nem projekt-specifikus)
**Mire NE:** Projekt-szintu Node csomagokra (arra npm kell)

---

## Node.js okoszisztema: npm, npx, pnpm, bun

### npm (Node Package Manager)

**Mi ez:** A Node.js alapertelmezett csomagkezeloje. Csomagokat telepit a `node_modules/` mappaba.

```bash
npm install              # package.json-bol telepit mindent
npm install react        # egy csomag hozzaadasa
npm run dev              # script futtatasa a package.json-bol
npm run build            # script futtatasa
```

**Fajlok amiket kezel:**
- `package.json` -- milyen csomagok kellenek (recept)
- `package-lock.json` -- pontos verziok rogzitve (lakat)
- `node_modules/` -- a letoltott csomagok (gitignore-ban!)

### npx (Node Package Execute)

**Mi ez:** Nem telepit, hanem **egyszer lefuttat** egy csomagot. Az npm-mel egyutt jon.

```bash
npx wrangler pages deploy ./out    # wrangler futtatasa telepites nelkul
npx create-next-app                # Next.js projekt letrehozasa
```

**Mikor hasznald:**
- CLI eszkozok amiket nem akarsz globalisan telepiteni
- Egyszeri parancsok (projekt generatorok)

**npm vs npx:**
```
npm install wrangler   -> letolti a node_modules-ba, ott marad
npx wrangler           -> letolti, lefuttatja, nem marad ott (cache-eli)
```

### pnpm

**Mi ez:** Az npm gyorsabb es hatekonyabb alternativaja. Ugyanazt csinalja, de a `node_modules`-t okosabban kezeli (hard linkek, kevesebb lemezhasznalas).

```bash
pnpm install    # mint npm install, de gyorsabb
pnpm add react  # mint npm install react
pnpm run dev    # mint npm run dev
```

**Mikor valaszd npm helyett:** Ha sok projekted van es a lemezhelyet/sebességet optimalizalnad. Amúgy az npm tokeletesen eleg.

### bun

**Mi ez:** Egy all-in-one tool ami egyszerre **runtime** (Node.js alternativa) + **csomagkezelo** (npm alternativa) + **bundler**. Zig-ben irt, nagyon gyors.

```bash
bun install     # mint npm install, de 10-25x gyorsabb
bun run dev     # mint npm run dev
bun add react   # mint npm install react
```

**Mikor valaszd:** Uj projekt, ahol a sebesseg fontos es nem kell 100% Node.js kompatibilitas.

---

## Python okoszisztema: pip, uv

### pip

**Mi ez:** A Python alapertelmezett csomagkezeloje.

```bash
pip install requests        # csomag telepitese
pip install -r requirements.txt  # lista alapjan telepites
pip freeze > requirements.txt    # telepitett csomagok mentese
```

**Fontos:** Mindig [[foundations/python-venv|Python venv]]-ben hasznald, kulonben globalisan telepit!

### uv

**Mi ez:** A pip modern, Rust-alapu alternativaja. Sokkal gyorsabb es a venv-et is kezeli.

```bash
uv add requests    # csomag + venv egyben
uv run script.py   # futtatas a venv-ben
```

---

## Rust okoszisztema: cargo

### cargo

**Mi ez:** A Rust nyelv beepitett csomagkezeloje + build tools. Ha Rust-ot irsz, a cargo mindent kezel.

```bash
cargo new my-project   # projekt letrehozas
cargo add serde        # csomag hozzaadasa
cargo build            # forditas
cargo run              # forditas + futtatas
```

**Fajlok:** `Cargo.toml` (mint a package.json) + `Cargo.lock` (mint a package-lock.json)

**Erdekesseg:** Sok modern dev tool Rust-ban irodott es cargo-val epul: fnm, uv, bun reszben, Turbopack (a Next.js build tool)

---

## Osszefoglalo tablazat

| Csomagkezelo | Okoszisztema | Mit kezel | Fo fajl | Lock fajl |
|---|---|---|---|---|
| **brew** | macOS | Rendszerszintu CLI programok | - | `brew.lock.json` |
| **npm** | Node.js | JS/TS csomagok | `package.json` | `package-lock.json` |
| **npx** | Node.js | Egyszeri futtatas (nem telepit) | - | - |
| **pnpm** | Node.js | JS/TS csomagok (hatekonyabb npm) | `package.json` | `pnpm-lock.yaml` |
| **bun** | Node.js alternativa | JS/TS csomagok + runtime | `package.json` | `bun.lockb` |
| **pip** | Python | Python csomagok | `requirements.txt` | - |
| **uv** | Python | Python csomagok + venv | `pyproject.toml` | `uv.lock` |
| **cargo** | Rust | Rust csomagok + build | `Cargo.toml` | `Cargo.lock` |

---

## Egy tipikus stack

```
brew    -> fnm, ffmpeg, egyeb CLI toolok
fnm     -> Node.js verziok kezelese (.nvmrc per projekt)
npm     -> Node csomagok (Next.js, React, GSAP, stb.)
npx     -> wrangler es egyeb egyszeri CLI futttatasok
```

---

## Kapcsolodo

- [[foundations/python-venv|Python venv]] -- Python izolacio
- [[foundations/projekt-szintu-izolacio|Projekt-szintu izolacio]]
- [[foundations/npm-run-dev-vs-build|npm run dev vs build]]
- [[frontend/nextjs|Next.js]]
- [[cloud/docker-alapok|Docker alapok]]
- [[_moc/moc-environment-setup|MOC - Environment Setup]]
