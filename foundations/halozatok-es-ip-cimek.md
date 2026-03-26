---
tags:
  - halozatok
kapcsolodo:
  - "[[cloud/docker-alapok|Docker alapok]]"
  - "[[foundations/bash-es-linux-parancssor|Bash és Linux parancssor]]"
  - "[[cloud/docker-compose|Docker Compose]]"
datum: 2026-02-08
szint: "🧱 Scout"
---

# Hálózatok és IP cimek

## Összefoglaló

Ahhoz hogy megertsd hogyan kommunikal az app a szerverrel, a szerver az adatbázissal, vagy a Docker konténerek egymassal -- ertened kell az IP cimeket és a hálózatok alapjait. Ez nem bonyolult, de tudni kell mi mi.

## Mi az az IP cim? -- egyszerűen

Képzeld el hogy az internet egy varos. Minden haz (szamitogep, szerver, telefon) kap egy **hazszamot** -- ez az IP cim. Ha valaki üzenetet akar küldeni neked, tudnia kell a hazszamodat.

**IPv4** -- a regi rendszer:
```
192.168.1.100
```
4 szam, pontokkal elvalasztva. Minden szam 0-255 kozott. Összesen ~4.3 milliard lehetseges cim -- ez keves, ezert jott az IPv6.

**IPv6** -- az új rendszer:
```
2001:0db8:85a3::8a2e:0370:7334
```
Sokkal több cim fer bele. Egyelore nem kell vele sokat foglalkozni, de jó tudni hogy letezik.

## Privat vs publikus IP -- miért fontos?

Gondolj egy lakoparkra:
- A lakopark **kapuja** kap egy hazszamot az utcan -- ez a **publikus IP** (a vilag látja)
- A lakasokon belul vannak saját ajtoszamok -- ezek a **privat IP**-k (csak belulrol latszanak)

**Privat tartomanyok** (nem erhetok el az internetrol):

| Tartomany | Hol talalkozol vele | Hasonlat |
|-----------|---------------------|----------|
| `192.168.x.x` | **Otthoni wifi router** -- a telefonod, laptopod ilyet kap | A lakasod ajtoszama |
| `172.16.x.x` -- `172.31.x.x` | **Docker konténerek** -- egymas kozott így beszelgetnek | Szobaszamok egy szallodan belul |
| `10.x.x.x` | **Felhő szolgáltatok** (AWS, Azure belső hálózata) | Irodaszamok egy nagy epuletben |

**Specialis cim:**
| Cim | Mit jelent | Mikor látod |
|-----|------------|-------------|
| `127.0.0.1` (localhost) | **Mindig a saját géped** | `localhost:3000` -- a saját gépen futo app |

## Miért szamit ez fejlesztes kozben?

Amikor deployolsz vagy Docker-t használsz, ezek a helyzetek jonnek elo:

- **"A backend nem eri el az adatbázist"** -> Valószinuleg rossz IP vagy host nevet használsz. Docker-ben a service neve = a hostneve (pl. `db`, nem `localhost`)
- **"Localhostrol megy, production-bol nem"** -> Localhost = a saját géped. A szerveren mas IP van. Környezeti változókban kell megadni
- **"CORS hiba"** -> A frontend mas cimrol probalja elerni a backendet mint amirol az elvarja

> [!tip] Best practice
> Mindig add még a kontextust hogy lokálisan futsz, Docker-ben, vagy production-ben -- mert mas-mas IP/host konfiguráciot kell használni mindegyikben.

## DNS -- hogyan lesz nevbol IP cim?

Amikor beirod a bongeszobe hogy `google.com`, a géped megkerdezi a **DNS szervert**: "Mi a google.com IP cime?" A DNS szerver visszaadja: `142.250.185.78`. Ezutan a géped erre az IP-re kuld kerest.

Olyan mint egy telefonkonyv: nev -> szam.

```
Te: "google.com kerem"
DNS: "Az 142.250.185.78"
Bongeszo: *odamegy es lekeri az oldalt*
```

## Port -- melyik ajton kopogtatsz?

Az IP cim a haz. A **port** az ajto a hazon belul.

| Port | Mi fut rajta általában |
|------|------------------------|
| `80` | HTTP (weboldal) |
| `443` | HTTPS (biztonságos weboldal) |
| `3000` | Next.js dev szerver |
| `5432` | PostgreSQL adatbázis |
| `6379` | Redis |

Amikor azt irod hogy `localhost:3000` -- az azt jelenti: "a saját gepemen a 3000-és ajton".

## Linux hálózati konfigurácio

**IP cim lekérdezése:**
```bash
ip a    # osszes halozati interfesz es IP cim
```

**Hálózat beállítása (Ubuntu/Debian):**

Az `/etc/netplan/` mappaban YAML fájlokkal konfigurálod:

```yaml
network:
  version: 2
  ethernets:
    eth0:
      addresses:
        - 192.168.1.100/24      # a szerver IP-je
      gateway4: 192.168.1.1      # a router (kifele erre megy a forgalom)
      nameservers:
        addresses:
          - 8.8.8.8              # Google DNS
          - 8.8.4.4
```

```bash
sudo netplan apply    # konfiguracio alkalmazasa
```

A `/24` a **subnet mask** -- azt mondja még hogy a hálózat melyik resze a "lakopark cime" és melyik a "lakas szama". `/24` = az első 3 szam a hálózat, az utolso szam az eszkoz.

## Fontos fájlok

| Fájl/könyvtár | Mire jó |
|----------------|---------|
| `/etc/netplan/` | Hálózati konfigurácio (IP, gateway, DNS) |
| `/etc/hosts` | Helyi "telefonkonyv" -- domain -> IP feloldas gep szintén |
| `/etc/resolv.conf` | Melyik DNS szervert használja a gep |

## Hasznos parancsok

| Parancs | Mit csinál | Mikor kell |
|---------|------------|------------|
| `ip a` | Hálózati interfeszek és IP cimek | "Mi az IP-m?" |
| `ping google.com` | Van-e internet kapcsolat | "Miért nem megy semmi?" |
| `curl ifconfig.me` | Publikus IP cim lekérdezése | "Mi az IP-m kivulrol?" |
| `nslookup domain.com` | DNS feloldas (domain -> IP) | "Hova mutat ez a domain?" |
| `ss -tlnp` | Melyik portot melyik process használja | "Mi foglalja a 3000-és portot?" |

## Fo tanulsagok
- IP cim = hazszam a hálózaton. Port = melyik ajto
- `localhost` / `127.0.0.1` = mindig a saját géped
- `192.168.x.x` = otthoni hálózat, `172.x.x.x` = Docker belső, `10.x.x.x` = felhő
- Privat IP-t kivulrol nem ered el -- ezert van publikus IP és port forwarding
- Docker-ben a service neve a hostname, nem localhost
- DNS = telefonkonyv, domain nevbol IP cimet csinál

## Kapcsolodo anyagok
- [[cloud/docker-alapok|Docker alapok]]
- [[foundations/bash-es-linux-parancssor|Bash és Linux parancssor]]
- [[cloud/docker-compose|Docker Compose]]
