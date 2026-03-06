---
tags:
  - python
  - tooling
kapcsolodo:
  - "[[foundations/projekt-szintu-izolacio|Projekt-szintu izolacio]]"
  - "[[cloud/docker-alapok|Docker alapok]]"
  - "[[foundations/csomagkezelok-es-cli-toolok|Csomagkezelok es CLI toolok]]"
  - "[[_moc/moc-environment-setup|MOC - Environment Setup]]"
  - "[[foundations/typescript-vs-python|TypeScript vs Python]]"
datum: 2026-02-08
szint: "🌱 Newcomer"
---

# Python venv

## Mi ez?

A **venv** (virtual environment) a Python beepitett izolacios eszkoze. Minden projekthez letrehoz egy kulon "dobozt" amiben a Python csomagok elnek -- nem keverednek a tobbi projekt csomagjaival.

Ugyanaz a koncepcio mint a [[foundations/projekt-szintu-izolacio|Projekt-szintu izolacio]] Node.js-ben: a `node_modules` mappa per-projekt, a `venv` is per-projekt.

---

## Miert kell?

**Problema venv nelkul:**

```
pip install requests==2.28      # Projekt A-nak ez kell
pip install requests==2.31      # Projekt B-nak ez kell -> felulirja A-t!
                                # Projekt A eltorik
```

**Megoldas venv-vel:**

```
projekt-a/venv/  -> requests 2.28 (izolalt)
projekt-b/venv/  -> requests 2.31 (izolalt)
                   nem tudnak egymasrol
```

---

## Hogyan mukodik?

A `venv` egy mappat hoz letre a projektedben (altalaban `venv/` vagy `.venv/`), amiben:
- Egy sajat Python binaris van (symlink a rendszer Python-ra)
- Egy sajat `pip` van
- Minden `pip install` ide rakja a csomagokat, nem a rendszer szintre

Amikor "aktivalod" a venv-et, a shell PATH-jat modositja, hogy a projekt Python-jat hasznald a globalis helyett.

---

## Hasznalat lepesrol lepesre

### 1. Letrehozas (egyszer, projekt elejen)

```bash
python3 -m venv venv
```

Ez letrehozza a `venv/` mappat. **Add hozza a `.gitignore`-hoz** -- nem commitoljuk!

### 2. Aktivalas (minden alkalommal amikor a projekten dolgozol)

```bash
source venv/bin/activate
```

Aktivalas utan a prompt eleje kerul a `(venv)`:

```
(venv) user@MacBook projekt %
```

### 3. Csomagok telepitese

```bash
pip install requests flask openai
```

Ezek a `venv/` mappaba kerulnek, nem globalisan.

### 4. Csomagok rogzitese (mint a package-lock.json)

```bash
pip freeze > requirements.txt
```

### 5. Deaktivalas

```bash
deactivate
```

### 6. Masik fejleszto / CI telepites

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

---

## Osszehasonlitas a Node.js-szel

| Koncepcio | Node.js | Python |
|---|---|---|
| Csomagok izolalasa | `node_modules/` (automatikus) | `venv/` (kezzel kell letrehozni) |
| Csomagok listaja | `package.json` | `requirements.txt` |
| Lock fajl | `package-lock.json` | `requirements.txt` (vagy `uv.lock`) |
| Telepites | `npm install` | `pip install -r requirements.txt` |
| Verzio kezelo | fnm + `.nvmrc` | `pyenv` + `.python-version` |

**Lenyeges kulonbseg:** Node.js-ben a `node_modules` automatikusan projekt-szintu. Pythonban a `pip install` alapbol **globalis** -- ezert kell a venv, hogy projektszintuve tedd.

---

## Modern alternativak

### uv (ajanlott)

A **uv** egy Rust-alapu Python csomag- es projektkezelo (hasonlo mint az fnm a Node-hoz). Sokkal gyorsabb mint a pip + venv es egy parancsba suriti az egeszet:

```bash
uv init my-project     # projekt letrehozas
uv add requests        # csomag hozzaadas (auto venv)
uv run script.py       # futtatas a venv-ben
```

### Poetry

Hasonlo mint az uv, de regebbi es lassabb. `pyproject.toml`-t hasznal a `requirements.txt` helyett.

---

## Mikor NE hasznalj venv-et?

- **Egysoros scriptek** -- ha csak egy `python3 script.py`-t futtatsz import nelkul
- **Docker-ben** -- a kontener maga az izolacio, venv felesleges overhead
- **Agentic scripteknél** -- ha a script nem fugg kulso csomagoktol

---

## Kapcsolodo

- [[foundations/projekt-szintu-izolacio|Projekt-szintu izolacio]]
- [[cloud/docker-alapok|Docker alapok]]
- [[foundations/csomagkezelok-es-cli-toolok|Csomagkezelok es CLI toolok]]
- [[_moc/moc-environment-setup|MOC - Environment Setup]]
- [[foundations/typescript-vs-python|TypeScript vs Python]] -- mikor melyik nyelvet hasznald
