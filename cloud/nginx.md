---
tags:
  - devops
  - networking
  - reverse-proxy
datum: 2026-03-06
szint: "🏗️ Builder"
kapcsolodo:
  - "[[cloud/traefik|Traefik]]"
  - "[[cloud/hostinger|Hostinger]]"
  - "[[cloud/docker-compose|Docker Compose]]"
  - "[[cloud/docker-alapok|Docker alapok]]"
  - "[[foundations/halozatok-es-ip-cimek|Hálózatok és IP cimek]]"
---

# Nginx

**Kategoria:** `web server` / `reverse proxy` / `load balancer`
**URL:** https://nginx.org
**Ar/Terv:** Ingyenes, open source / Nginx Plus (enterprise, fizetos)

---

## Mi az a reverse proxy?

> [!tldr] A reverse proxy koncepcio
> A reverse proxy egy **kozvetito szerver** -- a kliensek nem az appodhoz csatlakoznak kozvetlenul, hanem a proxyhoz. A proxy továbbitja a kerest az app fele, majd visszaadja a választ. A kliens nem tudja (és nem is kell hogy tudja), mi van mögötte.

**Miért nem csatlakozik a kliens kozvetlenul az apphoz?**

| Probléma | Megoldás reverse proxy-val |
|---|---|
| Az app csak `localhost:3000`-en hallgat, nem a 80/443 porton | A proxy fogadja a 80/443-as forgalmat, továbbitja 3000-re |
| Több app fut egy szerveren, de csak egy 80-as port van | A proxy domain nev alapján dönti el melyik apphoz megy |
| HTTPS/SSL tanúsítvány kezeles | A proxy kezeli az SSL-t, az app mögötte HTTP-n fut |
| Statikus fájlok (CSS, kepek) lassitjak az app servert | A proxy kiszolgálja a statikus fájlokat, az app csak az API-t kezeli |

```mermaid
graph LR
    C[Bongeszo] -->|HTTPS :443| N[Nginx reverse proxy]
    N -->|HTTP :3000| A1[Next.js app]
    N -->|HTTP :8080| A2[API szerver]
    N -->|statikus fajl| S[/var/www/static]
    N -.->|SSL tanusitvany| LE[Let's Encrypt]
```

## Mi az Nginx?

> [!tldr] Egy mondatban
> Az Nginx egy **nagy teljesítményu web szerver és reverse proxy** -- statikus fájlokat szolgal ki, kereseket iranyit at app szerverekre, és SSL-t kezel. A legelterjedtebb reverse proxy megoldás VPS-eken.

Az Nginx-et eredetileg C10K probléma megoldásara irtak (10.000 egyideju kapcsolat kezelese). Async, event-driven architektúraja miatt rendkivul keves memoriaval is sok kapcsolatot kezel.

**Mikor használd:**
- [[cloud/hostinger|Hostinger]] VPS-en több service fut, domain alapu routing kell
- HTTPS / SSL tanúsítványt akarsz (Let's Encrypt + Certbot-tal ingyen)
- Node.js/Next.js appot kell `yourdomain.com`-on elérhetővé tenni
- Statikus fájlokat hatékonyan akarsz kiszolgálni
- Rate limiting, gzip tomorites, caching headerek kellenek

**Mikor NE Nginx-et használj:**
- Docker Compose-ban sok service-t kezelsz → [[cloud/traefik|Traefik]] automatikusabb
- [[cloud/railway|Railway]] vagy [[cloud/vercel|Vercel]] platformon vagy → ok kezelik a proxy reteget, nem kell Nginx

---

## Setup -- lépésrol lépésre

### 1. Telepítes (Ubuntu/Debian VPS)

```bash
sudo apt update
sudo apt install nginx

# Ellenorzes
sudo systemctl status nginx
# Bongeszöben: http://<szerver-IP> → Nginx welcome page
```

### 2. Alap könyvtárszerkezet

```
/etc/nginx/
├── nginx.conf              # fo konfig (altalaban ne bantsd)
├── sites-available/        # elerheto site konfigok
│   └── mydomain.com        # a te konfig fajlod
└── sites-enabled/          # aktiv konfigok (symlinkek)
    └── mydomain.com -> ../sites-available/mydomain.com
```

### 3. Site konfig létrehozasa

```bash
sudo nano /etc/nginx/sites-available/mydomain.com
```

#### Reverse proxy Node.js/Next.js apphoz

```nginx
server {
    listen 80;
    server_name mydomain.com www.mydomain.com;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_cache_bypass $http_upgrade;
    }
}
```

#### Több domain, egy szerveren

```nginx
# /etc/nginx/sites-available/app1.com
server {
    listen 80;
    server_name app1.com;
    location / { proxy_pass http://localhost:3000; }
}

# /etc/nginx/sites-available/app2.com
server {
    listen 80;
    server_name app2.com;
    location / { proxy_pass http://localhost:4000; }
}
```

#### Statikus fájlok kiszolgálasa

```nginx
server {
    listen 80;
    server_name static.mydomain.com;

    root /var/www/html;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }

    # Cache headerek statikus fajlokhoz
    location ~* \.(jpg|jpeg|png|gif|ico|css|js)$ {
        expires 30d;
        add_header Cache-Control "public, no-transform";
    }
}
```

### 4. Site aktivalasa

```bash
# Symlink letrehozasa sites-enabled-ben
sudo ln -s /etc/nginx/sites-available/mydomain.com /etc/nginx/sites-enabled/

# Konfig szintaxis ellenorzese (fontos!)
sudo nginx -t

# Nginx ujrainditasa
sudo systemctl reload nginx
```

### 5. SSL/HTTPS -- Let's Encrypt + Certbot

```bash
# Certbot telepitese
sudo apt install certbot python3-certbot-nginx

# Tanusitvany igenylese (Nginx-et automatikusan konfigurálja)
sudo certbot --nginx -d mydomain.com -d www.mydomain.com

# Automatikus megujitas tesztelese
sudo certbot renew --dry-run
```

A Certbot automatikusan atirja az Nginx konfig-ot -- hozzáadja a `listen 443 ssl` blokkot és a tanúsítvány elérési utjat. A megujitas automatikusan fut (cron/systemd timer).

---

## Best Practices

### Gzip tomorites

```nginx
# /etc/nginx/nginx.conf-ban, a http {} blokkon belul
gzip on;
gzip_vary on;
gzip_min_length 1024;
gzip_types text/plain text/css application/json application/javascript text/xml application/xml;
```

### Rate limiting (brute force védelem)

```nginx
# http {} blokkban definialod a zonat
limit_req_zone $binary_remote_addr zone=api:10m rate=10r/s;

# server {} blokkban alkalmazod
location /api/ {
    limit_req zone=api burst=20 nodelay;
    proxy_pass http://localhost:3000;
}
```

### Biztonság -- X-headers

```nginx
server {
    # Clickjacking vedelem
    add_header X-Frame-Options "SAMEORIGIN";
    # XSS vedelem
    add_header X-XSS-Protection "1; mode=block";
    # MIME type sniffing tiltasa
    add_header X-Content-Type-Options "nosniff";
    # Nginx verzio elrejtese
    server_tokens off;
}
```

---

## Leggyakoribb parancsok

```bash
# Konfig tesztelese (mindig futtasd modositas elott)
sudo nginx -t

# Reload (graceful -- aktiv kapcsolatokat nem szakitja meg)
sudo systemctl reload nginx

# Restart (minden kapcsolatot megszakit)
sudo systemctl restart nginx

# Logok
sudo tail -f /var/log/nginx/access.log
sudo tail -f /var/log/nginx/error.log

# Osszes aktiv site
ls /etc/nginx/sites-enabled/
```

---

## Buktatok

> [!warning] `nginx -t` nelkul ne reload-olj
> Ha a konfig szintaktikailag hibas és reload-olsz, az Nginx leall és az összes site elérhetelenevé valik. Mindig `sudo nginx -t` először.

> [!bug] 502 Bad Gateway
> Az Nginx eléri a portot, de az app nem fut vagy nem válaszol. Ellenőrizd: `systemctl status myapp` vagy `pm2 status`. Az Nginx naploban: `sudo tail /var/log/nginx/error.log`.

> [!warning] WebSocket support
> Ha az app WebSocket-et használ (pl. Next.js HMR, Socket.io), az `Upgrade` és `Connection` headereket kötelezo atadni -- lásd a fenti reverse proxy konfig-ban.

---

## Hasznos linkek

- Docs: https://nginx.org/en/docs/
- Config generator: https://www.digitalocean.com/community/tools/nginx
- Certbot: https://certbot.eff.org

---

## Kapcsolodo

- [[cloud/traefik|Traefik]] -- Docker-native alternativa, automatikus service discovery és auto SSL label-ekkel
- [[cloud/hostinger|Hostinger]] -- a VPS ahol az Nginx fut
- [[cloud/docker-compose|Docker Compose]] -- ha Docker-rel deployolsz, Nginx vagy Traefik a forgalom belepes pontja
- [[foundations/halozatok-es-ip-cimek|Hálózatok és IP cimek]] -- portok, DNS, hogyan jut el a keres a bongeszotol a szerverre
- [[cloud/docker-alapok|Docker alapok]] -- Nginx gyakran Docker konténerben fut
- Tailscale -- VPN-nel kombinalva belső service-eket vedhetsz Nginx mögött
