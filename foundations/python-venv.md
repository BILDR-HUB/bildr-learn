---
tags:
  - python
  - tooling
kapcsolodo:
  - "[[foundations/projekt-szintu-izolacio|Projekt-szintű izolácio]]"
  - "[[cloud/docker-alapok|Docker alapok]]"
  - "[[foundations/csomagkezelok-es-cli-toolok|Csomagkezelok és CLI toolok]]"
  - "[[_moc/moc-environment-setup|MOC - Environment Setup]]"
  - "[[foundations/typescript-vs-python|TypeScript vs Python]]"
datum: 2026-02-08
szint: "🌱 Newcomer"
---

# Python venv

## Mi ez?

A **venv** (virtual environment) a Python beepitett izolácios eszkoze. Minden projekthez létrehoz egy kulon "dobozt" amiben a Python csomagok elnek -- nem keverednek a többi projekt csomagjaival.

Ugyanaz a koncepcio mint a [[foundations/projekt-szintu-izolacio|Projekt-szintű izolácio]] Node.js-ben: a `node_modules` mappa per-projekt, a `venv` is per-projekt.

---

## Miért kell?

**Probléma venv nelkul:**

```
pip install requests==2.28      # Projekt A-nak ez kell
pip install requests==2.31      # Projekt B-nak ez kell -> felulirja A-t!
                                # Projekt A eltorik
```

**Megoldás venv-vel:**

```
projekt-a/venv/  -> requests 2.28 (izolalt)
projekt-b/venv/  -> requests 2.31 (izolalt)
                   nem tudnak egymasrol
```

---

## Hogyan működik?

A `venv` egy mappat hoz letre a projektedben (általában `venv/` vagy `.venv/`), amiben:
- Egy saját Python binaris van (symlink a rendszer Python-ra)
- Egy saját `pip` van
- Minden `pip install` ide rakja a csomagokat, nem a rendszer szintre

Amikor "aktivalod" a venv-et, a shell PATH-jat módosítja, hogy a projekt Python-jat használd a globális helyett.

---

## Használat lépésrol lépésre

### 1. Létrehozas (egyszer, projekt elejen)

```bash
python3 -m venv venv
```

Ez létrehozza a `venv/` mappat. **Add hozzá a `.gitignore`-hoz** -- nem commitoljuk!

### 2. Aktivalas (minden alkalommal amikor a projekten dolgozol)

```bash
source venv/bin/activate
```

Aktivalas utan a prompt eleje kerul a `(venv)`:

```
(venv) user@MacBook projekt %
```

### 3. Csomagok telepítese

```bash
pip install requests flask openai
```

Ezek a `venv/` mappaba kerulnek, nem globálisan.

### 4. Csomagok rögzítése (mint a package-lock.json)

```bash
pip freeze > requirements.txt
```

### 5. Deaktivalas

```bash
deactivate
```

### 6. Másik fejleszto / CI telepítes

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

---

## Összehasonlítas a Node.js-szel

| Koncepcio | Node.js | Python |
|---|---|---|
| Csomagok izolalasa | `node_modules/` (automatikus) | `venv/` (kezzel kell létrehozni) |
| Csomagok listaja | `package.json` | `requirements.txt` |
| Lock fájl | `package-lock.json` | `requirements.txt` (vagy `uv.lock`) |
| Telepítes | `npm install` | `pip install -r requirements.txt` |
| Verzió kezelo | fnm + `.nvmrc` | `pyenv` + `.python-version` |

**Lényeges különbség:** Node.js-ben a `node_modules` automatikusan projekt-szintű. Pythonban a `pip install` alapbol **globális** -- ezert kell a venv, hogy projektszintuve tedd.

---

## Modern alternativak

### uv (ajanlott)

A **uv** egy Rust-alapu Python csomag- és projektkezelo (hasonlo mint az fnm a Node-hoz). Sokkal gyorsabb mint a pip + venv és egy parancsba suriti az egészet:

```bash
uv init my-project     # projekt letrehozas
uv add requests        # csomag hozzaadas (auto venv)
uv run script.py       # futtatas a venv-ben
```

### Poetry

Hasonlo mint az uv, de régebbi és lassabb. `pyproject.toml`-t használ a `requirements.txt` helyett.

---

## Mikor NE használj venv-et?

- **Egysoros scriptek** -- ha csak egy `python3 script.py`-t futtatsz import nelkul
- **Docker-ben** -- a konténer maga az izolácio, venv felesleges overhead
- **Agentic scripteknél** -- ha a script nem fugg külső csomagoktol

---

## Kapcsolodo

- [[foundations/projekt-szintu-izolacio|Projekt-szintű izolácio]]
- [[cloud/docker-alapok|Docker alapok]]
- [[foundations/csomagkezelok-es-cli-toolok|Csomagkezelok és CLI toolok]]
- [[_moc/moc-environment-setup|MOC - Environment Setup]]
- [[foundations/typescript-vs-python|TypeScript vs Python]] -- mikor melyik nyelvet használd
