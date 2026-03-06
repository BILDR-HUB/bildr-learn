---
tags:
  - nodejs
  - nextjs
kapcsolodo:
  - "[[frontend/nextjs|Next.js]]"
  - "[[cloud/cloudflare|Cloudflare]]"
  - "[[cloud/vercel|Vercel]]"
datum: 2026-02-08
szint: "🧱 Brick"
---

# npm run dev vs build

## Mi a kulonbseg?

Ket teljesen kulonbozo mod, ket kulonbozo celra:

| | `npm run dev` | `npm run build` |
|---|---|---|
| **Cel** | Fejlesztes kozben tesztelni | Eles deploy elokeszitese |
| **Mit csinal** | Hot-reload dev szerver (localhost) | Legeneralja az osszes statikus fajlt |
| **Rendereles** | Lazy -- csak amit megnyitsz a bongeszben | Eager -- minden oldalt elore renderel |
| **Output** | Memoriaban, nem ir fajlt | `out/` vagy `.next/` mappa |
| **Optimalizacio** | Nincs (gyorsabb fejlesztes) | Minify, tree-shake, kep optimalizacio |
| **Sebesseg** | Azonnal indul | Percekig tart (merettol fugg) |
| **Hibak** | Csak a megnyitott oldalnal jelennek meg | Minden hiba egyszerre kiderul |

---

## Vizualisan

```
npm run dev:
  Te megnyitod -> /blog/cikk-1 -> rendereli -> megmutatja
                 /blog/cikk-2 -> nem nyitottad meg -> nem rendereli
                 Gyors, de nem teljes

npm run build:
  Vegigmegy mindenen -> /           -> renderel
                        /blog        -> renderel
                        /blog/cikk-1 -> renderel
                        /blog/cikk-2 -> renderel
                        ... mind az 58 oldal
                        -> out/ mappaba menti -> kesz a deploy-ra
```

---

## Mikor melyiket?

### `npm run dev`
- Kodolas kozben, lokalisan
- Uj feature tesztelese
- Dizajn finomhangolas
- **Nem kell utana deploy**

### `npm run build`
- Deploy elott ([[cloud/cloudflare|Cloudflare]], [[cloud/vercel|Vercel]])
- CI/CD pipeline-ban
- Teljes hiba-ellenorzes (minden oldal renderelodik)
- **Az eredmenyet toltod fel az elo szerverre**

---

## Next.js specifikus: `output: 'export'`

Ha a `next.config.ts`-ben `output: 'export'` van beallitva, a `npm run build`:

1. Minden oldalt **statikus HTML-le** renderel
2. Az `out/` mappaba menti
3. Ezt az `out/` mappat toltod fel Cloudflare Pages-re

Ez a **Static Site Generation (SSG)** -- nincs szerver, csak fajlok.

---

## Gyakorlati tipp

Ha a `dev` mukodik de a `build` nem, az altalaban azert van, mert:
- Egy hiba csak akkor derul ki, amikor **minden** oldal renderelodik
- Env valtozo hianyzik build-idoben
- Egy dependency nem kompatibilis a Node verzioval

A `dev` ezeket elfedi, mert lazy -- csak a megnyitott oldalt rendereli.

---

## Kapcsolodo

- [[frontend/nextjs|Next.js]]
- [[cloud/cloudflare|Cloudflare]]
- [[cloud/vercel|Vercel]] -- deploy platform, ahol a `npm run build` eredmenye fut
