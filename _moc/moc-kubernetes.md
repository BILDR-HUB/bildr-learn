# MOC - Kubernetes

> [!tldr]
> Ez a Map of Content összegyűjti az összes Kubernetes-hez kapcsolódó jegyzetet. A konténer orchestration világa: alap koncepcióktól a production üzemeltetésig.

---

## Bevezető

| Note | Leírás |
|------|--------|
| [[cloud/kubernetes-bevezeto|Kubernetes bevezeto]] | K8s alapfogalmak: pod-ok, service-ek, deployment-ek, és miért jobb mint a Docker Compose production-ben |

## Disztribúciók

| Note | Leírás |
|------|--------|
| [[cloud/kubernetes-disztribuciok|Kubernetes disztribuciok]] | A három fő K8s verzió összehasonlítása: teljes k8s vs könnyített k3s (Rancher) és k0s (Mirantis) |

## Hálózat és Csomagkezelés

| Note | Leírás |
|------|--------|
| [[cloud/helm|Helm]] | Package manager Kubernetes-hez: chart-ok használata, közösségi és saját chart-ok, környezetenkénti konfiguráció |
| [[cloud/kubernetes-networking|Kubernetes Networking]] | Service típusok, Ingress, CoreDNS, NetworkPolicy — a K8s hálózat mélyebben |

## Production

| Note | Leírás |
|------|--------|
| [[cloud/kubernetes-production-deployment|Kubernetes Production Deployment]] | Éles K8s cluster felállítása: managed vs self-hosted, HPA, monitoring, secrets, backup |
| [[cloud/gitops|GitOps]] | ArgoCD / Flux, deklaratív K8s konfigurációkezelés git-ből |

## AI és ML

| Note | Leírás |
|------|--------|
| [[cloud/ai-workload-orchestration|AI Workload Orchestration]] | GPU pod-ok, model serving K8s-en, KEDA, scale-to-zero, költség optimalizálás ML workload-okhoz |

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
| [[foundations/machine-learning-alapok|Machine Learning alapok]] | ML koncepciók amikre az AI workload orchestration épít |
| [[toolbox/orbstack|OrbStack]] | Docker és Kubernetes macOS-en — gyors lokális K8s cluster fejlesztéshez |

---

## Tanulási útvonal

Ajánlott sorrend ha nulláról kezded:
1. [[cloud/kubernetes-bevezeto|Kubernetes bevezeto]] — alapfogalmak, Docker Compose vs Kubernetes összehasonlítás
2. [[cloud/kubernetes-disztribuciok|Kubernetes disztribuciok]] — melyik verziót érdemes használni milyen méretű projektnél
3. Kubernetes gyakorlat OrbStack — hands-on: deploy, scale, debug OrbStack-en
4. [[cloud/helm|Helm]] — alkalmazások telepítése és kezelése chart-okkal
5. [[cloud/kubernetes-networking|Kubernetes Networking]] — Service, Ingress, DNS, NetworkPolicy
6. [[cloud/kubernetes-production-deployment|Kubernetes Production Deployment]] — éles cluster felállítása és üzemeltetése
7. [[cloud/ai-workload-orchestration|AI Workload Orchestration]] — GPU workload-ok, model serving

---

## Hézagok

- [x] Helm → [[cloud/helm|Helm]] — package manager Kubernetes-hez, chart-ok használata
- [x] K8s networking → [[cloud/kubernetes-networking|Kubernetes Networking]] — Service, Ingress, DNS, NetworkPolicy mélyebben
- [x] Production deployment → [[cloud/kubernetes-production-deployment|Kubernetes Production Deployment]] — éles K8s cluster felállítása és üzemeltetése
- [x] GitOps → [[cloud/gitops|GitOps]] — ArgoCD / Flux, deklaratív K8s konfigurációkezelés git-ből
- [x] AI workload orchestration → [[cloud/ai-workload-orchestration|AI Workload Orchestration]] — GPU pod-ok, model serving K8s-en, scaling stratégiák ML-hez
- [x] kubectl parancsok → Kubernetes gyakorlat OrbStack
- [x] K8s YAML manifesztek → Kubernetes gyakorlat OrbStack
