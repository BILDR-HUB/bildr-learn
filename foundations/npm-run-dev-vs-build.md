---
tags:
  - nodejs
  - nextjs
kapcsolodo:
  - "[[frontend/nextjs|Next.js]]"
  - "[[cloud/cloudflare|Cloudflare]]"
  - "[[cloud/vercel|Vercel]]"
datum: 2026-02-08
szint: "🧱 Scout"
---

# npm run dev vs build

## Mi a különbség?

Ket teljesen különböző mod, ket különböző celra:

| | `npm run dev` | `npm run build` |
|---|---|---|
| **Cel** | Fejlesztes kozben tesztelni | Éles deploy elokeszitese |
| **Mit csinál** | Hot-reload dev szerver (localhost) | Legeneralja az összes statikus fájlt |
| **Rendereles** | Lazy -- csak amit megnyitsz a bongeszben | Eager -- minden oldalt elore renderel |
| **Output** | Memoriaban, nem ir fájlt | `out/` vagy `.next/` mappa |
| **Optimalizacio** | Nincs (gyorsabb fejlesztes) | Minify, tree-shake, kep optimalizacio |
| **Sebesseg** | Azonnal indul | Percekig tart (merettol fugg) |
| **Hibak** | Csak a megnyitott oldalnal jelennek még | Minden hiba egyszerre kiderul |

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
- Kódolas kozben, lokálisan
- Új feature tesztelése
- Dizajn finomhangolas
- **Nem kell utána deploy**

### `npm run build`
- Deploy elott ([[cloud/cloudflare|Cloudflare]], [[cloud/vercel|Vercel]])
- CI/CD pipeline-ban
- Teljes hiba-ellenőrzes (minden oldal renderelodik)
- **Az eredmenyet toltod fel az elo szerverre**

---

## Next.js specifikus: `output: 'export'`

Ha a `next.config.ts`-ben `output: 'export'` van beállítva, a `npm run build`:

1. Minden oldalt **statikus HTML-le** renderel
2. Az `out/` mappaba menti
3. Ezt az `out/` mappat toltod fel Cloudflare Pages-re

Ez a **Static Site Generation (SSG)** -- nincs szerver, csak fájlok.

---

## Gyakorlati tipp

Ha a `dev` működik de a `build` nem, az általában azert van, mert:
- Egy hiba csak akkor derul ki, amikor **minden** oldal renderelodik
- Env változó hianyzik build-idoben
- Egy dependency nem kompatibilis a Node verzióval

A `dev` ezeket elfedi, mert lazy -- csak a megnyitott oldalt rendereli.

---

## Kapcsolodo

- [[frontend/nextjs|Next.js]]
- [[cloud/cloudflare|Cloudflare]]
- [[cloud/vercel|Vercel]] -- deploy platform, ahol a `npm run build` eredmenye fut
