---
tags:
  - docker
  - devops
datum: 2026-02-08
szint: "🌱 Newcomer"
kapcsolodo:
  - "[[cloud/docker-compose|Docker Compose]]"
  - "[[cloud/kubernetes-bevezeto|Kubernetes bevezeto]]"
  - "[[foundations/halozatok-es-ip-cimek|Halozatok es IP cimek]]"
  - "[[_moc/moc-docker|MOC - Docker]]"
  - "[[_moc/moc-deployment|MOC - Deployment]]"
---

# Docker alapok

## Osszefoglalo

A Docker lehetove teszi, hogy alkalmazasokat izolalt kontenerekben futtass. Minden kontener tartalmazza az appot es az osszes fuggoseget, igy barhol ugyanugy fut -- legyen az a sajat geped, egy szerver, vagy a felho.

## Jegyzetek

### Mi az a Docker es miert kell?

- Megoldja a "nalam mukodik" problemat -- a kontener mindenhol ugyanugy fut
- Konnyu, gyors, nem egy teljes virtualis gep (VM) -- csak az app es ami kell hozza
- Fejleszteshez, teszteleshez es deployhoz is ugyanaz az environment

## Build flow

```mermaid
graph TD
    A[Dockerfile] --> B[docker build]
    B --> C[Image - layerek halmaza]
    C --> D[docker run]
    D --> E[Futo kontener]
    E --> F{Van volume?}
    F -->|igen| G[Adat megmarad stopnal]
    F -->|nem| H[Adat elvesz stopnal]
```

### Dockerfile -- az uzembehelyezesi leiras

A `Dockerfile` egy lepesenkenti recept ami megmondja hogyan epuljon fel a kontener.

```dockerfile
FROM node:20-alpine       # Alap image (mibol indulunk ki)
WORKDIR /app              # Munkakonyvtar a kontenerben
COPY package*.json ./     # Fajlok bemasolasa
RUN npm install           # Parancs futtatasa build kozben
COPY . .                  # A tobbi fajl bemasolasa
EXPOSE 3000               # Melyik portot hasznalja
CMD ["npm", "start"]      # Mit futtasson indulaskor
```

**UFS (Union File System):** A Dockerfile egymas utani retegeket (layer) hoz letre. Minden `RUN`, `COPY`, `ADD` parancs egy uj reteg. Ha egy reteg nem valtozik, Docker cache-bol hasznalja -- ezert gyorsabb a rebuild.

> [!tip] Cache-barat sorrend a Dockerfile-ban
> Mindig a **ritkan valtozo dolgok jonnek elore** (pl. `COPY package.json` + `RUN npm install`), es a **surun valtozo kod utoljara** (`COPY . .`). Ha forditva csinálod, minden kodvaltozasnal ujra telepiti az osszes csomagot.

### Docker image kezeles

| Parancs | Mit csinal |
|---------|------------|
| `docker build -t appnev .` | Image epitese a Dockerfile-bol |
| `docker tag nginx nginx:v1.0` | Image megjelolese nevvel es verzioval |
| `docker images` | Helyi image-ek listazasa |

### Kontener futtatasa

| Parancs | Mit csinal |
|---------|------------|
| `docker run image-nev` | Kontener inditasa |
| `docker run -d image-nev` | Hatterben futtatas (detached) |
| `docker run -p 3000:3000 image-nev` | Port kivezetese (host:kontener) |
| `docker ps` | Futo kontenerek listazasa |
| `docker stop kontener-id` | Kontener leallitasa |

### Volumeok -- adat megosztas

```bash
docker run -v /host-home/dir:/container/home/dir image-nev
```

A `-v` (volume) osszekoti a sajat geped egy konyvtarat a kontener egy konyvtaraval. Igy:
- A kontener a sajat konyvtaradba ment adatot
- Ha a kontener meghal, az adat megmarad
- Fejleszteskor a kodod valtozasait is latja a kontener

### Registry -- image-ek tarolasa es megosztasa

A registry egy kozponti hely ahova feltoltod az image-eket es ahonnan masok (vagy a szerver) lehuzzak.

| Registry | Mire jo |
|----------|---------|
| Docker Hub | Publikus registry, alapertelmezett |
| GitHub Container Registry | [[foundations/git-es-github|Git es GitHub]]-hoz kotott, privat is lehet |
| GitLab Container Registry | GitLab projektekhez |
| Sajat/ceges registry | Pl. sajat hosted registry |

```bash
docker login registry-url                       # Bejelentkezes
docker push registry-url/image:tag             # Feltoltes
docker pull registry-url/image:tag             # Lehuzas
```

### Hasznos hibakereso parancsok

```bash
# Ki hasznalja a portot?
lsof -i :3000

# Keresd meg a futo process-t:
ps aux | grep "next"

# Old meg egyszerre az osszeset:
pkill -f "next dev"
```

## Fo tanulsagok
- A Dockerfile retegekbol all -- a sorrend szamit a cache hatekonysaga miatt
- Volume nelkul a kontener adatai elvesznek ha a kontener megall
- A registry olyan mint a GitHub, csak image-eknek -- push/pull logika
- Mindig adj verziot a tag-nek (`app:1.0`), ne csak `latest`-et hasznalj

## Kapcsolodo anyagok
- [[cloud/docker-compose|Docker Compose]]
- [[cloud/kubernetes-bevezeto|Kubernetes bevezeto]]
- [[foundations/halozatok-es-ip-cimek|Halozatok es IP cimek]]
- Tailscale
- [[_moc/moc-docker|MOC - Docker]]
- [[_moc/moc-deployment|MOC - Deployment]]
- [Container Patterns (GitHub)](https://github.com/luebken/container-patterns/tree/master)
- [Bash set flags magyarazat](https://gist.github.com/mohanpedala/585292a6bf844c4b9c94635a1038ced3)
- [ByteByteGo YouTube](https://www.youtube.com/@ByteByteGo)

## Takaritas es karbantartas

### Minden futo container leallitasa

```bash
docker stop $(docker ps -q)
```

### Minden container torlese (volume MARAD)

```bash
docker rm $(docker ps -aq)
```

Vagy ha [[cloud/docker-compose|Docker Compose]] projekten belul akarod:

```bash
# Leallit + torol, de volume-ok maradnak
docker compose down
```

> [!warning] Figyelem
> A `docker compose down` alapbol NEM torli a volume-okat. Csak a `--volumes` / `-v` flag torolne -- azt ne hasznald, hacsak nem szandekos.

### Hasznos takarito parancsok (volume-okhoz nem nyulnak)

- `docker system prune` -- torli a leallitott containereket, nem hasznalt networkokat, dangling image-eket
- `docker image prune -a` -- torli az osszes nem hasznalt image-et (helyet szabadit)
- `docker volume ls` -- megnézheted milyen volume-jaid vannak
