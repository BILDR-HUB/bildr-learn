# MOC - Docker

> [!tldr]
> Ez a Map of Content összegyűjti az összes Docker-hez kapcsolódó jegyzetet. A konténerizáció alapjaitól a multi-service orchestration-ig.

---

## Alapok

| Note | Leírás |
|------|--------|
| [[cloud/docker-alapok|Docker alapok]] | Mi az a Docker, miért kell, Dockerfile írás, image-ek, konténerek, volume-ok, hálózatok |
| [[cloud/docker-multi-stage-builds|Docker Multi-stage Builds]] | Optimalizált, kisebb image-ek production-re — több build fázis, csak a szükséges output |
| [[cloud/registry-kezeles|Registry kezelés]] | Docker Hub, GitHub Container Registry, privát registry-k — image-ek tárolása és megosztása |
| [[cloud/docker-networking|Docker Networking]] | Bridge, host, overlay network-ök — konténerek közötti kommunikáció részletesen |
| [[cloud/docker-security|Docker Security]] | Best practice-ek: non-root user, image scanning, secret kezelés, minimal base image |

## Orchestration

| Note | Leírás |
|------|--------|
| [[cloud/docker-compose|Docker Compose]] | Több konténer kezelése egyetlen `docker-compose.yml` fájlból: service-ek, network-ök, volume-ok összekötése |
| [[cloud/docker-swarm|Docker Swarm]] | Docker natív orchestration — több gépre, automatikus load balancing, Kubernetes alternatíva |

## Fejlesztői környezet

| Note | Leírás |
|------|--------|
| [[toolbox/dev-containers|Dev Containers]] | VS Code / Claude Code integrált fejlesztői konténerek — egységes dev környezet a csapatnak |
| [[cloud/ai-fejlesztoi-kornyezet-dockerben|AI fejlesztői környezet Dockerben]] | GPU passthrough, ML model serving konténerizálva — CUDA + PyTorch Docker-ben |

---

## Kapcsolódó note-ok

| Note | Kapcsolat |
|------|-----------|
| [[cloud/kubernetes-bevezeto|Kubernetes bevezeto]] | A Docker Compose "nagy testvére" — több gépre, production-re |
| [[foundations/projekt-szintu-izolacio|Projekt-szintű izoláció]] | A Docker a legteljesebb izolációs megoldás |
| [[cloud/railway|Railway]] | Railway alatt Docker konténerek futnak — amit Docker-ben buildelsz, Railway-en deployolod |
| [[toolbox/tailscale|Tailscale]] | VPS-en futó Docker konténerek elérése biztonságos alagúton keresztül |
| [[cloud/coolify|Coolify]] | Self-hosted PaaS — Docker-alapú deploy, Vercel/Heroku alternatíva saját VPS-en |
| [[toolbox/orbstack|OrbStack]] | Docker Desktop alternatíva macOS-en — gyorsabb, kevesebb RAM, K8s support |
| [[cloud/ci-cd-pipelines|CI/CD Pipelines]] | Docker image build és push automatizálása GitHub Actions-zel |
| [[cloud/traefik|Traefik]] | Docker-native reverse proxy — automatikus service discovery és SSL |

---

## Tanulási útvonal

Ajánlott sorrend ha nulláról kezded:
1. [[cloud/docker-alapok|Docker alapok]] — konténerizáció koncepció, Dockerfile, image build, `docker run` parancsok
2. [[cloud/docker-compose|Docker Compose]] — multi-service setup, `docker-compose.yml` szintaxis, service-ek összekötése
3. [[cloud/docker-multi-stage-builds|Docker Multi-stage Builds]] — optimalizált production image-ek
4. [[cloud/docker-networking|Docker Networking]] — hogyan kommunikálnak a konténerek
5. [[cloud/docker-security|Docker Security]] — biztonságos konténerizáció
6. [[cloud/registry-kezeles|Registry kezelés]] — image-ek tárolása és CI/CD integráció

---

## Hézagok

- [x] Multi-stage builds → [[cloud/docker-multi-stage-builds|Docker Multi-stage Builds]] — optimalizált, kisebb image-ek production-re
- [x] Docker Swarm → [[cloud/docker-swarm|Docker Swarm]] — Docker natív orchestration (Kubernetes alternatíva)
- [x] Registry kezelés → [[cloud/registry-kezeles|Registry kezelés]] — Docker Hub, GitHub Container Registry, privát registry-k
- [x] Docker networking deep dive → [[cloud/docker-networking|Docker Networking]] — bridge, host, overlay network-ök részletesen
- [x] Docker security → [[cloud/docker-security|Docker Security]] — best practice-ek, non-root user, image scanning
- [x] AI fejlesztői környezet Docker-ben → [[cloud/ai-fejlesztoi-kornyezet-dockerben|AI fejlesztői környezet Dockerben]] — GPU passthrough, ML model serving konténerizálva
- [x] Dev containers → [[toolbox/dev-containers|Dev Containers]] — VS Code / Claude Code integrált fejlesztői konténerek
