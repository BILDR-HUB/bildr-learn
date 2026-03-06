# MOC - Kubernetes

> [!tldr]
> Ez a Map of Content összegyűjti az összes Kubernetes-hez kapcsolódó jegyzetet. A konténer orchestration világa: alap koncepcióktól a disztribúciókig.

---

## Bevezető

| Note | Leírás |
|------|--------|
| [[cloud/kubernetes-bevezeto|Kubernetes bevezeto]] | K8s alapfogalmak: pod-ok, service-ek, deployment-ek, és miért jobb mint a Docker Compose production-ben |

## Disztribúciók

| Note | Leírás |
|------|--------|
| [[cloud/kubernetes-disztribuciok|Kubernetes disztribuciok]] | A három fő K8s verzió összehasonlítása: teljes k8s vs könnyített k3s (Rancher) és k0s (Mirantis) |

## Gyakorlat

| Note | Leírás |
|------|--------|
| Kubernetes gyakorlat OrbStack | Gyakorlati session: 7 teszt (self-healing, scale, rolling update, exec, logs, describe, hiba debug) OrbStack-en |

---

## Kapcsolódó note-ok

| Note | Kapcsolat |
|------|-----------|
| [[cloud/docker-alapok|Docker alapok]] | A Kubernetes Docker konténereket orchestrál — a Docker az alap |
| [[cloud/docker-compose|Docker Compose]] | A Compose egyetlen gépre való, a K8s ennek a "nagy testvére" |
| [[foundations/halozatok-es-ip-cimek|Halozatok es IP cimek]] | K8s networking megértéséhez hálózati alapok kellenek |

---

## Tanulási útvonal

Ajánlott sorrend ha nulláról kezded:
1. [[cloud/kubernetes-bevezeto|Kubernetes bevezeto]] — alapfogalmak, Docker Compose vs Kubernetes összehasonlítás
2. [[cloud/kubernetes-disztribuciok|Kubernetes disztribuciok]] — melyik verziót érdemes használni milyen méretű projektnél
3. Kubernetes gyakorlat OrbStack — hands-on: deploy, scale, debug OrbStack-en

---

## Hézagok

- [ ] Helm — package manager Kubernetes-hez, chart-ok használata
- [ ] K8s networking — Service, Ingress, DNS, NetworkPolicy mélyebben
- [ ] Production deployment — éles K8s cluster felállítása és üzemeltetése
- [ ] GitOps — ArgoCD / Flux, deklaratív K8s konfigurációkezelés git-ből
- [ ] AI workload orchestration — GPU pod-ok, model serving K8s-en, scaling stratégiák ML-hez
- [x] kubectl parancsok → Kubernetes gyakorlat OrbStack
- [x] K8s YAML manifesztek → Kubernetes gyakorlat OrbStack
