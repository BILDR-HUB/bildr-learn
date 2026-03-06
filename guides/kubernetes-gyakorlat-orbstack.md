---
tags:
  - kubernetes
  - devops
  - gyakorlat
datum: 2026-02-22
szint: "🏗️ Builder"
kapcsolodo:
  - "[[cloud/kubernetes-bevezeto|Kubernetes bevezeto]]"
  - "[[cloud/docker-alapok|Docker alapok]]"
---

# Kubernetes gyakorlat OrbStack-kel

Ebben a gyakorlatban egy teljes Kubernetes demo kornyezetet epitesz fel OrbStack-en: Nginx webapp replikakkal, Redis cache-sel, Service-ekkel, es vegigcsinalod a 7 legfontosabb uzemeltetesi tesztet.

---

## Elofeltetel

Ismerd meg elobb az alapfogalmakat (pod, deployment, service, namespace) a [[cloud/kubernetes-bevezeto|Kubernetes bevezeto]]-bol, illetve a kontenerezes alapjait a [[cloud/docker-alapok|Docker alapok]]-bol.

---

## Amit epitsz

Teljes mukodo demo kornyezet a `demo` namespace-ben:
- **Nginx webapp** (2 replika) — egyedi HTML oldallal, ConfigMap-bol injektalva
- **Redis** cache — belso DNS-en erheto (`redis.demo.svc.cluster.local`)
- **Service-ek** — belso load balancing a podok kozott
- **Ingress** manifest elokeszitve (Ingress Controller nelkul port-forwarddal dolgozz)

---

## Klaszter inditas

```bash
orbctl start k8s
kubectl cluster-info
```

Az OrbStack egy konnyu Linux VM-ben futtatja a [[cloud/kubernetes-bevezeto|Kubernetes]]-t. Egyetlen node (`orbstack`), lokalisan.

---

## YAML manifesztek alkalmazasa

```bash
kubectl apply -f k8s/namespace.yaml    # demo namespace
kubectl apply -f k8s/                   # configmap, webapp, redis, ingress
```

---

## 7 teszt — csinald vegig mindegyiket

| # | Teszt | Kulcs parancs | Mit tanulsz |
|---|-------|---------------|-------------|
| 1 | **Self-healing** | `kubectl delete pod` | A Deployment automatikusan potol — deklarativ megkozelites |
| 2 | **Scale up/down** | `kubectl scale --replicas=5` | Replika szam modositas, elesben HPA csinalja |
| 3 | **Rolling update** | `kubectl rollout restart` | ConfigMap modositas → podok egyenkent cserelodnek, nincs downtime |
| 4 | **Exec** | `kubectl exec -it -- sh` | Belepes futo kontenerbe — mint a `docker exec -it` |
| 5 | **Logs** | `kubectl logs -f -l app=webapp` | Elo log figyeles label szuressel |
| 6 | **Describe** | `kubectl describe pod` | Events resz = legfontosabb debug info |
| 7 | **Szandekos hiba** | nem letezo image deploy | ImagePullBackOff — describe Events-ben a pontos ok |

### Tipp a teszteléshez

Hasznald a **watch modot** (`kubectl get pods -w`) egy kulon terminalban — valos idoben latod mi tortenik. A masik terminalban hajsd vegre az akciokat. Ez a legjobb tanulasi modszer.

---

## kubectl parancsok osszefoglalo

| Parancs | Mire jo |
|---------|---------|
| `kubectl get pods -n demo -w` | Pod-ok figyelese watch modban |
| `kubectl apply -f mappa/` | Osszes YAML alkalmazasa egy mappabol |
| `kubectl delete pod` | Pod torlese (self-healing teszt) |
| `kubectl scale deployment` | Replika szam valtoztatasa |
| `kubectl rollout restart` | Deployment ujrainditasa |
| `kubectl exec -it` | Belepes futo kontenerbe |
| `kubectl logs -f -l` | Logok figyelese label alapjan |
| `kubectl describe pod` | Reszletes info + Events |
| `kubectl get events --sort-by` | Osszes esemeny idorendben |
| `kubectl port-forward` | Belso service elerese localhost-rol |
| `kubectl wait --for=condition=Ready` | Varakozas amig a podok ready-k |

---

## Debug flow

```
1. kubectl get pods           → mi a státusz? (Running? CrashLoopBackOff?)
2. kubectl describe pod <név> → miért? (Events rész a kulcs)
3. kubectl logs <pod>         → mit mond az alkalmazás?
```

> [!tip] Events a kulcs
> A `kubectl describe` kimenetéből az **Events** rész a legfontosabb debug info. Itt látod időrendben: Scheduled → Pulled → Created → Started, vagy ha hiba van: ImagePullBackOff, CrashLoopBackOff stb.

---

## Stack értékelés

| Eszköz/Szolgáltatás | Bevált? | Megjegyzés |
|----------------------|---------|------------|
| OrbStack Kubernetes | igen | Könnyű, gyors, `orbctl start k8s` és kész |
| kubectl | igen | Minden amit csinálsz, ezzel megy |
| Nginx Alpine image | igen | Könnyű, gyors pull |
| Redis Alpine image | igen | Azonnal indul, PONG |
| port-forward | részben | Dev-re jó, rolling update-nél megszakad |

---

## Fontos tanulsagok

- A **watch + akció** minta tökéletes tanulási módszer Kubernetes-hez
- **Describe Events** az első hely ahova nézz ha valami nem működik
- **Mindig takaríts** — ne maradjon ott felesleges deployment
- **ConfigMap** > beégetett config az image-be
- A **port-forward** rolling update közben megszakadhat — ez nem éles megoldás, Ingress Controller kell
- Terminál **job control** (`Ctrl+Z` + `bg`) hasznos DevOps munka közben

---

## Kapcsolodo anyagok

- [[cloud/kubernetes-bevezeto|Kubernetes bevezeto]] — alapfogalmak amikre ez a gyakorlat épit
- [[cloud/docker-alapok|Docker alapok]] — konténerek alapjai, amire a Kubernetes épül
- [[cloud/docker-compose|Docker Compose]] — az egyszerűbb alternatíva
