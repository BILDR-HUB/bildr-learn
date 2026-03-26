---
tags:
  - cloudflare
  - storage
  - s3
datum: 2026-03-26
szint: "🧱 Brick"
kapcsolodo:
  - "[[cloud/cloudflare|Cloudflare]]"
  - "[[cloud/cloudflare-monorepo-mappastruktura|Cloudflare Monorepo Mappastruktúra]]"
  - "[[cloud/nextjs-on-cloudflare-workers|Next.js on Cloudflare Workers]]"
---

# Cloudflare R2

**Kategória:** `object storage` / `cloud`
**URL:** https://developers.cloudflare.com/r2/

---

## Mi ez és mire jó?

> [!tldr] Egy mondatban
> S3-kompatibilis object storage **nulla egress díjjal** - fájlokat (képek, PDF-ek, backup-ok) tárolsz, és nem fizetsz a letöltésért, ami az S3 legnagyobb költségtétele.

A **Cloudflare R2** az AWS S3 alternatívája a [[cloud/cloudflare|Cloudflare]] ökoszisztémában. Ugyanazt az S3 API-t használja (tehát minden S3-kompatibilis tool és SDK működik vele), de a fő különbség: **nincs egress fee**. Az S3-nál a letöltés (egress) drága ($0.09/GB), R2-nél $0 - ez média-heavy alkalmazásoknál óriási megtakarítás.

A Cloudflare CDN-nel natívan integrált, tehát a fájlok automatikusan a legközelebbi edge-ről szolgálnak ki. Workers-ből közvetlenül elérhető binding-ekkel, nincs szükség SDK-ra.

---

## Mikor hasznos?

- **Felhasználó által feltöltött fájlok** - profilképek, dokumentumok, media az appodban
- **Statikus asset-ek** - JS/CSS bundle-ök, image CDN
- **Backup és archívum** - adatbázis dump-ok, log fájlok
- **Cloudflare Workers stack** - Workers-ből natív binding, nincs külön SDK/auth konfigurálás
- **Költségérzékeny projekt** - sok letöltés (video, image serving) esetén az S3 egress drága, R2 ingyenes

### Mikor NE használd

- **Nem Cloudflare stack** - ha AWS-ben vagy, S3 natívabb; ha GCP-ben, Cloud Storage
- **Komplex S3 feature-ök** - R2-ből hiányzik néhány S3 feature (pl. S3 Select, Glacier tiers)
- **Fájlrendszer kell** - R2 object storage, nem block storage; ha mount-olható fs kell, más megoldás kell

---

## Árazás

| Tétel | Ár |
|-------|-----|
| **Storage** | $0.015 / GB / hó |
| **Class A ops** (write) | $4.50 / millió request |
| **Class B ops** (read) | $0.36 / millió request |
| **Egress** | **$0** - ez a fő előny |
| **Free tier** | 10 GB storage, 10M read, 1M write / hó |

> [!info] Összehasonlítás: 100 GB storage + 1 TB egress/hó -> S3: ~$92/hó, R2: ~$1.50/hó

---

## R2 vs S3 vs Backblaze B2

| Szempont | Cloudflare R2 | AWS S3 | Backblaze B2 |
|----------|--------------|--------|-------------|
| **Egress** | $0 | $0.09/GB (drága) | $0.01/GB |
| **Storage** | $0.015/GB | $0.023/GB | $0.006/GB |
| **S3 kompatibilis** | Igen | Natív | Igen |
| **CDN** | Cloudflare natív | CloudFront külön | Cloudflare partnerség |
| **Edge integration** | Workers binding | Lambda@Edge | Nincs |
| **Mikor válaszd** | Cloudflare stack, sok egress | AWS stack, komplex S3 features | Cheapest storage, backup |

---

## AI-natív fejlesztés

Az R2 bucket setup és a Workers binding konfiguráció jól generálható AI-val. Claude Code ismeri a wrangler R2 binding szintaxist, a presigned URL generálást és az S3-kompatibilis SDK használatot Workers-ből.

> [!tip] Hogyan használd AI-val
> - *"Állíts be Cloudflare R2 bucket-et wrangler-rel és írj egy Workers endpoint-ot ami presigned URL-t generál fájl feltöltéshez"*
> - *"Migráld az S3 bucket-et R2-re - írj egy scriptet ami az összes objektumot átmásolja rclone-nal"*

---

## Kapcsolódó

- [[cloud/cloudflare|Cloudflare]] - R2 a Cloudflare ökoszisztéma része
- [[cloud/cloudflare-monorepo-mappastruktura|Cloudflare Monorepo Mappastruktúra]] - R2 binding a monorepo-ban
- [[cloud/nextjs-on-cloudflare-workers|Next.js on Cloudflare Workers]] - R2 integráció Workers-alapú Next.js appban
