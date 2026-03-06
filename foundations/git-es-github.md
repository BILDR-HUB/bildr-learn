---
tags:
  - git
kapcsolodo:
  - "[[foundations/bash-es-linux-parancssor|Bash es Linux parancssor]]"
  - "[[cloud/vercel|Vercel]]"
  - "[[cloud/railway|Railway]]"
datum: 2026-02-08
szint: "🌱 Newcomer"
---

# Git es GitHub

## Osszefoglalo

A Git nyomon koveti a kodod valtozasait -- barmikor visszamehetsz egy korabbi verziora, es tobben dolgozhattok parhuzamosan. A GitHub az ahol a kodod "el" online, es ahonnan a [[cloud/vercel|Vercel]]/[[cloud/railway|Railway]] deployol.

## Mi a Git es miert kell?

Kepzeld el hogy van egy Word dokumentumod es Ctrl+Z-vel visszavonod a valtozasokat. A Git ugyanez, csak:
- **Nem egy fajlra**, hanem az egesz projektre vonatkozik
- **Nem csak visszavonas**, hanem barmely korabbi allapothoz visszalephetsz
- **Tobben dolgozhattok** egyszerre, es osszeolvasztjatok a valtozasokat

**Miert fontos ha AI-val kodolsz:**
- Ha az AI agent elront valamit -> `git checkout .` es visszaall minden
- Ha kiserletezz -> branch-en dolgozol, ha nem jo: torlod
- A Vercel/Railway automatikusan deployol ha push-olsz GitHubra

## A 3 hely ahol a kodod van

```
Working Directory -> Staging Area -> Repository
  (a fajljaid)       (ami menni fog)   (elmentett verzio)
```

1. **Working directory** -- a mappad ahol dolgozol
2. **Staging area** -- amit kijeloltel hogy az kovetkezo mentesbe (commit) keruljon
3. **Repository** -- az elmentett verziok halmaza

## Alapparancsok -- a mindennapok

| Parancs | Mit csinal | Mikor kell |
|---------|------------|------------|
| `git init` | Uj Git repo letrehozasa | Projekt elejen, egyszer |
| `git status` | Mi valtozott? Mi van staging-ben? | Mindig, mielott commitolsz |
| `git add .` | Az osszes valtozast staging-be teszi | Amikor mindent menteni akarsz |
| `git add fajl.ts` | Csak egy fajlt tesz staging-be | Amikor valogatni akarsz |
| `git commit -m "uzenet"` | Elment egy verziot uzenettel | Amikor egy logikai egyseg kesz |
| `git log --oneline` | Korabbi commitok listaja | Megnezni mi tortent |
| `git diff` | Mi valtozott a fajlokban | Mielott commitolsz, atnezni |

## Commit -- a "mentes gomb"

Egy commit = a projekt egy pillanatkipe. Minden commithoz tartozik:
- Egyedi azonosito (hash): `a1b2c3d`
- Uzenet: mit csinaltal
- Idopont es szerzo

**Jo commit uzenet:** Rovid, leirja MIT csinaltal es MIERT
```
feat: add user login page
fix: resolve database connection timeout
```

**Best practice AI-val valo fejleszteshez:**
- **Commitolj gyakran** -- ha az AI agent csinalt egy mukodo valtoztatast, azonnal commitold. Ha a kovetkezo valtoztatas elront valamit, konnyu visszaallni
- **Ne commitolj mindent egybe** -- ha az agent 5 fajlt modositott, de 3 egy feature es 2 egy masik, csinald 2 commitban

## Branch -- parhuzamos munka

A branch egy "parhuzamos valosag" a kododban. Az eredeti kod (`main`) nem valtozik, amig a branchen dolgozol.

```
main:     A -> B -> C
                    \
feature:             D -> E -> F
```

| Parancs | Mit csinal |
|---------|------------|
| `git branch` | Milyen branchek vannak |
| `git branch feature-nev` | Uj branch letrehozasa |
| `git checkout feature-nev` | Atvaltas a branchre |
| `git checkout -b feature-nev` | Letrehozas + atvaltas egyben |
| `git merge feature-nev` | Branch beolvasztasa a jelenlegibe |
| `git branch -d feature-nev` | Branch torlese (ha mar merge-olve) |

**Best practice AI-val valo fejleszteshez:**
- **Minden feature-nek uj branch** -- ha az AI agent nagy valtoztatast csinal, csinald branchen
- Ha elromlik valami -> `git checkout main` es ott vagy ahol voltal
- A Vercel automatikusan preview deploy-t csinal minden branchhez

## GitHub -- a kod online otthona

A Git lokalisan mukodik a gepeden. A **GitHub** az ahol:
- A kod online tarolva van (backup)
- Masok (es a Vercel/Railway) hozzafernek
- Pull Request-ekkel atnezed a valtozasokat

| Parancs | Mit csinal |
|---------|------------|
| `git remote add origin URL` | GitHub repo hozzarendelese |
| `git push origin main` | Kod feltoltese GitHubra |
| `git push -u origin main` | Feltoltes + megjegyzi (utana eleg `git push`) |
| `git pull` | Legfrissebb verzio lehuzasa GitHubrol |
| `git clone URL` | Meglevo repo letoltese |

## .gitignore -- amit NE tolts fel

A `.gitignore` fajl megmondja a Git-nek mit hagyjon ki:

```gitignore
node_modules/        # fuggosegek (npm install ujra letolti)
.env                 # titkos kulcsok, API key-ek
.env.local           # lokalis environment valtozok
.next/               # Next.js build mappa
dist/                # build output
.DS_Store            # macOS rendszerfajl
```

> [!danger] Titok felkerult GitHubra
> Ha egyszer felkerult GitHubra egy API kulcs vagy jelszo, akkor mar keso gitignore-olni. A Git history-ban benne marad -- barki lathatja. Ilyenkor **azonnal uj kulcsot kell generalni**, es a regit invalidalni.

## Pull Request (PR) -- valtoztatas atnezese

A PR egy keres: "ezeket a valtozatasokat szeretnem beolvasztani a main-be". GitHub-on jon letre.

```
1. Csinalsz egy branch-et -> dolgozol rajta
2. Push-olod GitHubra
3. GitHub-on nyitsz egy Pull Request-et
4. Atnezed (vagy masok atnezik) a valtozasokat
5. Merge -> beolvad a main-be
6. Vercel automatikusan deployol
```

**Best practice:**
- Nezd at a PR diff-et mielott merge-olsz -- AI is hibazhat
- A PR-nek legyen leirasa: mit csinal es miert

## Visszaallitas -- ha valami elromlik

| Helyzet | Parancs | Mit csinal |
|---------|---------|------------|
| Meg nem commitoltam, mindent vissza | `git checkout .` | Visszaallitja az osszes fajlt az utolso commitra |
| Egy fajlt akarok visszaallitani | `git checkout -- fajl.ts` | Csak azt az egyet allitja vissza |
| Commitoltam de meg nem pushotam, vissza | `git reset HEAD~1` | Visszavonja az utolso commitot (a fajlok maradnak) |
| Korabbi allapotot akarok megnezni | `git log --oneline` -> `git checkout a1b2c3d` | "Idoutazas" egy regi commithoz |
| Pusholva van, de vissza akarok | `git revert a1b2c3d` | Uj committal visszacsinalja a regit (biztonsagos) |

**Best practice:**
- Mielott nagy valtoztatast csinalsz -> `git status` es ha kell `git stash` (felreteszi a jelenlegi valtozasokat)
- Ha az AI agent teljesen szetcseszte a kodot -> `git checkout .` es kezded elorol
- **Ne hasznalj `git reset --hard`-ot** hacsak nem tudod pontosan mit csinal -- ez veglegesen torol

## GitHub CLI (`gh`) -- gyorsabb mint a bongeszo

```bash
# Telepites
brew install gh

# Bejelentkezes
gh auth login

# Repo letrehozasa az aktualis mappabol
gh repo create projekt-nev --public --source=.

# PR nyitasa
gh pr create --title "Add user auth" --body "Login es register oldal"

# PR-ek listazasa
gh pr list

# Issue letrehozasa
gh issue create --title "Bug: login nem mukodik"
```

## Branch -> commit -> merge flow

```mermaid
graph LR
    A[main] --> B[git checkout -b feature]
    B --> C[commitok]
    C --> D[git push]
    D --> E[Pull Request]
    E --> F[merge -> main]
    F --> G[auto deploy\nVercel / Railway]
```

## Fo tanulsagok
- **Commitolj gyakran** -- foleg ha AI-val dolgozol, minden mukodo allapotot ments
- **Branch minden feature-nek** -- igy a main mindig mukodik
- A `.gitignore` az elso fajl amit beallitasz -- titkok NE keruljenek GitHubra
- `git checkout .` a mentoov ha az AI agent elront valamit
- A GitHub + Vercel/Railway integracio = push es automatikusan deployol
- PR-eket erdemes atnezni mielott merge-olod, akkor is ha AI irta

## Kapcsolodo anyagok
- [[foundations/bash-es-linux-parancssor|Bash es Linux parancssor]]
- [[cloud/vercel|Vercel]]
- [[cloud/railway|Railway]]
- [[cloud/docker-alapok|Docker alapok]]
