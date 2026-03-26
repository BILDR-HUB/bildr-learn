---
tags:
  - eszkoz
  - halozat
  - vpn
datum: 2026-03-06
szint: "🧱 Scout"
kapcsolodo:
  - "[[foundations/halozatok-es-ip-cimek|Halozatok es IP cimek]]"
  - "[[foundations/bash-es-linux-parancssor|Bash es Linux parancssor]]"
  - "[[cloud/docker-alapok|Docker alapok]]"
---

# Tailscale

**Kategória:** `hálózat` / `VPN`
**URL:** https://tailscale.com
**Ár/Terv:** Ingyenes (3 user, 100 eszköz) / Personal Pro ($48/év)

---

## Mi ez és mire jó?

> [!tldr] Egy mondatban
> A Tailscale úgy köti össze a gépeidet, mintha egy wifi hálózaton lennének — bárhol is vannak fizikailag.

### A probléma

Van a MacBookod és egy VPS (Virtual Private Server) valahol felhőben ([[cloud/railway|Railway]], Hetzner, stb.). Ha SSH-val akarsz csatlakozni:

```
MacBook → internet → VPS publikus IP:22
```

Ez működik, de:
- A VPS **22-es portja nyitva van az egész világnak** — bárki próbálkozhat betörni
- Ha a VPS-nek nincs fix publikus IP-je (vagy megváltozik), szívás
- Ha otthonról egy másik szolgáltatást is el akarsz érni a VPS-en (pl. adatbázis a 5432-es porton), azt is ki kell nyitnod → biztonsági kockázat

### A megoldás: Tailscale

A Tailscale egy **privát hálózatot** (tailnet) hoz létre a gépeid között. Telepíted mindkét gépre, és utána úgy viselkednek, mintha egy helyi hálózaton lennének:

```mermaid
graph LR
    A[MacBook<br/>100.64.0.1] <-->|Tailscale<br/>titkosított alagút| B[VPS<br/>100.64.0.2]
    style A fill:#e8e2da,stroke:#333
    style B fill:#e8e2da,stroke:#333
```

Minden gép kap egy **100.x.x.x** Tailscale IP-t. Ez a tiéd, nem változik, és kívülről nem érhető el.

### Mi az a VPN?

A **VPN** (Virtual Private Network) egy titkosított alagutat hoz létre két pont között az interneten keresztül. Olyan mintha egy titkos csövet húznál a két géped között — az adatok ezen mennek át, senki más nem látja.

A Tailscale egy **modern VPN** ami:
- Nem kell hozzá szerver beállítás (a régi VPN-eknél, pl. OpenVPN, saját szervert kellett üzemeltetni)
- Peer-to-peer: a gépek közvetlenül beszélnek egymással (nem egy központi szerveren keresztül)
- WireGuard protokollt használ (gyors, modern, biztonságos)

---

## Szcenárió: MacBook + VPS

### Tailscale nélkül

```
MacBook                          VPS (pl. Hetzner)
                                 Publikus IP: 65.108.42.100
   │                                │
   │   ssh user@65.108.42.100       │
   │ ──────────────────────────────>│ :22 NYITVA (bárki eléri!)
   │                                │ :5432 NYITVA? (adatbázis)
   │                                │ :3000 NYITVA? (app)
```

**Problémák:**
- Portok nyitva a világnak → támadási felület
- IP cím változhat
- Minden szolgáltatáshoz külön port forwarding kell

### Tailscale-lel

```
MacBook                          VPS (pl. Hetzner)
Tailscale IP: 100.64.0.1        Tailscale IP: 100.64.0.2
   │                                │
   │   ssh user@100.64.0.2         │
   │ ─────── titkosított alagút ──>│ :22 csak Tailscale-ről érhető el
   │                                │ :5432 csak Tailscale-ről
   │                                │ :3000 csak Tailscale-ről
   │                                │
   │   Publikus portok: MIND ZÁRVA  │
```

**Előnyök:**
- **Nem kell portot nyitni** — a VPS tűzfalában mindent bezársz, csak a Tailscale forgalmat engeded
- **Fix IP** — a `100.64.0.2` nem változik, akkor sem ha a VPS publikus IP-je igen
- **Minden szolgáltatás elérhető** — adatbázis, app, bármi, anélkül hogy kinyitnád az internetre

---

## Setup — lépésről lépésre

### 1. Regisztráció

https://login.tailscale.com — GitHub vagy Google fiókkal.

### 2. Telepítés MacBookra

```bash
brew install --cask tailscale
```

Megnyitod az appot → bejelentkezel → kész, a MacBook rajta van a tailneten.

### 3. Telepítés VPS-re (Ubuntu/Debian)

```bash
# SSH-val csatlakozol a VPS-re a hagyományos módon (utoljára!)
ssh user@65.108.42.100

# Tailscale telepítése
curl -fsSL https://tailscale.com/install.sh | sh

# Tailscale indítása
sudo tailscale up

# Kiír egy linket — kattints rá, bejelentkezel, jóváhagyod
```

### 3. Ellenőrzés

```bash
# Bármelyik gépen:
tailscale status
```

Kiírja az összes géped + Tailscale IP-jüket:

```
100.64.0.1    macbook         user@  macOS   -
100.64.0.2    vps-hetzner     user@  linux   -
```

### 4. SSH átállítása Tailscale IP-re

Ezentúl:

```bash
ssh user@100.64.0.2    # Tailscale IP, nem publikus
```

> [!tip] SSH config
> Add hozzá az `~/.ssh/config`-hoz, hogy ne kelljen megjegyezni:
> ```
> Host vps
>     HostName 100.64.0.2
>     User myuser
> ```
> Utána csak: `ssh vps`

### 5. VPS tűzfal bezárása

Ha a Tailscale működik, a VPS-en bezárhatod az összes publikus portot:

```bash
# UFW tűzfal beállítása a VPS-en
sudo ufw default deny incoming
sudo ufw allow in on tailscale0    # csak Tailscale forgalom engedélyezve
sudo ufw enable
```

> [!warning] Figyelem
> Mielőtt bezárod a tűzfalat, győződj meg hogy a Tailscale SSH működik! Különben kizárod magad.

---

## Best Practices

### Biztonság

- **Zárd be a publikus portokat** a VPS-en a Tailscale telepítés után
- **MagicDNS** bekapcsolása — ezzel név alapján is eléred a gépeidet: `ssh user@vps-hetzner` IP cím helyett
- **Key expiry** beállítása a Tailscale admin panelen — hogy a kulcsok lejárjanak és meg kelljen újítani

### Teljesítmény

- A Tailscale peer-to-peer: a forgalom **nem megy keresztül Tailscale szerverein**, közvetlenül a két géped között megy
- WireGuard alapú → minimális overhead, szinte észrevehetetlen a lassulás

---

## Gyakori minták / Használati esetek

### 1. SSH VPS-re Tailscale-en keresztül

```bash
ssh user@100.64.0.2
# vagy MagicDNS-szel:
ssh user@vps-hetzner
```

Nem kell publikus IP, nem kell nyitott port.

### 2. Lokális fejlesztés + VPS adatbázis

A VPS-en fut egy PostgreSQL a `5432`-es porton. Tailscale-lel:

```bash
# .env.local a Next.js projektedben:
DATABASE_URL=postgresql://user:pass@100.64.0.2:5432/mydb
```

Az adatbázis nincs kinyitva az internetre, csak a Tailscale hálózatodon érhető el.

### 3. Több szerver összekötése

Ha több VPS-ed van (pl. backend + adatbázis külön szerveren):

```
MacBook (100.64.0.1) ←→ Backend VPS (100.64.0.2) ←→ DB VPS (100.64.0.3)
                         Mind egy tailneten, titkosítva
```

---

## Buktatók és hibák amiket elkerülj

- **Ne zárd be a tűzfalat mielőtt a Tailscale SSH működik** — kizárod magad a VPS-ről
- **`tailscale up` kell minden újraindítás után** a VPS-en (hacsak nem `--operator` flaggel telepíted)
- **Régi SSH session-ök** a publikus IP-n megmaradnak a tűzfal bezárásáig — ne felejtsd el átállni Tailscale IP-re

---

## Mikor kapcsold be?

> [!info] Fontos: Tailscale nem full-tunnel VPN
> Alapból **csak a saját eszközeid közötti forgalmat** titkosítja — az internet traffic **nem** megy át rajta (hacsak nem állítasz be exit node-ot). Ezért sokkal kevésbé "tolakodó" mint egy klasszikus VPN.

### Be kell kapcsolni

- **Publikus WiFi** (kávézó, hotel, repülő) — a hálózat nem megbízható, Tailscale védi az eszközeid közötti kommunikációt
- **Remote desktop / SSH** saját gépre vagy VPS-re
- **Céges belső rendszerek** elérése (admin panel, belső DB, stb.)
- **Otthoni NAS / szerver** elérése távolról

### Nem kell bekapcsolni

- Otthon, saját routered mögött — a hálózat megbízható
- Normál böngészés, YouTube, Spotify — Tailscale úgysem érinti ezeket
- Ha nem érsz el semmit a tailnetenedről

### Exit node nélkül: hagyd bekapcsolva 0-24

A Tailscale WireGuard-alapú, peer-to-peer — ha nincs exit node konfigurálva, szinte **semmi overhead** nincs. Bekapcsolva hagyhatod folyamatosan, és mindig elérheted a tailnet eszközeidet, amikor szükséged van rájuk.

> [!tip] Praktikus beállítás
> Macen beállíthatod, hogy induláskor automatikusan elinduljon: **Tailscale → Preferences → Launch at Login**

---

## Hasznos parancsok / kódrészletek

```bash
tailscale status         # gépek listája + IP-k
tailscale ip             # saját Tailscale IP
tailscale ping vps       # ping egy másik gépet a tailneten
tailscale up             # csatlakozás a tailnethez
tailscale down           # lecsatlakozás
tailscale ssh user@vps   # Tailscale SSH (kulcs nélkül, Tailscale auth-tal)
```

---

## Hasznos linkek

- Docs: https://tailscale.com/kb
- Dashboard: https://login.tailscale.com/admin
- Státusz oldal: https://status.tailscale.com

---

## Kapcsolódó

- [[foundations/halozatok-es-ip-cimek|Halozatok es IP cimek]] — IP alapok, portok, DNS
- [[foundations/bash-es-linux-parancssor|Bash es Linux parancssor]] — SSH, tűzfal parancsok
- [[cloud/docker-alapok|Docker alapok]] — Docker + Tailscale kombó
