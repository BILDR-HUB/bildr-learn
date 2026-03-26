---
tags:
  - eszkoz
  - design
datum: 2026-03-26
szint: "🌱 Newcomer"
kapcsolodo:
  - "[[frontend/react-vs-spa-vs-preact|React]]"
  - "[[_moc/moc-ai-tooling|MOC - AI Tooling]]"
---

# Figma

**Kategória:** `design tool` (UI/UX design - böngészőalapú)
**URL:** https://figma.com
**Ár:** Starter: ingyenes (3 Figma + 3 FigJam fájl), Professional: $15/hó/szerkesztő

---

## Mi ez és mire jó?

A **Figma** egy böngészőalapú design tool - az iparági standard, amit designerek használnak UI/UX tervezéshez. Fejlesztőként nem te designolsz benne, hanem **inspektálod a design-t**: méreteket, színeket, spacing-et, tipográfiát másolsz ki belőle.

A Figma a "design forrás igazsága" - a designer itt tervez, te pedig Dev Mode-ban kiveszed a CSS-t, spacing-et, színkódokat és komponens struktúrát, amit kódra fordítasz.

### Fejlesztőknek releváns funkciók

| Funkció | Mire jó neked |
|---------|---------------|
| **Dev Mode** | Inspect panel: CSS/spacing/color értékek másolása, komponens prop-ok |
| **Auto Layout** | Flexbox megfelelője: ha a designer auto layout-ot használ, 1:1 Flexbox-ra fordítható |
| **Design Tokens** | Színek, spacing, tipográfia rendszerezve - Tailwind config-ba |
| **Components** | Figma komponensek - [[frontend/react-vs-spa-vs-preact|React]] komponensek - struktúra megértése |
| **Handoff link** | "Share" - fejlesztői link, inspect nézet böngészőben |

### Figma - Kód workflow

```
Designer                          Fejlesztő
────────                          ──────────
1. Designol Figma-ban     →   2. Dev Mode-ban megnyitja
                               3. Inspect: spacing, color, font
                               4. Tailwind class-okra fordítja
                               5. React komponenseket épít
```

---

## Összehasonlítás

| Szempont | **Figma** | Sketch | Penpot |
|----------|----------|--------|--------|
| **Platform** | Böngésző (+ desktop app) | Csak Mac | Böngésző |
| **Ár** | Ingyenes tier van | $12/hó | Ingyenes (open source) |
| **Iparági elfogadottság** | Standard (90%+) | Csökkenő | Növekvő, de kicsi |
| **Dev Mode** | Beépített, kiváló | Plugin-ok kellenek | Inspect van, de limitált |
| **Kollaboráció** | Valós idejű, többen egyszerre | Egyedül (fájl-alapú) | Valós idejű |
| **Plugin ökoszisztéma** | Hatalmas | Közepes | Kicsi |

> [!tip] Mikor melyiket?
> - **Profi projekt, designer csapattal** - Figma (de facto standard)
> - **Open source, self-hosted kell** - Penpot
> - **Sketch** - csak ha a designer ragaszkodik hozzá (legacy)

---

## Dev Mode - amit fejlesztőként használsz

A Dev Mode (Figma jobb felső "Dev Mode" toggle) megmutatja:

| Adat | Példa | Hova fordítod |
|------|-------|---------------|
| Padding/margin | `padding: 16 24` | `p-4 px-6` (Tailwind) |
| Szín | `#3B82F6` | `text-blue-500` vagy custom token |
| Font | `Inter, 16px, 600` | `font-semibold text-base` |
| Border radius | `8px` | `rounded-lg` |
| Komponens név | `Button/Primary/Large` | `<Button variant="primary" size="lg">` |

---

## Hogyan használd promptban?

- *"Ezt a Figma design-t implementáld: [leírás/screenshot] - Tailwind CSS + shadcn-ui komponensekkel"*
- *"A Figma-ban a spacing 8px grid rendszer - konfiguráld a Tailwind config-ot ehhez"*
- *"Design tokens: primary=#3B82F6, radius=8px - shadcn-ui theme beállítás"*

> [!tip] Figma spec-et szövegesen add meg!
> AI nem lát Figma fájlokat. Másold ki a színkódokat, méreteket, font-okat szövegesen, vagy készíts screenshot-ot és írd le a struktúrát. Minél pontosabb a spec, annál jobb a generált kód.
