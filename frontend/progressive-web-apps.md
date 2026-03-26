---
tags:
  - frontend
  - pwa
  - mobile
datum: 2026-03-26
szint: "🧱 Scout"
kapcsolodo:
  - "[[frontend/nextjs|Next.js]]"
  - "[[frontend/react-vs-spa-vs-preact|React]]"
  - "[[cloud/vercel|Vercel]]"
  - "[[cloud/cloudflare|Cloudflare]]"
---

# Progressive Web Apps (PWA)

> [!tldr] Miért releváns
> A Progressive Web App lehetővé teszi, hogy a weboldalad installálható legyen, offline működjön, és push notification-öket küldjön - mindezt natív app fejlesztés és App Store nélkül.

---

## Kulcs tanulságok

- A PWA **nem egy framework** - három webes technológia kombinációja: Service Worker, Web App Manifest, HTTPS
- A felhasználó a böngészőből "telepítheti" a home screen-re - saját ikon, splash screen, fullscreen mód
- Az offline működés a Service Worker cache-elésén múlik - te döntöd el mit cache-elsz és meddig
- 2025-től a Chrome és Edge **már nem követeli meg a Service Worker-t** az install prompt-hoz - elég a manifest
- Az Apple (Safari/iOS) támogatás **javult, de korlátozott** - push notification csak iOS 16.4+, és az EU-ban külső böngészők is telepíthetnek PWA-t

---

## A 3 fő komponens

| Komponens | Mi ez | Mit csinál |
|-----------|-------|------------|
| **Web App Manifest** | `manifest.json` fájl | Megmondja a böngészőnek: app neve, ikonok, színek, display mód (standalone/fullscreen), start URL |
| **Service Worker** | JavaScript fájl, a háttérben fut | Elfogja a hálózati kéréseket, cache-el, offline működést biztosít, push notification-öket fogad |
| **HTTPS** | Titkosított kapcsolat | Kötelező - Service Worker csak HTTPS-en regisztrálható (localhost kivétel) |

### Service Worker - a lényeg

A Service Worker egy **proxy a böngésző és a hálózat között**. Minden fetch kérést elfoghat, és eldöntheti: cache-ből szolgálja ki, hálózatról kéri le, vagy kombinál (cache first, network fallback).

Stratégiák:

| Stratégia | Logika | Mikor használd |
|-----------|--------|----------------|
| **Cache First** | Cache-ből, ha nincs -> hálózat | Statikus asset-ek (képek, CSS, JS) |
| **Network First** | Hálózat, ha nincs -> cache | API válaszok, friss adat |
| **Stale While Revalidate** | Cache-ből azonnal + háttérben frissít | Legjobb UX - gyors, de friss |

---

## Mikor PWA vs natív app vs [[frontend/react-vs-spa-vs-preact|React]] Native vs Electron

| Szempont | PWA | React Native + Expo | Natív app | Electron |
|----------|-----|------|-----------|---------|
| **Telepítés** | Böngészőből, azonnal | App Store | App Store review (napok) | .dmg / .exe letöltés |
| **Frissítés** | Automatikus (SW) | OTA (EAS Update) | Store review kell | Auto-updater |
| **Offline** | Service Worker cache | Natív storage | Natív storage | Node.js fájlrendszer |
| **Push notification** | Igen (iOS korlátozott) | Teljes | Teljes | Igen |
| **Hardver hozzáférés** | Korlátozott | Szinte teljes | Teljes | Teljes (Node.js) |
| **Fejlesztési költség** | Alacsony | Közepes | Magas | Közepes |
| **App Store jelenlét** | Nem | Igen | Igen | Nem |
| **Kód megosztás web-bel** | 100% (web tech) | Magas (React) | Nincs | Közepes |

> [!tip] Döntési fa: mobil app megközelítés
> 1. **Kell App Store jelenlét?** -> Nem: PWA elég
> 2. **Kell offline + push + kamera?** -> PWA tudja (korlátokkal)
> 3. **Kell NFC, Bluetooth, HealthKit?** -> React Native + Expo vagy natív
> 4. **Van React tapasztalat?** -> React Native + Expo
> 5. **Perf-kritikus (játék, komplex animáció)?** -> Natív (Swift/Kotlin)

**Ökölszabály:** Ha nem kell App Store jelenlét, és nem kell mély hardver hozzáférés (NFC, advanced Bluetooth, HealthKit) - a PWA szinte mindig elég. Ha kell store jelenlét de React-ben dolgozol -> React Native + Expo.

---

## [[frontend/nextjs|Next.js]] + PWA setup

A Next.js-hez a `next-pwa` vagy `@ducanh2912/next-pwa` csomagok a legelterjedtebbek. A lényeg:

1. Manifest fájl a `public/manifest.json`-ban
2. Service Worker generálás build time-ban (Workbox alapú)
3. `<link rel="manifest">` a `<head>`-ben

A legtöbb esetben a **Stale While Revalidate** stratégia a legjobb Next.js-hez - a statikus oldalak cache-ből jönnek, az API route-ok network first.

---

## AI-natív fejlesztés

A PWA konfiguráció (manifest.json, Service Worker, cache stratégia) boilerplate-heavy, és az AI coding tool-ok kiválóan generálják. Claude Code-dal a teljes PWA setup-ot - manifest, Service Worker stratégiák, offline fallback oldal - percek alatt elkészítheted.

> [!tip] Hogyan használd AI-val
> - *"Adj PWA támogatást ehhez a Next.js projekthez - manifest.json, next-pwa konfiguráció, offline fallback oldal"*
> - *"Írj egy Service Worker-t Stale While Revalidate stratégiával az API route-okhoz és Cache First-tel a statikus asset-ekhez"*
> - *"Generálj PWA ikonokat és splash screen-eket a meglévő logóból, és konfiguráld a manifest.json-t"*

---

## Kapcsolódó

- [[frontend/nextjs|Next.js]] - Next.js + PWA setup
- [[frontend/react-vs-spa-vs-preact|React]] - PWA React-tel a leggyakoribb
- [[cloud/vercel|Vercel]] - PWA deployment Vercel-en (Service Worker a CDN edge-ről)
- [[cloud/cloudflare|Cloudflare]] - Cloudflare Workers + PWA cache stratégia
