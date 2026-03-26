---
tags:
  - eszkoz
  - cli
datum: 2026-03-26
szint: "🧱 Brick"
kapcsolodo:
  - "[[foundations/linux|Linux]]"
  - "[[cloud/docker-alapok|Docker]]"
  - "[[cloud/cloudflare|Cloudflare]]"
---

# FFMPEG

**Kategória:** `dev tool` / `CLI` (multimédia feldolgozás)
**URL:** https://ffmpeg.org
**Ár:** Ingyenes, open source (LGPL/GPL)

---

## Mi ez és mire jó?

Az **FFMPEG** egy **parancssoros multimédia feldolgozó eszköz** - videókat és hangfájlokat konvertál, vág, tömörít, egyesít, és szinte bármit csinál velük amit el tudsz képzelni. A legtöbb videó- és hangfeldolgozó szoftver (YouTube, VLC, OBS, HandBrake) a háttérben FFMPEG-et használ.

> [!tldr] Egy mondatban
> Az FFMPEG a "svájci bicska" videó- és hangfájlokhoz - a terminálból bármilyen médiafájl konverziót, vágást, tömörítést megold egyetlen paranccsal.

### Mire használod fejlesztőként?

| Feladat | Példa |
|---------|-------|
| **Videó konvertálás** | MP4 - WebM (böngészőhöz optimalizált) |
| **Hang kinyerés** | Videóból MP3/AAC hang kinyerése |
| **Tömörítés** | 4K videó méretcsökkentése minőségvesztés nélkül |
| **Képsorozat - videó** | Screenshot-okból timelapse |
| **Vágás** | Videó elejéről/végéről levágás |
| **GIF készítés** | Videóból animált GIF (demó, docs) |
| **Thumbnail generálás** | Videó adott frame-jének kimentése képként |
| **Streaming** | RTMP stream kezelés, HLS szegmentálás |
| **Formátum info** | `ffprobe` - médiafájl metaadatok lekérdezése |

---

## Telepítés

Az FFMPEG rendszerszintű CLI tool - brew-vel telepíted:

```bash
brew install ffmpeg
```

> [!warning] [[cloud/docker-alapok|Docker]]-ben explicit kell
> Ne feltételezd, hogy a szerveren lesz `ffmpeg`. Ha Docker-ben kell, a `Dockerfile`-ba tedd: `RUN apk add ffmpeg`.

---

## Gyakori feladatok

### 1. Videó konverzió és tömörítés

| Mit akarsz | FFMPEG logika |
|------------|--------------|
| MP4 - WebM | Formátum konverzió, VP9 codec |
| 4K - 1080p | Skálázás (`scale`), kisebb fájlméret |
| Minőség csökkentés (méret) | CRF érték állítás (18=jó, 28=kisebb) |
| Gyors konverzió (minőség rovására) | `ultrafast` preset |
| Legjobb minőség (lassú) | `veryslow` preset |

> [!info] CRF (Constant Rate Factor)
> A CRF a minőség-méret egyensúly fő paramétere. **Alacsonyabb = jobb minőség, nagyobb fájl.** A 18-23 tartomány a "vizuálisan veszteségmentes" zóna. 28+ felett már látható a minőségromlás.

### 2. Hang kinyerés és konverzió

Videóból hang kinyerése, podcast/meeting felvétel konvertálása, WAV - MP3 tömörítés - mind FFMPEG terület.

### 3. GIF és thumbnail generálás

Dokumentációhoz, demo videókhoz, PR review-hoz hasznos: rövid videóklipből GIF-et vagy statikus thumbnail-t generálni.

### 4. Batch feldolgozás

Amikor egy egész mappa videót kell konvertálni - shell script + FFMPEG kombináció:

```bash
# Minden .mov fájl konvertálása .mp4-re az aktuális mappában
for f in *.mov; do ffmpeg -i "$f" "${f%.mov}.mp4"; done
```

---

## FFMPEG + AI workflow

Az FFMPEG parancsok tökéletes feladat az AI-nak - a szintaxis komplex, rengeteg flag van, és a legtöbb fejlesztő nem tartja fejben a paramétereket.

**Így használd:**
1. Mondd el [[toolbox/claude-code-projekt-setup|Claude Code]]-nak **mit akarsz** (nem hogyan)
2. Az AI megírja az FFMPEG parancsot a helyes codec-kel, preset-tel, filter-rel
3. Futtasd a terminálban

> [!tip] Az FFMPEG az egyik legjobb "AI-delegálható" CLI tool
> Senki nem tartja fejben az összes FFMPEG flag-et. A legjobb workflow: természetes nyelven leírod mit akarsz, és az AI megírja a parancsot. Ez sokkal gyorsabb mint a docs böngészése.

---

## Buktatók

- **Codec kompatibilitás** - nem minden codec támogatott minden konténerben. MP4-hez H.264/H.265, WebM-hez VP9/AV1 kell
- **Felülírás figyelmeztetés** - alapból FFMPEG megkérdezi, hogy felülírja-e a kimeneti fájlt. Automatizálásnál a `-y` flag kell (auto overwrite)
- **Hang nélküli output** - ha csak videó codec-et adsz meg, az FFMPEG elhagyhatja a hangot. Mindig jelezd mindkét stream-et
- **Lassú konverzió** - nagy fájloknál a `preset` paraméter drámaian befolyásolja a sebességet. `ultrafast` 10x gyorsabb mint `veryslow`, de nagyobb fájlt ad
- **[[cloud/cloudflare|Cloudflare]] Workers-ön NEM fut** - az FFMPEG natív bináris, edge/serverless környezetben nem érhető el. Railway-en vagy saját szerveren kell futtatni

---

## Hogyan használd promptban?

- *"FFMPEG: konvertáld ezt az MP4-et WebM-re VP9 codec-kel, 1080p-re skálázva, CRF 28-cal (webes optimalizálás)"*
- *"FFMPEG batch: az egész mappa MP4-jét tömörítsd le 50%-ra - shell script kellene ami végigmegy a fájlokon"*
- *"Videóból kinyerem a hangot MP3-ként, de csak az első 30 másodpercet - FFMPEG parancs?"*

> [!tip] Add meg a célt és a kontextust
> Az FFMPEG-nél a "mire" fontosabb mint a "hogyan" - jelezd a promptban: *webre*, *e-mailhez csatolás*, *archív minőség*, *social media*. Ez segít az AI-nak a helyes codec/preset/CRF kombinációt választani.
