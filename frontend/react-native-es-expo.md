---
tags: [frontend, react-native, expo, mobile]
datum: 2026-03-26
szint: "🧱 Brick"
kapcsolodo:
  - "[[frontend/react|React]]"
  - "[[frontend/nextjs|Next.js]]"
  - "[[cloud/vercel|Vercel]]"
---

## Mi ez és mire jó?

> [!tldr] Egy mondatban
> Ha tudsz [[frontend/react|React]]-et, tudsz mobil appot építeni. A React Native a framework, az Expo a toolchain ami praktikussá teszi - build, deploy, OTA update, natív API hozzáférés.

A **React Native** lehetővé teszi, hogy egyetlen [[foundations/typescript-vs-python|TypeScript]]/React kódbázisból iOS és Android appot készíts - nem webes wrapper (mint Cordova), hanem **natív UI komponensek** renderelődnek.

Az **Expo** a React Native feletti platform, ami megoldja a fájdalmas részeket: build konfiguráció, natív modulok kezelése, app store submit, over-the-air frissítések. Expo nélkül RN fejlesztés = Xcode + Android Studio + manuális konfig.

**Analógia:** React Native = [[frontend/nextjs|Next.js]] a mobilra, Expo = [[cloud/vercel|Vercel]] a React Native-hoz.

---

## Mikor melyik megközelítés?

| Szempont | **RN + Expo** | **Flutter** | **Natív (Swift/Kotlin)** | **PWA** |
|----------|---------------|-------------|--------------------------|------|
| **Nyelv** | TypeScript/React | Dart | Swift / Kotlin | TypeScript/React |
| **Tanulási görbe** | Alacsony (React dev) | Közepes (új nyelv) | Magas (2 platform) | Alacsony |
| **Teljesítmény** | Jó (90%) | Nagyon jó (95%) | Legjobb (100%) | Közepes |
| **App Store** | Igen | Igen | Igen | Nem |
| **Hardver hozzáférés** | Szinte teljes | Teljes | Teljes | Korlátozott |
| **Kód megosztás web-bel** | Magas (React) | Alacsony | Nincs | 100% |
| **OTA update** | EAS Update | Nincs beépítve | Nincs | Automatikus |
| **Ideális** | React csapat, gyors MVP | Perf-kritikus, custom UI | Max perf, OS-szintű | Egyszerű app, nincs store |

> [!tip] Döntési fa
> - **Kell App Store jelenlét?** - Nem: PWA elég
> - **Van React tapasztalat?** - Igen: React Native + Expo
> - **Perf-kritikus (játék, animáció)?** - Flutter vagy natív
> - **Mindkét platform (iOS + Android)?** - RN + Expo (egy kódbázis)

---

## Expo kulcs fogalmak

| Fogalom | Mi ez | Miért fontos |
|---------|-------|-------------|
| **Expo Router** | File-based routing | Ugyanaz mint Next.js `app/` - ismerős minta |
| **EAS Build** | Cloud build service | Nem kell lokális Xcode/Android Studio |
| **EAS Submit** | App Store feltöltés | CLI-ből submit App Store-ba és Google Play-re |
| **EAS Update** | OTA frissítés | Kód push App Store review nélkül (JS bundle) |
| **Expo Modules** | Natív API bridge | Kamera, értesítések, fájlrendszer - előre csomagolva |
| **Expo Go** | Fejlesztői app | Telefonra telepíted, azonnal tesztelsz QR kóddal |

---

## Setup gyorsan

```bash
# Új projekt (Expo Router template)
npx create-expo-app@latest my-app --template tabs

# Fejlesztés (Expo Go-val telefonon)
npx expo start

# EAS Build (production)
eas build --platform all
eas submit --platform all
```

---

## AI-natív fejlesztés

A React Native + Expo fejlesztés kiválóan párosul AI coding tool-okkal, mert a React tudásod közvetlenül alkalmazható. Claude Code érti az Expo Router file-based routing-ot, az EAS konfigurációt és a natív modulok használatát - a webes React tapasztalatodra építve mobilra is gyorsan tudsz fejleszteni.

> [!tip] Hogyan használd AI-val
> - *"Hozz létre egy Expo Router projektet tabs navigációval, TypeScript-tel és NativeWind (Tailwind) styling-gal"*
> - *"Írj egy kamera képernyőt expo-camera-val ami fényképet készít és elmenti a galériába"*
> - *"Konfiguráld az EAS Build-et és az EAS Update-et ehhez az Expo projekthez - development és production profil"*

---

## Kapcsolódó

- [[frontend/react|React]] - ugyanaz a komponens modell, ugyanaz a gondolkodásmód
- [[frontend/nextjs|Next.js]] - Expo Router ugyanúgy file-based mint a Next.js App Router
- [[cloud/vercel|Vercel]] - Expo az a React Native-hoz, mint a Vercel a Next.js-hez
