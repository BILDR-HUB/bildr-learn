---
tags:
  - cli
  - tooling
kapcsolodo:
  - "[[foundations/python-venv|Python venv]]"
  - "[[foundations/projekt-szintu-izolacio|Projekt-szintű izolácio]]"
  - "[[foundations/npm-run-dev-vs-build|npm run dev vs build]]"
  - "[[frontend/nextjs|Next.js]]"
  - "[[cloud/docker-alapok|Docker alapok]]"
  - "[[_moc/moc-environment-setup|MOC - Environment Setup]]"
datum: 2026-02-08
szint: "🌱 Newcomer"
---

# Csomagkezelok és CLI toolok

## Mi az a csomagkezelo?

Egy csomagkezelo (package manager) egy program, ami **mas programokat és könyvtárakat telepít, frissít és kezel** helyetted. Ahelyett, hogy kezzel letöltenel mindent egy weboldalrol, egy parancsot irsz és kesz.

Minden okoszisztemanak megvan a sajátja:

```
macOS alkalmazasok     -> brew
Node.js csomagok       -> npm / pnpm / bun
Python csomagok        -> pip / uv
Rust csomagok          -> cargo
```

---

## Az osszefugges: nyelv -> runtime -> csomagkezelo

Ez a retegzodes a lényeg:

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

Például egy Next.js projektnel:
- **TypeScript**-ben irod a kódot (nyelv)
- **Node.js** futtatja (runtime)
- **npm** telepíti a Next.js-t, React-ot, GSAP-ot (csomagkezelo)
- **brew** telepítette magat a Node.js-t a gépedre (OS szintű) -- most már fnm csinálja

---

## Rendszerszintu: brew

**Mi ez:** macOS csomagkezelo -- CLI programokat és alkalmazásokat telepít a gépedre.

```bash
brew install git           # git telepitese
brew install ffmpeg         # video konvertalo
brew install fnm            # Node verzikezelo
brew install --cask firefox # GUI alkalmazas
```

**Mit kezel:** Mindent ami a gépedre kerul rendszerszinten (nem projekt-specifikus)
**Mire NE:** Projekt-szintű Node csomagokra (arra npm kell)

---

## Node.js okoszisztema: npm, npx, pnpm, bun

### npm (Node Package Manager)

**Mi ez:** A Node.js alapértelmezett csomagkezeloje. Csomagokat telepít a `node_modules/` mappaba.

```bash
npm install              # package.json-bol telepit mindent
npm install react        # egy csomag hozzaadasa
npm run dev              # script futtatasa a package.json-bol
npm run build            # script futtatasa
```

**Fájlok amiket kezel:**
- `package.json` -- milyen csomagok kellenek (recept)
- `package-lock.json` -- pontos verziók rögzítve (lakat)
- `node_modules/` -- a letoltott csomagok (gitignore-ban!)

### npx (Node Package Execute)

**Mi ez:** Nem telepít, hanem **egyszer lefuttat** egy csomagot. Az npm-mel egyutt jon.

```bash
npx wrangler pages deploy ./out    # wrangler futtatasa telepites nelkul
npx create-next-app                # Next.js projekt letrehozasa
```

**Mikor használd:**
- CLI eszkozok amiket nem akarsz globálisan telepíteni
- Egyszeri parancsok (projekt generatorok)

**npm vs npx:**
```
npm install wrangler   -> letolti a node_modules-ba, ott marad
npx wrangler           -> letolti, lefuttatja, nem marad ott (cache-eli)
```

### pnpm

**Mi ez:** Az npm gyorsabb és hatékonyabb alternativaja. Ugyanazt csinálja, de a `node_modules`-t okosabban kezeli (hard linkek, kevesebb lemezhasznalas).

```bash
pnpm install    # mint npm install, de gyorsabb
pnpm add react  # mint npm install react
pnpm run dev    # mint npm run dev
```

**Mikor valaszd npm helyett:** Ha sok projekted van és a lemezhelyet/sebességet optimalizálnad. Amúgy az npm tokeletesen eleg.

### bun

**Mi ez:** Egy all-in-one tool ami egyszerre **runtime** (Node.js alternativa) + **csomagkezelo** (npm alternativa) + **bundler**. Zig-ben irt, nagyon gyors.

```bash
bun install     # mint npm install, de 10-25x gyorsabb
bun run dev     # mint npm run dev
bun add react   # mint npm install react
```

**Mikor valaszd:** Új projekt, ahol a sebesseg fontos és nem kell 100% Node.js kompatibilitas.

---

## Python okoszisztema: pip, uv

### pip

**Mi ez:** A Python alapértelmezett csomagkezeloje.

```bash
pip install requests        # csomag telepitese
pip install -r requirements.txt  # lista alapjan telepites
pip freeze > requirements.txt    # telepitett csomagok mentese
```

**Fontos:** Mindig [[foundations/python-venv|Python venv]]-ben használd, különben globálisan telepít!

### uv

**Mi ez:** A pip modern, Rust-alapu alternativaja. Sokkal gyorsabb és a venv-et is kezeli.

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

**Fájlok:** `Cargo.toml` (mint a package.json) + `Cargo.lock` (mint a package-lock.json)

**Érdekesseg:** Sok modern dev tool Rust-ban irodott és cargo-val epul: fnm, uv, bun reszben, Turbopack (a Next.js build tool)

---

## Összefoglaló táblazat

| Csomagkezelo | Okoszisztema | Mit kezel | Fo fájl | Lock fájl |
|---|---|---|---|---|
| **brew** | macOS | Rendszerszintu CLI programok | - | `brew.lock.json` |
| **npm** | Node.js | JS/TS csomagok | `package.json` | `package-lock.json` |
| **npx** | Node.js | Egyszeri futtatas (nem telepít) | - | - |
| **pnpm** | Node.js | JS/TS csomagok (hatékonyabb npm) | `package.json` | `pnpm-lock.yaml` |
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

- [[foundations/python-venv|Python venv]] -- Python izolácio
- [[foundations/projekt-szintu-izolacio|Projekt-szintű izolácio]]
- [[foundations/npm-run-dev-vs-build|npm run dev vs build]]
- [[frontend/nextjs|Next.js]]
- [[cloud/docker-alapok|Docker alapok]]
- [[_moc/moc-environment-setup|MOC - Environment Setup]]
