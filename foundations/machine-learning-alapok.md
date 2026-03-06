---
tags:
  - ml
  - python
kapcsolodo: []
datum: 2026-02-08
szint: "🧱 Brick"
---

# Machine Learning alapok

## Mi az a Machine Learning?

A **Machine Learning (ML)** egy modszer, amivel a szamitogep **nem explicit programozassal, hanem adatbol tanul**. Ahelyett hogy leirnad a szabalyokat (`if auto then...`), megmutatod neki sok-sok peldat, es o maga tanulja meg a mintat.

```
Hagyomanyos programozas:         Machine Learning:
--------------------             ---------------------
Szabalyok + Adat -> Eredmeny      Adat + Eredmeny -> Szabalyok
                                 (a modell maga "tanulja ki")
```

**Valos pelda: dron kamera**
Egy dron kamerafelvetelen felismerni embereket es autokat. Kezzel nem tudnad leirni a szabalyt ("az ember az egy fuggoleges teglalapszeru valami...") -- de ha megmutatod a modellnek 100 000 kepet ahol jelolve van ki az ember es mi az auto, megtanulja magatol.

---

## A neuralis halo -- az agy metaforaja

A modern ML szinte mind **neuralis halon** alapul. Az emberi agy neuronokbol all amelyek egymasnak jeleket kuldenek -- a neuralis halo ezt utanozza matematikailag.

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

Kezdetben random szamok. Training utan: az a sulykombinacio ami "kodolja" a tudast -- pl. hogy mi jellemezo egy emberre vs egy autora.

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
> A backpropagation kiszamolja: **melyik suly mennyiben felelos a hibaert**, es aranyosan korrigalja. A gradient descent algoritmussal mukodik -- mindig a "lejto" iranyaba lep a hiba minimuma fele. Ez az alapja szinte minden modern ML tanitasnak.

### Amit a training vegen kapsz: a modell fajl

```
Training kesz
    |
Sulyok elmentve -> model.pt / model.onnx / model.h5
    |
Ez a "tudas" -- ezt toltod be production-ban
```

---

## YOLO -- object detection pelda

A **YOLO (You Only Look Once)** egy object detection modell -- egy kepen egyszerre tobb objektumot ismer fel es kore huz egy bounding box-ot.

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

### Miert YOLO es nem valami mas?

| Modell | Sebesseg | Pontossag | Dronhoz? |
|---|---|---|---|
| **YOLO** | Real-time | Jo | Igen |
| R-CNN | Lassu | Nagyon jo | Nem |
| SSD | Kozepes | Kozepes | Esetleg |

Real-time alkalmazasoknal **sebesseg kritikus** -- nem varhatod meg hogy 2 masodpercig elemezzen egy kepkockat ha a dron mozog.

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
> AI fejlesztoeszkozok az egesz ML pipeline-t kepesek vezerelni: adatfeldolgozo scriptek, training konfig, eredmeny vizualizacio. Nem kell ML expertnek lenned -- leirod mit akarsz, es az eszkoz megirja a Python kodot.

---

## Transfer learning -- ne tanítsd nullarol

A YOLO mar **elore tanitott** (pretrained) -- latott 80+ kategoriat (COCO dataset: ember, auto, kerekpar, stb.). Ha standard kategoriak kellenek, **hasznald ahogy van**.

Ha specialis kategoria kell (pl. "munkavedelmi sisak", "dron a levegoben"):

```
Pretrained YOLO (80 kategoria tudasa)
         |
Fine-tuning: csak az utolso retegeket tanitod ujra
a sajat annotalt kepeiddel (100-1000 kep eleg)
         |
Sajat modell ami mar az egyedi kategoriadat is felismeri
```

Ez a **transfer learning** -- a meglevo tudast megtartod, csak a veget igazitod az uj feladathoz.

---

## Fontosabb fogalmak

| Fogalom | Mit jelent |
|---|---|
| **Model** | A betanitott neuralis halo (mentett sulyok) |
| **Training** | A tanitasi folyamat -- adatokon fut, sulyokat allit |
| **Inference** | Eles hasznalat -- uj adaton futtatod a modellt |
| **Epoch** | Az osszes training adat egyszer vegigment |
| **Batch** | Egyszerre feldolgozott mintak szama |
| **Loss** | Hiba merteke -- ezt minimalizalja a training |
| **Overfitting** | "Bemagolta" a training adatot, ujra nem altalanosit |
| **Dataset** | Tanitoanyag -- kepek + annotaciok |
| **Bounding box** | Felismert objektum kore huzott teglalap |
| **Confidence** | Bizonyossag merteke (0.94 = 94%) |
| **Pretrained** | Mar betanitott modell, amit tovabb lehet finomhangolni |

---

## Kapcsolodo

- ML inference pipeline agent-kent automatizalva
- ML eredmenyek beillesztese automatizalt workflow-ba
