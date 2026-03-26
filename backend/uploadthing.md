---
tags: [backend, file-upload, api]
datum: 2026-03-26
szint: "🌱 Newcomer"
kapcsolodo:
  - "[[frontend/nextjs|Next.js]]"
  - "[[frontend/react-vs-spa-vs-preact|React]]"
  - "[[cloud/cloudflare|Cloudflare]]"
  - "[[backend/hono|Hono]]"
  - "[[backend/stripe|Stripe]]"
---

# Uploadthing

**Kategória:** `file upload` (managed upload service - Next.js natív)
**URL:** https://uploadthing.com
**Ár:** Free: 2GB storage / 2GB transfer, Pro: $10/hó (100GB)

---

## Mi ez és mire jó?

Az **Uploadthing** egy managed file upload megoldás, főleg [[frontend/nextjs|Next.js]] és [[frontend/react-vs-spa-vs-preact|React]] projektekhez. Kezeli az egész flow-t: frontend upload UI, server-side validáció (fájlméret, típus), S3-kompatibilis storage, CDN delivery. Neked nem kell S3 bucket-et konfigurálni, CORS-t állítani, presigned URL-eket generálni.

> [!tldr] Egy mondatban
> Az Uploadthing a "file upload as a service" - definiálsz egy route-ot (mit fogadsz el, mekkora, ki töltheti fel), és kapsz hozzá React komponenst + CDN URL-t. Nincs S3 konfiguráció.

---

## Összehasonlítás

| Szempont | **Uploadthing** | [[cloud/cloudflare|Cloudflare]] R2 | AWS S3 | Supabase Storage |
|----------|----------------|---------------|--------|------------------|
| **Típus** | Managed service | Object storage (DIY) | Object storage (DIY) | Managed storage |
| **Setup idő** | 10 perc | 30 perc (R2 + Workers) | 1-2 óra (IAM, CORS, presigned) | 15 perc |
| **Upload UI** | Beépített React komponens | Nincs (magad írod) | Nincs | Nincs (JS SDK van) |
| **Validáció** | Server-side route-ban | Magad implementálod | Magad implementálod | Row-level security |
| **CDN** | Beépített | Cloudflare CDN | CloudFront külön | CDN van |
| **Ár (10GB)** | $10/hó | ~$0.15/hó | ~$0.25/hó | Ingyenes tierben benne |
| **Komplexitás** | Alacsony | Közepes | Magas | Alacsony-közepes |

> [!tip] Mikor melyiket?
> - **Legegyszerűbb, Next.js projekt** - Uploadthing
> - **Árérzékeny, nagy volume** - Cloudflare R2 (legolcsóbb storage)
> - **Már Supabase-t használod** - Supabase Storage (egy helyen minden)
> - **Enterprise, teljes kontroll** - AWS S3

---

## Döntési fa

```text
File upload kell a projektben?
├── Mennyi adat?
│   ├── < 2GB -> Uploadthing free tier
│   ├── 2-100GB -> Uploadthing Pro ($10/hó) VAGY Cloudflare R2 (~$1/hó)
│   └── > 100GB -> Cloudflare R2 vagy S3 (DIY, de olcsóbb)
├── Stack?
│   ├── Next.js / React -> Uploadthing (natív integráció)
│   ├── Hono + React SPA -> Cloudflare R2 (Hono Workers-ön fut)
│   └── Bármilyen backend -> S3 SDK (univerzális)
└── Ki csinálja a UI-t?
    ├── Gyorsan kell -> Uploadthing (kész komponens)
    └── Custom UI -> R2/S3 + presigned URL + saját dropzone
```

---

## Hogyan működik?

| Lépés | Hol | Mi történik |
|-------|-----|-------------|
| 1. Route definíció | `src/app/api/uploadthing/core.ts` | Megmondod: mit fogadsz (kép, max 4MB, auth check) |
| 2. Upload UI | React komponens | `<UploadButton>` vagy `<UploadDropzone>` |
| 3. User feltölt | Kliens - Uploadthing | Presigned URL, direct upload S3-ba |
| 4. Callback | Server-side | `onUploadComplete` - file URL mentése DB-be |
| 5. Megjelenítés | Frontend | CDN URL-ről töltöd be (`utfs.io/f/...`) |

```typescript
// Server route - ennyi a backend logika
export const ourFileRouter = {
  imageUploader: f({ image: { maxFileSize: "4MB" } })
    .middleware(async ({ req }) => {
      const user = await auth(req)
      if (!user) throw new UploadThingError("Unauthorized")
      return { userId: user.id }
    })
    .onUploadComplete(async ({ metadata, file }) => {
      // file.url -> mentsd DB-be
      await db.insert(images).values({
        url: file.url,
        userId: metadata.userId,
      })
    }),
} satisfies FileRouter
```

---

## AI-natív fejlesztés

Az Uploadthing route setup és a React upload komponens integrációja tipikus boilerplate, amit Claude Code gyorsan generál. A fontos: jelezd a storage megoldást a promptban, különben az AI valószínűleg kézi multer + lokális storage megoldást ad, ami production-ben nem működik.

> [!tip] Hogyan használd AI-val
> - *"Uploadthing file router: image upload max 4MB, auth middleware-rel, onUploadComplete-ben mentsd DB-be a URL-t"*
> - *"Jelezd a storage megoldást: 'Uploadthing' (managed) vagy 'Cloudflare R2 presigned URL' (DIY) - különben az AI kézzel implementálja multer + lokális storage-dzsal"*
