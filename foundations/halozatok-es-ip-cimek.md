---
tags:
  - halozatok
kapcsolodo:
  - "[[cloud/docker-alapok|Docker alapok]]"
  - "[[foundations/bash-es-linux-parancssor|Bash es Linux parancssor]]"
  - "[[cloud/docker-compose|Docker Compose]]"
datum: 2026-02-08
szint: "🧱 Brick"
---

# Halozatok es IP cimek

## Osszefoglalo

Ahhoz hogy megertsd hogyan kommunikal az app a szerverrel, a szerver az adatbazissal, vagy a Docker kontenerek egymassal -- ertened kell az IP cimeket es a halozatok alapjait. Ez nem bonyolult, de tudni kell mi mi.

## Mi az az IP cim? -- egyszeruen

Kepzeld el hogy az internet egy varos. Minden haz (szamitogep, szerver, telefon) kap egy **hazszamot** -- ez az IP cim. Ha valaki uzenetet akar kuldeni neked, tudnia kell a hazszamodat.

**IPv4** -- a regi rendszer:
```
192.168.1.100
```
4 szam, pontokkal elvalasztva. Minden szam 0-255 kozott. Osszesen ~4.3 milliard lehetseges cim -- ez keves, ezert jott az IPv6.

**IPv6** -- az uj rendszer:
```
2001:0db8:85a3::8a2e:0370:7334
```
Sokkal tobb cim fer bele. Egyelore nem kell vele sokat foglalkozni, de jo tudni hogy letezik.

## Privat vs publikus IP -- miert fontos?

Gondolj egy lakoparkra:
- A lakopark **kapuja** kap egy hazszamot az utcan -- ez a **publikus IP** (a vilag latja)
- A lakasokon belul vannak sajat ajtoszamok -- ezek a **privat IP**-k (csak belulrol latszanak)

**Privat tartomanyok** (nem erhetok el az internetrol):

| Tartomany | Hol talalkozol vele | Hasonlat |
|-----------|---------------------|----------|
| `192.168.x.x` | **Otthoni wifi router** -- a telefonod, laptopod ilyet kap | A lakasod ajtoszama |
| `172.16.x.x` -- `172.31.x.x` | **Docker kontenerek** -- egymas kozott igy beszelgetnek | Szobaszamok egy szallodan belul |
| `10.x.x.x` | **Felho szolgaltatok** (AWS, Azure belso halozata) | Irodaszamok egy nagy epuletben |

**Specialis cim:**
| Cim | Mit jelent | Mikor latod |
|-----|------------|-------------|
| `127.0.0.1` (localhost) | **Mindig a sajat geped** | `localhost:3000` -- a sajat gepen futo app |

## Miert szamit ez fejlesztes kozben?

Amikor deployolsz vagy Docker-t hasznalsz, ezek a helyzetek jonnek elo:

- **"A backend nem eri el az adatbazist"** -> Valoszinuleg rossz IP vagy host nevet hasznalsz. Docker-ben a service neve = a hostneve (pl. `db`, nem `localhost`)
- **"Localhostrol megy, production-bol nem"** -> Localhost = a sajat geped. A szerveren mas IP van. Kornyezeti valtozokban kell megadni
- **"CORS hiba"** -> A frontend mas cimrol probalja elerni a backendet mint amirol az elvarja

> [!tip] Best practice
> Mindig add meg a kontextust hogy lokalisan futsz, Docker-ben, vagy production-ben -- mert mas-mas IP/host konfiguraciot kell hasznalni mindegyikben.

## DNS -- hogyan lesz nevbol IP cim?

Amikor beirod a bongeszobe hogy `google.com`, a geped megkerdezi a **DNS szervert**: "Mi a google.com IP cime?" A DNS szerver visszaadja: `142.250.185.78`. Ezutan a geped erre az IP-re kuld kerest.

Olyan mint egy telefonkonyv: nev -> szam.

```
Te: "google.com kerem"
DNS: "Az 142.250.185.78"
Bongeszo: *odamegy es lekeri az oldalt*
```

## Port -- melyik ajton kopogtatsz?

Az IP cim a haz. A **port** az ajto a hazon belul.

| Port | Mi fut rajta altalaban |
|------|------------------------|
| `80` | HTTP (weboldal) |
| `443` | HTTPS (biztonsagos weboldal) |
| `3000` | Next.js dev szerver |
| `5432` | PostgreSQL adatbazis |
| `6379` | Redis |

Amikor azt irod hogy `localhost:3000` -- az azt jelenti: "a sajat gepemen a 3000-es ajton".

## Linux halozati konfiguracio

**IP cim lekerdezese:**
```bash
ip a    # osszes halozati interfesz es IP cim
```

**Halozat beallitasa (Ubuntu/Debian):**

Az `/etc/netplan/` mappaban YAML fajlokkal konfiguralod:

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

A `/24` a **subnet mask** -- azt mondja meg hogy a halozat melyik resze a "lakopark cime" es melyik a "lakas szama". `/24` = az elso 3 szam a halozat, az utolso szam az eszkoz.

## Fontos fajlok

| Fajl/konyvtar | Mire jo |
|----------------|---------|
| `/etc/netplan/` | Halozati konfiguracio (IP, gateway, DNS) |
| `/etc/hosts` | Helyi "telefonkonyv" -- domain -> IP feloldas gep szinten |
| `/etc/resolv.conf` | Melyik DNS szervert hasznalja a gep |

## Hasznos parancsok

| Parancs | Mit csinal | Mikor kell |
|---------|------------|------------|
| `ip a` | Halozati interfeszek es IP cimek | "Mi az IP-m?" |
| `ping google.com` | Van-e internet kapcsolat | "Miert nem megy semmi?" |
| `curl ifconfig.me` | Publikus IP cim lekerdezese | "Mi az IP-m kivulrol?" |
| `nslookup domain.com` | DNS feloldas (domain -> IP) | "Hova mutat ez a domain?" |
| `ss -tlnp` | Melyik portot melyik process hasznalja | "Mi foglalja a 3000-es portot?" |

## Fo tanulsagok
- IP cim = hazszam a halozaton. Port = melyik ajto
- `localhost` / `127.0.0.1` = mindig a sajat geped
- `192.168.x.x` = otthoni halozat, `172.x.x.x` = Docker belso, `10.x.x.x` = felho
- Privat IP-t kivulrol nem ered el -- ezert van publikus IP es port forwarding
- Docker-ben a service neve a hostname, nem localhost
- DNS = telefonkonyv, domain nevbol IP cimet csinal

## Kapcsolodo anyagok
- [[cloud/docker-alapok|Docker alapok]]
- [[foundations/bash-es-linux-parancssor|Bash es Linux parancssor]]
- [[cloud/docker-compose|Docker Compose]]
