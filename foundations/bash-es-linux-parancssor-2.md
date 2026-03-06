---
tags:
  - bash
kapcsolodo:
  - "[[foundations/bash-es-linux-parancssor|Bash és Linux parancssor]]"
  - "[[cloud/docker-alapok|Docker alapok]]"
  - "[[cloud/kubernetes-bevezeto|Kubernetes bevezeto]]"
datum: 2026-02-22
szint: "🌱 Newcomer"
---

# Bash és Linux parancssor 2

## Összefoglaló

Folytatas a [[foundations/bash-es-linux-parancssor|Bash és Linux parancssor]] jegyzetnek. Itt a fájl tartalom megtekintes (`cat`, `head`, `tail`, `less`), szoveg kiiras (`echo`), terminal szovegszerkesztok (`nano`, `vim`), folyamatok részletes kezelese (`ps`, `pgrep`, `pkill`), az eloter/háttér job control (`Ctrl+Z`, `bg`, `fg`), és a pipe-olas (`|`) kerul sorra.

## Fájl tartalom megtekintese -- cat, head, tail, less

| Parancs | Mit csinál |
|---------|------------|
| `cat fajl` | Kiirja a teljes fájl tartalmat a terminalba |
| `head -n 10 fajl` | Csak az első 10 sort irja ki |
| `tail -n 10 fajl` | Csak az utolso 10 sort irja ki |
| `tail -f fajl` | Eloben figyeli a fájlt -- ha új sor kerul bele, azonnal kiirja |
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

> [!tip] Mikor melyiket használd?
> `cat` gyors és egyszerű, de ha a fájl több ezer soros, elarasztja a terminalt. Ilyenkor `less` a jobb -- nem tolti be az egész fájlt a memoriaba, és tudsz benne keresni (`/keresett_szoveg`).

> [!tip] tail -f a DevOps-ban
> A `tail -f` ugyanaz a koncepcio mint a `kubectl logs -f` vagy a `docker logs -f`. Valós idoben figyeled ami tortenik, `Ctrl+C`-vel lepsz ki.

## Szoveg kiiras -- echo

| Parancs | Mit csinál |
|---------|------------|
| `echo "szoveg"` | Kiirja a szoveget a terminalba |
| `echo $VALTOZO` | Kiirja egy környezeti változó értékat |
| `echo "szoveg" > fajl` | Beleirja a fájlba (felulirja a meglevot!) |
| `echo "szoveg" >> fajl` | Hozzáfuzi a fájl vegehez |

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

> [!warning] A `>` és `>>` kozti különbség
> A `>` MINDIG felulirja a fájl teljes tartalmat. Ha veletlenul `>` helyett `>>` kellene, az eredeti tartalom orokre elveszik. Ha bizonytalan vagy, inkabb `>>`.

## Szovegszerkesztes terminalban -- nano, vim

### nano -- az egyszerű szerkeszto

```bash
nano config.yaml
```

| Billentyu | Mit csinál |
|-----------|------------|
| `Ctrl+O` | Mentés (Write Out) |
| `Ctrl+X` | Kilepes |
| `Ctrl+K` | Sor kivagas |
| `Ctrl+U` | Kivagott sor beillesztese |
| `Ctrl+W` | Kereses |

### vim -- a profi szerkeszto

```bash
vim config.yaml
```

A vim modokkal működik:

| Mod | Hogyan lepsz be | Mire jó |
|-----|-----------------|---------|
| Normal | `Esc` | Navigacio, parancsok |
| Insert | `i` | Szoveg irasa |
| Command | `:` (Normal modbol) | Mentés, kilepes |

| Parancs | Mit csinál |
|---------|------------|
| `i` | Insert modba lep (szerkesztes) |
| `Esc` | Visszalep Normal modba |
| `:w` | Mentés |
| `:q` | Kilepes |
| `:wq` | Mentés és kilepes |
| `:q!` | Kilepes mentés nelkul |
| `dd` | Sor törlése (Normal modban) |

> [!tip] Mikor melyiket?
> **nano**: ha gyorsan szerkeszteni akarsz valamit. Kezdobarat.
> **vim**: szerveren szinte mindig fenn van (nano neha nincs), és ha megtanulod, sokkal gyorsabb. Minimum a `:wq` és `:q!` kombinaciokat érdemes tudni.

> [!warning] A vim csapdaja
> Ha veletlenul megnyitottad és nem tudsz kilepni: `Esc` -> `:q!` -> Enter. Ez mentés nelkul kilep.

## Folyamatok kezelese -- részletesen

| Parancs | Mit csinál |
|---------|------------|
| `ps aux` | Összes futo folyamat listazasa |
| `ps aux \| grep nginx` | Szűres adott processre |
| `top` / `htop` | Elo nezet (CPU, memoria) |
| `pgrep -l nginx` | PID keresese nev alapján |
| `pkill nginx` | Processz leallitasa nev alapján |

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

> [!tip] pgrep és pkill
> Rovidites: nem kell `ps aux | grep` majd `kill PID`. A `pkill nginx` egyben megcsinalja amit a `ps aux | grep nginx` + `kill 1234` kettoben.

## Eloter/háttér kezeles -- Job control

Amikor fut egy parancs a terminalban (pl. `kubectl port-forward`), az elfoglalja a terminalt. A job control megoldja ezt.

| Parancs | Mit csinál |
|---------|------------|
| `Ctrl+Z` | Felfuggeszti (suspend) az aktualis futo parancsot |
| `bg` | Háttérbe rakja a felfuggesztett parancsot |
| `fg` | Visszahozza elotarbe |
| `jobs` | Listazza a háttérben futo job-okat |
| `kill %1` | Leallitja az 1-és szamu háttér job-ot |
| `parancs &` | Eleve háttérben indítja a parancsot |

### Gyakorlati példa -- port-forward háttérbe rakasa

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
> Ha eleve tudod hogy háttérben akarod:
> `kubectl port-forward svc/webapp 8080:80 &`
> Így nem kell a Ctrl+Z + bg tanc.

> [!tip] Több job kezelese
> Ha több háttér job-od van, a `jobs` parancs szamozza oket (`[1]`, `[2]`):
> `fg %2` -- a 2-és job elotarbe
> `kill %3` -- a 3-as job leallitasa

> [!warning] Felfuggesztett vs háttérben futo
> A `Ctrl+Z` csak **felfuggeszti** a processzt -- ilyenkor NEM fut! A port-forward nem működik amig `bg`-vel háttérbe nem rakod. A `suspended` =/= `running`.

## Pipe-olas ( | ) -- parancsok osszekotese

A pipe az első parancs kimenetit (stdout) atadja a masodik parancs bemeneteul.

| Parancs | Mit csinál |
|---------|------------|
| `ps aux \| grep nginx` | Futo processzek szűrese |
| `cat log.txt \| wc -l` | Sorok szamolasa |
| `cat log.txt \| grep ERROR` | Csak a hibas sorok |
| `kubectl get pods \| grep webapp` | K8s pod-ok szűrese |
| `history \| grep docker` | Korábbi parancsok keresese |

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
> "Egy program egy dolgot csináljon, de jol." A pipe-pal osszekotheted oket: `grep` keres, `wc` szamol, `sort` rendez, `head` levag. Kulon-kulon egyszerűek, de osszerakva bármit megoldhatsz.

## Fo tanulsagok

- `cat` gyors, de nagy fájlnal `less` jobb -- nem tolti be az egészet a memoriaba
- `echo` + atiranyitas (`>`, `>>`) az egyik legalapabb fájlba-iras modszer
- `Ctrl+Z` + `bg` a leggyakoribb "elfelejtettem háttérben indítani" mentoov
- A pipe (`|`) a legfontosabb shell koncepcio -- ezzel bármit osszekotol bármivel
- `nano` vs `vim`: helyi gépen mindegy, szerveren vim-et érdemes tudni mert mindenhol fenn van
- A `Ctrl+Z` csak felfuggeszti a processzt, `bg` kell hogy tényleg fusson háttérben

## Kapcsolodo anyagok
- [[foundations/bash-es-linux-parancssor|Bash és Linux parancssor]] -- alapok, első resz
- [[cloud/docker-alapok|Docker alapok]]
- [[cloud/kubernetes-bevezeto|Kubernetes bevezeto]]
