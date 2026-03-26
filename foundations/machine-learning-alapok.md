---
tags:
  - ml
  - python
kapcsolodo: []
datum: 2026-02-08
szint: "🧱 Scout"
---

# Machine Learning alapok

## Mi az a Machine Learning?

A **Machine Learning (ML)** egy modszer, amivel a szamitogep **nem explicit programozassal, hanem adatbol tanul**. Ahelyett hogy leirnad a szabalyokat (`if auto then...`), megmutatod neki sok-sok példat, és o maga tanulja még a mintat.

```
Hagyomanyos programozas:         Machine Learning:
--------------------             ---------------------
Szabalyok + Adat -> Eredmeny      Adat + Eredmeny -> Szabalyok
                                 (a modell maga "tanulja ki")
```

**Valós példa: dron kamera**
Egy dron kamerafelvetelen felismerni embereket és autokat. Kezzel nem tudnad leirni a szabalyt ("az ember az egy fuggoleges teglalapszeru valami...") -- de ha megmutatod a modellnek 100 000 kepet ahol jelolve van ki az ember és mi az auto, megtanulja magatol.

---

## A neuralis halo -- az agy metaforaja

A modern ML szinte mind **neuralis halon** alapul. Az emberi agy neuronokbol all amelyek egymasnak jeleket küldenek -- a neuralis halo ezt utanozza matematikailag.

```
Input reteg        Rejtett retegek        Output reteg
(pixelek)          (mintafelismeres)      (kategoriak)

  [px1] --+
           +---> [neuron] --+
  [px2] --+               +---> [ember]
           +---> [neuron] --+
  [px3] --+               +---> [auto]
           +---> [neuron] --+
  [pxN]                       [semmi]
```

### A sulyozasok (weights)

Minden kapcsolat a fenti abran egy **suly** -- egy szam, ami meghatarozza mennyire fontos az adott kapcsolat. A halo tanulasa = **ezeket a szamokat allitgatja** amig a helyes outputot adja.

```
input x suly + bias -> aktivalcios fuggveny -> kovetkezo reteg
```

Kezdetben random szamok. Training utan: az a sulykombinacio ami "kódolja" a tudast -- pl. hogy mi jellemezo egy emberre vs egy autora.

---

## A training folyamat

```
1. Forward pass
   Kep bemegy -> retegeken atmegy -> output: "90% auto, 5% ember"

2. Loss kiszamitasa
   Valodi ertek: "auto" -> Hiba = mennyire tevesztett

3. Backpropagation
   A hiba visszafele terjed -> minden sulyt kicsit modosit
   hogy legkozelebb pontosabb legyen

4. Ismetles
   Millio keppel, ezerszer -> a sulyok egyre jobbak lesznek
```

> [!info] Mi az a backpropagation?
> A backpropagation kiszamolja: **melyik suly mennyiben felelos a hibaert**, és aranyosan korrigalja. A gradient descent algoritmussal működik -- mindig a "lejto" iranyaba lep a hiba minimuma fele. Ez az alapja szinte minden modern ML tanitasnak.

### Amit a training vegen kapsz: a modell fájl

```
Training kesz
    |
Sulyok elmentve -> model.pt / model.onnx / model.h5
    |
Ez a "tudas" -- ezt toltod be production-ban
```

---

## YOLO -- object detection példa

A **YOLO (You Only Look Once)** egy object detection modell -- egy kepen egyszerre több objektumot ismer fel és kore huz egy bounding box-ot.

```
Dron kamerakep
      |
YOLO modell (milliszekundumok)
      |
+---------+  +------+  +------+
| ember   |  | auto |  | auto |
|  94%    |  |  87% |  |  76% |
+---------+  +------+  +------+
```

### Miért YOLO és nem valami mas?

| Modell | Sebesseg | Pontossag | Dronhoz? |
|---|---|---|---|
| **YOLO** | Real-time | Jó | Igen |
| R-CNN | Lassu | Nagyon jó | Nem |
| SSD | Kozepes | Kozepes | Esetleg |

Real-time alkalmazásoknal **sebesseg kritikus** -- nem varhatod még hogy 2 masodpercig elemezzen egy kepkockat ha a dron mozog.

### YOLO futtatasa

```bash
pip install ultralytics
```

```python
from ultralytics import YOLO

model = YOLO('yolov8n.pt')  # nano = gyors; yolov8x.pt = pontosabb

# Dron kamerakep elemzese
results = model('dron_felvetel.jpg')

for r in results:
    for box in r.boxes:
        cls = model.names[int(box.cls)]  # "person" / "car"
        conf = float(box.conf)           # 0.94 = 94% bizonyossag
        print(f"{cls}: {conf:.0%}")
```

> [!tip] AI eszkozok + ML pipeline
> AI fejlesztoeszkozok az egész ML pipeline-t képesek vezerelni: adatfeldolgozo scriptek, training konfig, eredmeny vizualizacio. Nem kell ML expertnek lenned -- leirod mit akarsz, és az eszkoz megirja a Python kódot.

---

## Transfer learning -- ne tanítsd nullarol

A YOLO már **elore tanitott** (pretrained) -- latott 80+ kategoriat (COCO dataset: ember, auto, kerekpar, stb.). Ha standard kategoriak kellenek, **használd ahogy van**.

Ha specialis kategoria kell (pl. "munkavedelmi sisak", "dron a levegoben"):

```
Pretrained YOLO (80 kategoria tudasa)
         |
Fine-tuning: csak az utolso retegeket tanitod ujra
a sajat annotalt kepeiddel (100-1000 kep eleg)
         |
Sajat modell ami mar az egyedi kategoriadat is felismeri
```

Ez a **transfer learning** -- a meglevo tudast megtartod, csak a veget igazitod az új feladathoz.

---

## Fontosabb fogalmak

| Fogalom | Mit jelent |
|---|---|
| **Model** | A betanitott neuralis halo (mentett sulyok) |
| **Training** | A tanitasi folyamat -- adatokon fut, sulyokat allit |
| **Inference** | Éles használat -- új adaton futtatod a modellt |
| **Epoch** | Az összes training adat egyszer vegigment |
| **Batch** | Egyszerre feldolgozott mintak szama |
| **Loss** | Hiba merteke -- ezt minimalizalja a training |
| **Overfitting** | "Bemagolta" a training adatot, ujra nem általánosit |
| **Dataset** | Tanitoanyag -- kepek + annotaciok |
| **Bounding box** | Felismert objektum kore huzott teglalap |
| **Confidence** | Bizonyossag merteke (0.94 = 94%) |
| **Pretrained** | Már betanitott modell, amit tovabb lehet finomhangolni |

---

## Kapcsolodo

- ML inference pipeline agent-kent automatizalva
- ML eredmenyek beillesztese automatizalt workflow-ba
