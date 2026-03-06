---
tags:
  - bash
kapcsolodo:
  - "[[foundations/bash-es-linux-parancssor|Bash es Linux parancssor]]"
  - "[[cloud/docker-alapok|Docker alapok]]"
  - "[[cloud/kubernetes-bevezeto|Kubernetes bevezeto]]"
datum: 2026-02-22
szint: "🌱 Newcomer"
---

# Bash es Linux parancssor 2

## Osszefoglalo

Folytatas a [[foundations/bash-es-linux-parancssor|Bash es Linux parancssor]] jegyzetnek. Itt a fajl tartalom megtekintes (`cat`, `head`, `tail`, `less`), szoveg kiiras (`echo`), terminal szovegszerkesztok (`nano`, `vim`), folyamatok reszletes kezelese (`ps`, `pgrep`, `pkill`), az eloter/hatter job control (`Ctrl+Z`, `bg`, `fg`), es a pipe-olas (`|`) kerul sorra.

## Fajl tartalom megtekintese -- cat, head, tail, less

| Parancs | Mit csinal |
|---------|------------|
| `cat fajl` | Kiirja a teljes fajl tartalmat a terminalba |
| `head -n 10 fajl` | Csak az elso 10 sort irja ki |
| `tail -n 10 fajl` | Csak az utolso 10 sort irja ki |
| `tail -f fajl` | Eloben figyeli a fajlt -- ha uj sor kerul bele, azonnal kiirja |
| `less fajl` | Interaktiv olvasas: gorgetes, kereses, `q`-val kilepes |

```bash
# Teljes fajl kiirasa
cat /var/log/syslog

# Elso 20 sor
head -n 20 /var/log/syslog

# Elo log figyeles -- pont mint a kubectl logs -f
tail -f /var/log/app.log

# Interaktiv olvasas nagy fajlnal
less /var/log/syslog
```

> [!tip] Mikor melyiket hasznald?
> `cat` gyors es egyszeru, de ha a fajl tobb ezer soros, elarasztja a terminalt. Ilyenkor `less` a jobb -- nem tolti be az egesz fajlt a memoriaba, es tudsz benne keresni (`/keresett_szoveg`).

> [!tip] tail -f a DevOps-ban
> A `tail -f` ugyanaz a koncepcio mint a `kubectl logs -f` vagy a `docker logs -f`. Valos idoben figyeled ami tortenik, `Ctrl+C`-vel lepsz ki.

## Szoveg kiiras -- echo

| Parancs | Mit csinal |
|---------|------------|
| `echo "szoveg"` | Kiirja a szoveget a terminalba |
| `echo $VALTOZO` | Kiirja egy kornyezeti valtozo ertekat |
| `echo "szoveg" > fajl` | Beleirja a fajlba (felulirja a meglevot!) |
| `echo "szoveg" >> fajl` | Hozzafuzi a fajl vegehez |

```bash
# Egyszeru kiiras
echo "Hello World"

# Kornyezeti valtozo kiirasa
echo $HOME        # pl. /home/user
echo $PATH        # utvonalak ahol a shell parancsokat keres
echo $USER        # aktualis felhasznalo neve

# Fajlba iras -- VIGYAZAT: felulirja!
echo "elso sor" > notes.txt

# Hozzafuzes -- a meglevo tartalom megmarad
echo "masodik sor" >> notes.txt
```

> [!warning] A `>` es `>>` kozti kulonbseg
> A `>` MINDIG felulirja a fajl teljes tartalmat. Ha veletlenul `>` helyett `>>` kellene, az eredeti tartalom orokre elveszik. Ha bizonytalan vagy, inkabb `>>`.

## Szovegszerkesztes terminalban -- nano, vim

### nano -- az egyszeru szerkeszto

```bash
nano config.yaml
```

| Billentyu | Mit csinal |
|-----------|------------|
| `Ctrl+O` | Mentes (Write Out) |
| `Ctrl+X` | Kilepes |
| `Ctrl+K` | Sor kivagas |
| `Ctrl+U` | Kivagott sor beillesztese |
| `Ctrl+W` | Kereses |

### vim -- a profi szerkeszto

```bash
vim config.yaml
```

A vim modokkal mukodik:

| Mod | Hogyan lepsz be | Mire jo |
|-----|-----------------|---------|
| Normal | `Esc` | Navigacio, parancsok |
| Insert | `i` | Szoveg irasa |
| Command | `:` (Normal modbol) | Mentes, kilepes |

| Parancs | Mit csinal |
|---------|------------|
| `i` | Insert modba lep (szerkesztes) |
| `Esc` | Visszalep Normal modba |
| `:w` | Mentes |
| `:q` | Kilepes |
| `:wq` | Mentes es kilepes |
| `:q!` | Kilepes mentes nelkul |
| `dd` | Sor torlese (Normal modban) |

> [!tip] Mikor melyiket?
> **nano**: ha gyorsan szerkeszteni akarsz valamit. Kezdobarat.
> **vim**: szerveren szinte mindig fenn van (nano neha nincs), es ha megtanulod, sokkal gyorsabb. Minimum a `:wq` es `:q!` kombinaciokat erdemes tudni.

> [!warning] A vim csapdaja
> Ha veletlenul megnyitottad es nem tudsz kilepni: `Esc` -> `:q!` -> Enter. Ez mentes nelkul kilep.

## Folyamatok kezelese -- reszletesen

| Parancs | Mit csinal |
|---------|------------|
| `ps aux` | Osszes futo folyamat listazasa |
| `ps aux \| grep nginx` | Szures adott processre |
| `top` / `htop` | Elo nezet (CPU, memoria) |
| `pgrep -l nginx` | PID keresese nev alapjan |
| `pkill nginx` | Processz leallitasa nev alapjan |

```bash
# Osszes futo folyamat
ps aux

# Szures -- csak az nginx processzek
ps aux | grep nginx

# PID kereses gyorsan
pgrep -l nginx
# 1234 nginx

# Leallitas nev alapjan (nem kell PID-et keresni)
pkill nginx
```

> [!tip] pgrep es pkill
> Rovidites: nem kell `ps aux | grep` majd `kill PID`. A `pkill nginx` egyben megcsinalja amit a `ps aux | grep nginx` + `kill 1234` kettoben.

## Eloter/hatter kezeles -- Job control

Amikor fut egy parancs a terminalban (pl. `kubectl port-forward`), az elfoglalja a terminalt. A job control megoldja ezt.

| Parancs | Mit csinal |
|---------|------------|
| `Ctrl+Z` | Felfuggeszti (suspend) az aktualis futo parancsot |
| `bg` | Hatterbe rakja a felfuggesztett parancsot |
| `fg` | Visszahozza elotarbe |
| `jobs` | Listazza a hatterben futo job-okat |
| `kill %1` | Leallitja az 1-es szamu hatter job-ot |
| `parancs &` | Eleve hatterben inditja a parancsot |

### Gyakorlati pelda -- port-forward hatterbe rakasa

```bash
# 1. Elinditok egy port-forward-ot
kubectl port-forward -n demo svc/webapp 8080:80
# Forwarding from 127.0.0.1:8080 -> 80
# A terminal "be van ragadva"...

# 2. Ctrl+Z -- felfuggesztem
# [1]  + suspended  kubectl port-forward ...

# 3. bg -- hatterbe rakom, a terminalt ujra hasznalhatom
bg
# [1]  + continued  kubectl port-forward ...

# 4. jobs -- megnezem mi fut a hatterben
jobs
# [1]  + running    kubectl port-forward ...

# 5. Kozben dolgozhatok a terminalban
kubectl get pods -n demo
curl localhost:8080

# 6. Ha le akarom allitani:
kill %1
```

> [!tip] A `&` rovidites
> Ha eleve tudod hogy hatterben akarod:
> `kubectl port-forward svc/webapp 8080:80 &`
> Igy nem kell a Ctrl+Z + bg tanc.

> [!tip] Tobb job kezelese
> Ha tobb hatter job-od van, a `jobs` parancs szamozza oket (`[1]`, `[2]`):
> `fg %2` -- a 2-es job elotarbe
> `kill %3` -- a 3-as job leallitasa

> [!warning] Felfuggesztett vs hatterben futo
> A `Ctrl+Z` csak **felfuggeszti** a processzt -- ilyenkor NEM fut! A port-forward nem mukodik amig `bg`-vel hatterbe nem rakod. A `suspended` =/= `running`.

## Pipe-olas ( | ) -- parancsok osszekotese

A pipe az elso parancs kimenetit (stdout) atadja a masodik parancs bemeneteul.

| Parancs | Mit csinal |
|---------|------------|
| `ps aux \| grep nginx` | Futo processzek szurese |
| `cat log.txt \| wc -l` | Sorok szamolasa |
| `cat log.txt \| grep ERROR` | Csak a hibas sorok |
| `kubectl get pods \| grep webapp` | K8s pod-ok szurese |
| `history \| grep docker` | Korabbi parancsok keresese |

```bash
# Szures
ps aux | grep nginx

# Sorok szamolasa
cat /var/log/syslog | wc -l

# Tobb pipe lancolva
cat /var/log/app.log | grep ERROR | wc -l
# Hany hiba sor van osszesen?

# Kubernetes-ben is ugyanigy
kubectl get pods -A | grep CrashLoopBackOff
```

> [!tip] A Unix filozofia
> "Egy program egy dolgot csinaljon, de jol." A pipe-pal osszekotheted oket: `grep` keres, `wc` szamol, `sort` rendez, `head` levag. Kulon-kulon egyszeruek, de osszerakva barmit megoldhatsz.

## Fo tanulsagok

- `cat` gyors, de nagy fajlnal `less` jobb -- nem tolti be az egeszet a memoriaba
- `echo` + atiranyitas (`>`, `>>`) az egyik legalapabb fajlba-iras modszer
- `Ctrl+Z` + `bg` a leggyakoribb "elfelejtettem hatterben inditani" mentoov
- A pipe (`|`) a legfontosabb shell koncepcio -- ezzel barmit osszekotol barmivel
- `nano` vs `vim`: helyi gepen mindegy, szerveren vim-et erdemes tudni mert mindenhol fenn van
- A `Ctrl+Z` csak felfuggeszti a processzt, `bg` kell hogy tenyleg fusson hatterben

## Kapcsolodo anyagok
- [[foundations/bash-es-linux-parancssor|Bash es Linux parancssor]] -- alapok, elso resz
- [[cloud/docker-alapok|Docker alapok]]
- [[cloud/kubernetes-bevezeto|Kubernetes bevezeto]]
