---
tags:
  - bash
kapcsolodo:
  - "[[cloud/docker-alapok|Docker alapok]]"
  - "[[foundations/halozatok-es-ip-cimek|Hálózatok és IP cimek]]"
  - "[[foundations/git-es-github|Git és GitHub]]"
datum: 2026-02-08
szint: "🌱 Newcomer"
---

# Bash és Linux parancssor

## Összefoglaló

A Linux/Unix parancssor alapjai: fájlkezeles, jogosultságok, folyamatok kezelese, hálózat, szovegkereses és -feldolgozas. Ezek nelkul nem megy a szerver oldali munka, deploy, és a legtobb dev eszkoz is ezekre epul.

## Konfigurácios fájlok

| Fájl | Mire jó | Megjegyzes |
|------|---------|------------|
| `.bashrc` | Környezeti változók, aliasok | Csak Bash shell-re vonatkozik |
| `.profile` | Környezeti változók | Bármilyen shell-re, Linux környezetben |

## Jogosultságok

A fájlok elejen levo karakterek mutatjak a **read / write / execute** jogokat.

`chmod` -- jogosultságszerkesztes

Harom szam egymas utan, harom különböző felhasználó csoportnak (owner / group / others):
- **Read** = 4
- **Write** = 2
- **Execute** = 1
- Ossze kell adni oket

| Parancs | Mit csinál |
|---------|------------|
| `chmod +x a.key` | Futtathatova teszi |
| `chmod 700 a.key` | Csak en tudok vele mindent (rwx------) |
| `chmod 755 script.sh` | En mindent, masok olvasni+futtatni |

## Futo folyamatok

| Parancs | Mit csinál |
|---------|------------|
| `ps aux` | Most futo folyamatok listaja |
| `top` | Elo nezet: mi fogyaszt CPU-t és memoriat |
| `htop` | Ugyanaz mint `top`, de szebb és interaktiv |
| `kill PID` | Folyamat leallitasa PID alapján |
| `kill -9 PID` | Eroltetett leallitas (ha a sima `kill` nem eleg) |

## Hálózat

| Parancs | Mit csinál |
|---------|------------|
| `ip a` | Összes hálózati kapcsolat listazasa |
| `curl URL` | HTTP request küldése (API tesztelés, letöltés) |
| `wget URL` | Fájl letöltése URL-rol |

## Fájlok és parancsok futtatasa

- A `/bin` könyvtárban vannak a rendszer parancsok
- Ha helyben akarod futtatni: `./parancsneve`
- Shell scriptben kiterjesztés nelkul is lehet futtatni

## Szoveg kereses -- grep és rg

| Parancs | Mit csinál |
|---------|------------|
| `grep "szoveg" fajl` | Rakeresi a szoveget a fájlban |
| `grep -i "szoveg" fajl` | Kis- és nagybetu nem szamit |
| `grep -v "szoveg" fajl` | Minden sort kiir amiben NINCS benne |
| `rg "szoveg"` | Gyorsabb kereses, foleg sok fájlnal (ripgrep) |

> [!tip] grep vs ripgrep
> `grep` kicsit lassu nagy mennyisegu fájlnal -- ilyenkor `rg` (ripgrep) a jobb választas.

## Szoveg csere -- sed

`sed` -- kereses és csere, foleg scriptekben

```bash
sed 's/[0-9]/X/g' fajl    # csereld ki a szamokat X-re
```

- `s/` = substitute (csere)
- `/g` = globálisan (az összes talalatot, nem csak az elsőt)

## Oszlopok kiszedese -- awk

```bash
awk '{print $1}' fajl    # kiirja az elso oszlopot
```

Hasznos ha egy parancs kimenetabol csak bizonyos oszlopok kellenek.

## Kimenet atiranyitas

| Csatorna | Neve | Szam |
|----------|------|------|
| Normal kimenet | stdout | 1 |
| Hiba kimenet | stderr | 2 |

| Parancs | Mit csinál |
|---------|------------|
| `2>` | Atiranyitja a hiba csatornat (pl. fájlba) |
| `2>&1` | Osszevezes: stderr-t is oda küldi ahol stdout megy |
| `parancs > log.txt 2>&1` | Minden kimenet (normal + hiba) a log.txt-be |

## Fo tanulsagok
- A jogosultságok megertese alapvető: `chmod` szamokkal gyors és pontos
- `grep` vs `rg`: kis projektnel mindegy, sok fájlnal ripgrep nyer
- `sed` és `awk` nem kell fejbol tudni, de felismerni és erteni igen
- `kill -9` az utolso mentsvár, először mindig sima `kill`-el probalkozz -- a `-9` azonnal megoli a processzt, nincs cleanup

> [!warning] kill -9 veszelye
> A `kill -9` (SIGKILL) nem ad eselyt a processnek a tiszta leallasra. Fájlok nyitva maradhatnak, lock file-ok bent ragadnak. Először mindig sima `kill PID` (SIGTERM), és csak ha az nem segit, akkor `-9`.

- A kimenet atiranyitas (`2>&1`) fontos scripteknel és loggolasnal

## Kapcsolodo anyagok
- [[cloud/docker-alapok|Docker alapok]]
- [[foundations/halozatok-es-ip-cimek|Hálózatok és IP cimek]]
- [[foundations/git-es-github|Git és GitHub]]
- [[foundations/bash-es-linux-parancssor-2|Bash és Linux parancssor 2]] -- folytatas, halado parancsok
