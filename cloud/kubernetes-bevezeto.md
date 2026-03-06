---
tags:
  - kubernetes
  - devops
datum: 2026-02-08
szint: "🏗️ Builder"
kapcsolodo:
  - "[[cloud/docker-alapok|Docker alapok]]"
  - "[[cloud/docker-compose|Docker Compose]]"
  - "[[foundations/halozatok-es-ip-cimek|Halozatok es IP cimek]]"
  - "[[cloud/railway|Railway]]"
  - "[[_moc/moc-kubernetes|MOC - Kubernetes]]"
---

# Kubernetes bevezeto

## Osszefoglalo

A Kubernetes (K8s) kontenerek automatikus kezelesere valo: elosztja a terhelest, ujrainditja ami elhal, skalaz felfelé-lefele. A Docker Compose egy gepre jo -- a Kubernetes sok gepre, eles kornyezetbe.

## Jegyzetek

### Docker Compose vs Kubernetes

| | Docker Compose | Kubernetes |
|--|----------------|------------|
| Mire jo | Egy gep, fejlesztes, kis projektek | Tobb gep, production, nagy rendszerek |
| Skalazas | Manualis | Automatikus |
| Ha meghal egy kontener | Nem indul ujra magatol | Automatikusan ujraindul |
| Load balancing | Nincs | Beepitett |
| Komplexitas | Alacsony | Magas |
| Mikor kell | Legtobb SMB projekt | Nagy forgalom, magas rendelkezesre allas |

### Alapfogalmak

| Fogalom | Mi ez | Hasonlat |
|---------|-------|----------|
| **Cluster** | A teljes rendszer (tobb gep egyutt) | Az egesz gyar |
| **Node** | Egy gep a clusterben | Egy munkas a gyarban |
| **Pod** | A legkisebb egyseg, 1+ kontener egyutt | Egy munkaallomas |
| **Deployment** | Leirja mit futtass es hany peldanyban | Munkautasitas |
| **Service** | Stabil cim a podokhoz (load balancer) | Recepcio ami a megfelelo munkashoz iranyit |
| **Namespace** | Logikai elvalasztas (dev/staging/prod) | Kulonbozo emeletek |

### Hogyan mukodik nagyvonalakban?

```
Te: "Kerek 3 peldanyt a backend-bol"
    ↓
Kubernetes: "OK, elinditok 3 pod-ot, elosztom a gepekre"
    ↓
Meghal 1 pod → Kubernetes: "Eszrevettem, inditok ujat"
    ↓
Nagy forgalom → Kubernetes: "Tobb pod kell, skalazok 5-re"
```

```mermaid
graph TD
    A[Cluster] --> B[Node 1 gep]
    A --> C[Node 2 gep]
    B --> D[Pod]
    B --> E[Pod]
    C --> F[Pod]
    G[Service load balancer] --> D
    G --> E
    G --> F
    H[Deployment 3 replika] --> G
```

### Egy egyszeru Deployment YAML

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend
spec:
  replicas: 3                    # 3 peldany fusson
  selector:
    matchLabels:
      app: backend
  template:
    metadata:
      labels:
        app: backend
    spec:
      containers:
        - name: backend
          image: myapp/backend:1.0
          ports:
            - containerPort: 4000
```

### kubectl -- a Kubernetes CLI

| Parancs | Mit csinal |
|---------|------------|
| `kubectl get pods` | Futo pod-ok listazasa |
| `kubectl get services` | Service-ek listazasa |
| `kubectl apply -f deploy.yaml` | Konfiguracio alkalmazasa |
| `kubectl logs pod-nev` | Pod logjainak megtekintese |
| `kubectl describe pod pod-nev` | Pod reszletes info |

### Mikor NEM kell Kubernetes?

- Kis projektek, keves felhasznalo
- Egy szerveren elfer az egesz app
- Nincs dedikalt DevOps ember/tudas
- Docker Compose + egy VPS eleg (a legtobb SMB projekthez ez a helyzet)

### Managed Kubernetes szolgaltatasok

Ha megis kell K8s, ne magad uzemeltsd:

| Szolgaltatas | Hol |
|--------------|-----|
| GKE | Google Cloud |
| EKS | AWS |
| AKS | Azure |
| DigitalOcean Kubernetes | DigitalOcean |

## Fo tanulsagok
- Kubernetes = Docker kontenerek automatikus menedzselese sok gepen
- A legtobb projekthez tulzas -- Docker Compose eleg
- Ha kell, managed szolgaltatast hasznalj (GKE, EKS), ne magad uzemeltsd
- Erdemes erteni a fogalmakat akkor is ha nem hasznalod, mert mindenhol elojonnek

## Kapcsolodo anyagok
- [[cloud/docker-alapok|Docker alapok]]
- [[cloud/docker-compose|Docker Compose]]
- [[foundations/halozatok-es-ip-cimek|Halozatok es IP cimek]]
- [[cloud/railway|Railway]]
- Kubernetes gyakorlat OrbStack -- gyakorlati session ahol az itt tanult fogalmak eletre keltek
- [[_moc/moc-kubernetes|MOC - Kubernetes]]
- [ByteByteGo YouTube](https://www.youtube.com/@ByteByteGo)
