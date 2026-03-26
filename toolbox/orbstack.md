---
tags:
  - eszkoz
  - container
datum: 2026-03-26
szint: "🌱 Newcomer"
kapcsolodo:
  - "[[cloud/docker-alapok|Docker]]"
  - "[[cloud/kubernetes-bevezeto|Kubernetes]]"
---

# OrbStack

**Kategória:** `dev tool` / `container`
**URL:** https://orbstack.dev

---

## Mi ez és mire jó?

Gyors, könnyű Docker Desktop alternatíva macOS-re. Azonnali indulás, alacsony memóriahasználat, beépített [[cloud/kubernetes-bevezeto|Kubernetes]] - gyakorlatilag drop-in replacement, csak jobb.

---

## Miért OrbStack?

A Docker Desktop macOS-en lassú, memóriaéhes és néha instabil. Az OrbStack ugyanazt tudja, de:

- **Azonnali indulás** - nem kell várni 30-60 másodpercet boot-ra
- **~50%-kal kevesebb RAM** - nem zabálja a memóriát háttérben
- **Natív macOS integráció** - file sharing gyors, hálózatkezelés egyszerű (`.orb.local` domain-ek)
- **Beépített Kubernetes** - egy kattintás, működik, nem kell minikube/kind

---

## Összehasonlítás

| Eszköz | Előny | Hátrány |
|--------|-------|---------|
| **OrbStack** | Gyors, könnyű, K8s beépítve | Csak macOS, fizetős (free tier van) |
| **Docker Desktop** | Cross-platform, hivatalos | Lassú, memóriaéhes, licenc korlátok |
| **Colima** | Ingyenes, nyílt forráskódú | CLI-only, lassabb, nincs GUI |
| **Rancher Desktop** | K8s-fókuszú, ingyenes | Nehezebb, kevésbé stabil macOS-en |

---

## Mikor használd?

- **Bármilyen [[cloud/docker-alapok|Docker]]/K8s munka macOS-en** - nincs ok Docker Desktop-ot használni helyette
- Kubernetes tanulás/tesztelés lokálisan
- Multi-container fejlesztés (`docker compose` ugyanúgy működik)

---

## Hogyan használd promptban

> [!tip] Claude Code kontextus
> Az OrbStack teljesen transzparens - `docker` és `kubectl` parancsok változtatás nélkül működnek [[toolbox/claude-code-projekt-setup|Claude Code]] session-ökben. Nem kell külön konfigurálni, az OrbStack a háttérben kezeli.

---

## Telepítés

```bash
brew install orbstack
```

Ennyi. Az OrbStack automatikusan átveszi a Docker CLI-t. Ha volt Docker Desktop, utána nyugodtan törölhető.
