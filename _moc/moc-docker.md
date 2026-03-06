# MOC - Docker

> [!tldr]
> Ez a Map of Content osszegyujti az osszes Docker-hez kapcsolodo note-t. A kontenerizacio alapjaitol a multi-service orchestration-ig.

---

## Alapok

| Note | Leírás |
|------|--------|
| [[cloud/docker-alapok|Docker alapok]] | Mi az a Docker, miért kell, Dockerfile írás, image-ek, konténerek, volume-ok, hálózatok |

## Orchestration

| Note | Leírás |
|------|--------|
| [[cloud/docker-compose|Docker Compose]] | Több konténer kezelése egyetlen `docker-compose.yml` fájlból: service-ek, network-ök, volume-ok összekötése |

---

## Kapcsolódó note-ok

| Note | Kapcsolat |
|------|-----------|
| [[cloud/kubernetes-bevezeto|Kubernetes bevezeto]] | A Docker Compose "nagy testvére" — több gépre, production-re |
| Projekt-szintű izoláció | A Docker a legteljesebb izolációs megoldás |
| [[cloud/railway|Railway]] | Railway alatt Docker konténerek futnak — amit Docker-ben buildelsz, Railway-en deployolod |
| [[toolbox/tailscale|Tailscale]] | VPS-en futó Docker konténerek elérése biztonságos alagúton keresztül |

---

## Tanulási útvonal

Ajánlott sorrend ha nulláról kezded:
1. [[cloud/docker-alapok|Docker alapok]] — konténerizáció koncepció, Dockerfile, image build, `docker run` parancsok
2. [[cloud/docker-compose|Docker Compose]] — multi-service setup, `docker-compose.yml` szintaxis, service-ek összekötése

---

## Hézagok

- [ ] Multi-stage builds — optimalizált, kisebb image-ek production-re
- [ ] Docker Swarm — Docker natív orchestration (Kubernetes alternatíva)
- [ ] Registry kezelés — Docker Hub, GitHub Container Registry, privát registry-k
- [ ] Docker networking deep dive — bridge, host, overlay network-ök részletesen
- [ ] Docker security — best practice-ek, non-root user, image scanning
