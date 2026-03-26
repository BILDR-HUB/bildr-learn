---
tags:
  - nyelv
  - szintaxis
  - python
datum: 2026-03-26
szint: "🌱 Newcomer"
kapcsolodo:
  - "[[foundations/szintaxis-alapok|Szintaxis alapok]]"
  - "[[foundations/javascript-es-typescript-szintaxis|JavaScript és TypeScript szintaxis]]"
  - "[[foundations/typescript-vs-python|TypeScript vs Python]]"
  - "[[foundations/python-venv|Python venv]]"
---

# Python szintaxis

## Miről szól ez a note?

A [[foundations/szintaxis-alapok|Szintaxis alapok]] note-ban a közös fogalmakat tanultad meg. Ez a note a **Python-specifikus szintaxis** - azok a dolgok amik csak Pythonban vannak, vagy Pythonban másképp néznek ki.

> [!tldr]
> Python szintaxis "szótár" - ha Python kódot látsz, itt megtalálod mit jelent benne minden.

---

## Python felismerése - honnan tudod hogy Python?

Ha ezeket látod - Python:
- **Nincs `{ }` kapcsos zárójel** - behúzás (indentáció) jelöli a blokkokat
- **`def`** kulcsszó függvényeknél (nem `function`)
- **`:`** kettőspont az `if`, `for`, `def`, `class` végén
- **`True` / `False` / `None`** nagybetűkkel kezdődik (JS-ben `true` / `false` / `null`)
- **`#`** komment (nem `//`)
- **`self`** paraméter osztályokban
- **`elif`** (nem `else if`)

---

## A blokk-rendszer - behúzás a király

Pythonban a **behúzás (4 szóköz) maga a szintaxis**:

```python
def feldolgoz(adat):                # <- kettőspont = blokk kezdődik
    if adat is not None:            # <- 4 szóköz = feldolgoz() blokkja
        eredmeny = adat * 2         # <- 8 szóköz = if blokkja
        return eredmeny             # <- 8 szóköz = még mindig if-ben
    return 0                        # <- 4 szóköz = if-en kívül, de feldolgoz()-on belül
                                    # <- 0 szóköz = a függvényen kívül
```

> [!warning] Fontos
> Ha a behúzás rosszul van - `IndentationError` hibát kapsz. Pythonban a whitespace **nem opcionális formázás** - a program logikáját határozza meg.

---

## Függvények részletesen

### def - függvény definiálás

```python
def koszontes(nev, formalis=False):     # <- paraméterek + alapértelmezett érték
    """Köszönti a felhasználót."""       # <- docstring (dokumentáció)
    if formalis:
        return f"Üdvözlöm, {nev}!"
    return f"Szia, {nev}!"

# Hívás
koszontes("Anna")                       # -> "Szia, Anna!"
koszontes("Anna", formalis=True)        # -> "Üdvözlöm, Anna!"
koszontes(nev="Anna", formalis=True)    # <- named argument (névvel adod meg)
```

| Amit látsz | Jelentés |
|-----------|---------|
| `def nev():` | Függvény definiálás |
| `"""szoveg"""` | Docstring - a függvény dokumentációja |
| `param=ertek` | Alapértelmezett paraméter (ha nem adod meg, ezt kapja) |
| `nev="Anna"` | Híváskor: named argument (névesített paraméter) |
| `*args` | Tetszőleges számú pozicionális arg (ritkán látod) |
| `**kwargs` | Tetszőleges számú névesített arg (ritkán látod) |

---

## f-string - szöveg behelyettesítés

```python
nev = "Anna"
kor = 28

# f-string - az f jelzi hogy benne változók lesznek
uzenet = f"Szia {nev}, te {kor} éves vagy!"
# -> "Szia Anna, te 28 éves vagy!"

# Kifejezés is lehet benne
uzenet = f"Jövőre {kor + 1} leszel"
# -> "Jövőre 29 leszel"
```

| Szintaxis | Példa |
|-----------|-------|
| `f"szoveg {valtozo}"` | Python f-string |
| `` `szoveg ${valtozo}` `` | JS/TS template literal (hasonló!) |

> [!tip] Felismerés
> Ha `f"..."` látsz (kis f a string előtt) - az egy f-string, a `{ }` között lévő rész kód amit behelyettesít.

---

## Osztályok (class) és self

```python
class Szamla:
    def __init__(self, osszeg, partner):    # <- konstruktor (létrehozáskor fut le)
        self.osszeg = osszeg                # <- self = "ez az objektum"
        self.partner = partner
        self.fizetve = False                # <- alapértelmezett property

    def fizet(self):                        # <- metódus (az osztály függvénye)
        self.fizetve = True
        return f"{self.osszeg} Ft fizetve"

# Használat
szamla = Szamla(50000, "Példa Kft.")        # <- példányosítás (instance létrehozás)
szamla.fizet()                               # <- metódus hívás
```

| Amit látsz | Jelentés |
|-----------|---------|
| `class Nev:` | Osztály definíció (objektum "terv") |
| `__init__(self, ...)` | Konstruktor - lefut amikor létrehozod |
| `self` | Az aktuális objektumra mutat ("saját magam") |
| `self.nev = ertek` | Tulajdonság beállítása |
| `Szamla(...)` | Példányosítás - egy konkrét objektum létrehozása |

> [!tip] Felismerés
> Ha `self`-et látsz - Python osztály metódusa. A `self` mindig az első paraméter, de híváskor nem adod meg - automatikus. A `self.valami` az objektum saját adata.

---

## Decorator-ok (@) - függvény módosítók

A **decorator** egy `@` jellel jelölt "burkoló" ami módosítja a függvény viselkedését:

```python
@app.get("/api/users")          # <- decorator: "ez a függvény a GET /api/users route"
def list_users():
    return {"users": [...]}

@require_auth                   # <- decorator: "csak bejelentkezett user hívhatja"
def secret_data():
    return {"secret": "..."}

@staticmethod                   # <- decorator: "ez a metódus nem kap self-et"
def helper():
    return "segítség"
```

> [!tip] Felismerés
> Ha `@valamit` látsz egy függvény felett - az egy decorator. Nem kell megértened mit csinál a háttérben - elég annyit tudni, hogy **módosítja vagy jelöli a függvényt** alatta. Gyakori minták: `@app.get()` (API route), `@login_required` (auth), `@property` (getter).

---

## Adatszerkezetek

### Lista, tuple, dict, set

```python
# Lista - módosítható, rendezett
gyumolcsok = ["alma", "korte", "szilva"]
gyumolcsok.append("barack")             # <- hozzáadás

# Tuple - NEM módosítható, rendezett (zárójelben)
koordinatak = (47.4979, 19.0402)        # <- ezt nem tudod megváltoztatni

# Dictionary - kulcs-érték párok (mint JS object)
felhasznalo = {
    "nev": "Anna",
    "kor": 28,
    "aktiv": True
}
felhasznalo["nev"]                      # -> "Anna"
felhasznalo.get("email", "nincs")       # -> "nincs" (ha nem létezik a kulcs)

# Set - egyedi elemek (nincs duplikálás)
tagok = {"Anna", "Daniel", "Anna"}       # -> {"Anna", "Daniel"}
```

| Szintaxis | Mi ez | JS/TS megfelelő |
|-----------|-------|-----------------|
| `[...]` | Lista | `[...]` array |
| `(...)` | Tuple | Nincs natív |
| `{kulcs: ertek}` | Dict | `{kulcs: ertek}` object |
| `{elem1, elem2}` | Set | `new Set([...])` |

---

## List comprehension - tömör listaépítés

Ez a Python egyik legjellemzőbb szintaxisa:

```python
# Hagyományos ciklus
paros_szamok = []
for szam in range(10):
    if szam % 2 == 0:
        paros_szamok.append(szam)

# UGYANEZ list comprehension-nel - egy sor
paros_szamok = [szam for szam in range(10) if szam % 2 == 0]
#               ^ MIT   ^ HONNAN              ^ SZŰRŐ (opcionális)
# -> [0, 2, 4, 6, 8]
```

**Olvasási minta:** `[MIT for VALTOZO in LISTA if FELTETEL]`
Magyarul: "adj egy listát, ami MITből áll, VÁLTOZÓnként bejárva a LISTÁT, de csak ha a FELTÉTEL igaz"

---

## with - erőforrás kezelés

A `with` biztosítja, hogy egy erőforrás (fájl, kapcsolat) **automatikusan bezáródjon**:

```python
# Fájl olvasás - a with automatikusan bezárja a fájlt
with open("adat.txt", "r") as fajl:
    tartalom = fajl.read()
# <- itt a fájl már be van zárva, nem kell kézzel

# Adatbázis kapcsolat
with get_db_connection() as db:
    db.execute("SELECT * FROM users")
# <- kapcsolat automatikusan lezárva
```

> [!tip] Felismerés
> `with X as Y:` - "nyisd meg X-et, használd Y néven, és ha kilépünk a blokkból, automatikusan zárd be". Fájloknál és adatbázisoknál nagyon gyakori.

---

## Type hints - típusjelölés

Python 3.5+ óta **opcionálisan** megadhatod a típusokat:

```python
# Típus nélkül - működik, de nem tudod mit vár
def osszead(a, b):
    return a + b

# Típussal - dokumentálja mit vár és mit ad vissza
def osszead(a: int, b: int) -> int:
    return a + b
#                             ^ -> jelöli a visszatérési típust

# Összetett típusok
def keres(nev: str) -> list[dict[str, str]]:
    ...

# Optional - lehet None is
def keres(id: int) -> str | None:       # <- str VAGY None
    ...
```

> [!info] Fontos különbség
> Python type hintek **nem kényszerítenek** - ha rossz típust adsz, a kód akkor is lefut (futásidőben derül ki a hiba). TypeScript-ben viszont a compiler **megállítja** a buildet ha rossz a típus.

---

## Gyakran látott minták

### if __name__ == "__main__"

```python
def main():
    print("Fő program fut")

if __name__ == "__main__":      # <- "csak akkor futtasd ha közvetlenül ezt a fájlt indítod"
    main()                      #    (ha másik fájl importálja, NEM fut le)
```

### try / except (hibakezelés)

```python
try:
    eredmeny = 10 / 0
except ZeroDivisionError:       # <- konkrét hiba típus
    print("Nullával nem lehet osztani")
except Exception as e:          # <- bármilyen hiba, `e` változóba teszi
    print(f"Hiba: {e}")
finally:                        # <- MINDIG lefut (akár volt hiba, akár nem)
    print("Takarítás")
```

---

## AI-natív fejlesztés

A Python az AI/ML világ lingua franca-ja - a legtöbb AI modell, training pipeline és data science tool Python-ban íródott. Ha Python szintaxist értesz, könnyebben navigálsz az AI tool-ok forráskódjában és dokumentációjában.

> [!tip] Hogyan használd AI-val
> - *"Magyarázd el mit csinál ez a Python decorator: [kód beillesztése]"*
> - *"Írj egy Python scriptet ami CSV-t olvas és pandas-szal feldolgozza"*
> - *"Alakítsd át ezt a for ciklust list comprehension-né"*

---

## Kapcsolódó

- [[foundations/szintaxis-alapok|Szintaxis alapok]] - közös fogalmak minden nyelvhez
- [[foundations/javascript-es-typescript-szintaxis|JavaScript és TypeScript szintaxis]] - JS/TS specifikus szintaxis
- [[foundations/typescript-vs-python|TypeScript vs Python]] - mikor melyik nyelvet válaszd
- [[foundations/python-venv|Python venv]] - Python projekt izoláció
