---
tags:
  - bash
kapcsolodo:
  - "[[cloud/docker-alapok|Docker alapok]]"
  - "[[foundations/halozatok-es-ip-cimek|Halozatok es IP cimek]]"
  - "[[foundations/git-es-github|Git es GitHub]]"
datum: 2026-02-08
szint: "🌱 Newcomer"
---

# Bash es Linux parancssor

## Osszefoglalo

A Linux/Unix parancssor alapjai: fajlkezeles, jogosultsagok, folyamatok kezelese, halozat, szovegkereses es -feldolgozas. Ezek nelkul nem megy a szerver oldali munka, deploy, es a legtobb dev eszkoz is ezekre epul.

## Konfiguracios fajlok

| Fajl | Mire jo | Megjegyzes |
|------|---------|------------|
| `.bashrc` | Kornyezeti valtozok, aliasok | Csak Bash shell-re vonatkozik |
| `.profile` | Kornyezeti valtozok | Barmilyen shell-re, Linux kornyezetben |

## Jogosultsagok

A fajlok elejen levo karakterek mutatjak a **read / write / execute** jogokat.

`chmod` -- jogosultsagszerkesztes

Harom szam egymas utan, harom kulonbozo felhasznalo csoportnak (owner / group / others):
- **Read** = 4
- **Write** = 2
- **Execute** = 1
- Ossze kell adni oket

| Parancs | Mit csinal |
|---------|------------|
| `chmod +x a.key` | Futtathatova teszi |
| `chmod 700 a.key` | Csak en tudok vele mindent (rwx------) |
| `chmod 755 script.sh` | En mindent, masok olvasni+futtatni |

## Futo folyamatok

| Parancs | Mit csinal |
|---------|------------|
| `ps aux` | Most futo folyamatok listaja |
| `top` | Elo nezet: mi fogyaszt CPU-t es memoriat |
| `htop` | Ugyanaz mint `top`, de szebb es interaktiv |
| `kill PID` | Folyamat leallitasa PID alapjan |
| `kill -9 PID` | Eroltetett leallitas (ha a sima `kill` nem eleg) |

## Halozat

| Parancs | Mit csinal |
|---------|------------|
| `ip a` | Osszes halozati kapcsolat listazasa |
| `curl URL` | HTTP request kuldese (API teszteles, letoltes) |
| `wget URL` | Fajl letoltese URL-rol |

## Fajlok es parancsok futtatasa

- A `/bin` konyvtarban vannak a rendszer parancsok
- Ha helyben akarod futtatni: `./parancsneve`
- Shell scriptben kiterjesztes nelkul is lehet futtatni

## Szoveg kereses -- grep es rg

| Parancs | Mit csinal |
|---------|------------|
| `grep "szoveg" fajl` | Rakeresi a szoveget a fajlban |
| `grep -i "szoveg" fajl` | Kis- es nagybetu nem szamit |
| `grep -v "szoveg" fajl` | Minden sort kiir amiben NINCS benne |
| `rg "szoveg"` | Gyorsabb kereses, foleg sok fajlnal (ripgrep) |

> [!tip] grep vs ripgrep
> `grep` kicsit lassu nagy mennyisegu fajlnal -- ilyenkor `rg` (ripgrep) a jobb valasztas.

## Szoveg csere -- sed

`sed` -- kereses es csere, foleg scriptekben

```bash
sed 's/[0-9]/X/g' fajl    # csereld ki a szamokat X-re
```

- `s/` = substitute (csere)
- `/g` = globalisan (az osszes talalatot, nem csak az elsot)

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

| Parancs | Mit csinal |
|---------|------------|
| `2>` | Atiranyitja a hiba csatornat (pl. fajlba) |
| `2>&1` | Osszevezes: stderr-t is oda kuldi ahol stdout megy |
| `parancs > log.txt 2>&1` | Minden kimenet (normal + hiba) a log.txt-be |

## Fo tanulsagok
- A jogosultsagok megertese alapveto: `chmod` szamokkal gyors es pontos
- `grep` vs `rg`: kis projektnel mindegy, sok fajlnal ripgrep nyer
- `sed` es `awk` nem kell fejbol tudni, de felismerni es erteni igen
- `kill -9` az utolso mentsvár, eloszor mindig sima `kill`-el probalkozz -- a `-9` azonnal megoli a processzt, nincs cleanup

> [!warning] kill -9 veszelye
> A `kill -9` (SIGKILL) nem ad eselyt a processnek a tiszta leallasra. Fajlok nyitva maradhatnak, lock file-ok bent ragadnak. Eloszor mindig sima `kill PID` (SIGTERM), es csak ha az nem segit, akkor `-9`.

- A kimenet atiranyitas (`2>&1`) fontos scripteknel es loggolasnal

## Kapcsolodo anyagok
- [[cloud/docker-alapok|Docker alapok]]
- [[foundations/halozatok-es-ip-cimek|Halozatok es IP cimek]]
- [[foundations/git-es-github|Git es GitHub]]
- [[foundations/bash-es-linux-parancssor-2|Bash es Linux parancssor 2]] -- folytatas, halado parancsok
