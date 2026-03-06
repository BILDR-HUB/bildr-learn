---
tags:
  - hosting
  - vps
  - devops
datum: 2026-03-06
szint: "🧱 Brick"
kapcsolodo:
  - "[[cloud/railway|Railway]]"
  - "[[foundations/bash-es-linux-parancssor|Bash es Linux parancssor]]"
  - "[[_moc/moc-deployment|MOC - Deployment]]"
---

# Hostinger

**Kategoria:** `hosting` / `VPS`
**URL:** https://hostinger.com
**Ar/Terv:** KVM 1 (~$5/ho, 1 vCPU, 4GB RAM) / KVM 2 (~$8/ho, 2 vCPU, 8GB RAM)

---

## Mi ez es mire jo?

> [!tldr] Egy mondatban
> A Hostinger egy olcso VPS (Virtual Private Server) hosting -- **sajat Linux szervered van felhoben**, ahol barmit futtathatsz, teljes root hozzaferessel.

A [[cloud/railway|Railway]] es a [[cloud/vercel|Vercel]] managed platform: ok kezelik az infrastrukturat, de dragabbak es korlatozottabbak. A Hostinger VPS-en te vagy a sysadmin -- olcsobb, de tobbet kell kezzel konfiguralni.

**Mikor jobb, mint a Railway:**
- Hosszan futo background service-ek (pl. n8n)
- Fix havi ar, nem usage-alapu billing (nem lep meg a szamla)
- Teljes kontroll: barmilyen port, barmilyen service
- Olcsobb ha sok memoriat/CPU-t hasznalsz

**Mikor rosszabb, mint a Railway:**
- Nincs automatikus deploy GitHub-bol (kezzel kell SSH-val)
- Nincs managed adatbazis → sajat PostgreSQL-t kell uzemeltetni
- Sysadmin feladatok: frissitesek, tuzfal, backup = te csinálod

---

## Setup -- lepesrol lepesre

### 1. VPS megvasarlasa

- hostinger.com → VPS Hosting → KVM csomag valasztas
- OS: Ubuntu 22.04 LTS (ajanlott)
- Region: EU (Amsterdam / Frankfurt a legkozelebb)

### 2. SSH hozzaferes beallitasa

```bash
# Helyi gepen: SSH kulcs generalas (ha meg nincs)
ssh-keygen -t ed25519 -C "user@hostinger"

# Publikus kulcs masolasa a szerverre
ssh-copy-id root@<SZERVER_IP>
```

```
# ~/.ssh/config
Host myserver
    HostName <SZERVER_IP>
    User root
    IdentityFile ~/.ssh/id_ed25519
```

Ezutan: `ssh myserver` -- kesz.

### 3. Alap szerver konfiguracio (Ubuntu)

```bash
ssh myserver

# Rendszer frissites
apt update && apt upgrade -y

# Tuzfal (UFW) beallitas
ufw allow ssh
ufw allow 80
ufw allow 443
ufw enable

# Fail2ban (brute force vedelem SSH-ra)
apt install fail2ban -y
systemctl enable fail2ban
```

### 4. Service telepites Docker-rel

```bash
# Docker telepites
curl -fsSL https://get.docker.com | sh

# Pelda: n8n inditas
docker run -d \
  --name n8n \
  --restart unless-stopped \
  -p 5678:5678 \
  -v n8n_data:/home/node/.n8n \
  -e N8N_BASIC_AUTH_ACTIVE=true \
  -e N8N_BASIC_AUTH_USER=admin \
  -e N8N_BASIC_AUTH_PASSWORD=<JELSZO> \
  n8nio/n8n
```

---

## Best Practices

### Biztonsag

- **Ne hasznalj root-ot mindenre** -- hozz letre kulon usert az alkalmazasoknak
- **SSH kulcsos autentikacio** -- jelszavas SSH-t tiltsd le
- **UFW tuzfal** -- csak a szukseges portokat nyisd ki
- **Tailscale** -- ha csak sajat gepedrol kell elerni a szervert, Tailscale-lel biztonsagosabb (nem kell publikus port)

### Backup strategia

- Hostinger dashboard-ban: Snapshots (heti automatikus, de manualist is csinalhatsz deploy elott)
- Adatok backup: `docker volume` → `tar` → remote storage

### Frissitesek

```bash
# Rendszer frissites (havi rutinkent)
ssh myserver
apt update && apt upgrade -y

# Docker image frissites
docker pull n8nio/n8n
docker stop n8n && docker rm n8n
# ujrainditas ugyanazokkal a parameterekkel
```

---

## Hasznos parancsok

```bash
# Csatlakozas
ssh myserver

# Szerver statusz
htop                          # CPU, RAM, processek
df -h                         # Disk hasznalat
docker ps                     # Futo containerek
docker logs n8n --tail=50     # Logok

# Tuzfal
ufw status                    # Nyitott portok listaja

# Szerver ujrainditas
reboot
```

---

## Hasznos linkek

- Panel: https://hpanel.hostinger.com
- VPS docs: https://support.hostinger.com/en/articles/1583459
- Community: https://www.reddit.com/r/hostinger/

---

## Kapcsolodo

- [[cloud/railway|Railway]] -- managed hosting alternativa, dragabb de kevesebb sysadmin munka
- Tailscale -- VPN a VPS biztonsagos eleresehez publikus port nelkul
- n8n workflow automatizalas -- a Hostinger VPS-en fut
- [[foundations/bash-es-linux-parancssor|Bash es Linux parancssor]] -- SSH, tuzfal, szerver parancsok
- [[_moc/moc-deployment|MOC - Deployment]]

---
