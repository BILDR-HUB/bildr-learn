---
tags:
  - security
  - backend
  - alapok
datum: 2026-03-26
szint: "🧱 Brick"
kapcsolodo:
  - "[[backend/jwt|JWT]]"
  - "[[backend/webhook-verification|Webhook verification]]"
  - "[[foundations/halozatok-es-ip-cimek|Hálózatok és IP címek]]"
---

# Kriptográfia alapok fejlesztőknek

> [!tldr] Miért releváns
> Nem kell kriptográfust csinálni magadból, de ezeket a koncepciókat **napi szinten használod** - jelszó tárolás, API kulcsok, HTTPS, JWT tokenek. Ha tudod mi történik a háttérben, jobb döntéseket hozol auth implementációnál.

---

## Kulcs tanulságok

- A kriptográfia nem csak "titkosítás" - **hashing, signing, és kulcskezelés** legalább annyira fontos
- **Soha ne implementálj saját kriptográfiát** - használj bevált library-kat (bcrypt, jose, crypto modul)
- A legtöbb fejlesztő ezekkel a koncepcióval a **jelszókezelés és JWT** kapcsán találkozik először
- Az aszimmetrikus kriptográfia (publikus/privát kulcspár) az alapja a HTTPS-nek, SSH-nak, és a modern auth rendszereknek
- Ha auth provider-t használsz (Clerk, BetterAuth, Supabase), a kriptográfia nagy részét ők kezelik - de értened kell mit csinálnak

---

## A 7 koncepció

| # | Koncepció | Mi ez | Hol találkozol vele |
|---|-----------|-------|---------------------|
| 1 | **Hashing** | Egyirányú függvény: inputból fix méretű "ujjlenyomatot" csinál. Nem visszafejthető. | Jelszó tárolás, fájl integritás ellenőrzés, [[foundations/git-es-github|Git]] commit hash-ek |
| 2 | **Salt** | Random adat amit a jelszóhoz adnak hashing előtt - megakadályozza hogy két azonos jelszónak azonos legyen a hash-e. | bcrypt automatikusan kezeli, nem kell manuálisan |
| 3 | **HMAC** | Hash-based Message Authentication Code - hash + titkos kulcs kombinációja. Bizonyítja hogy az üzenet nem módosult és a küldő ismeri a kulcsot. | Webhook signature ellenőrzés (Stripe, GitHub), HS256 JWT |
| 4 | **Szimmetrikus titkosítás** | Egy kulcs titkosít és fejt vissza (AES). Gyors, nagy adatmennyiségre jó. | HTTPS-en belüli adatforgalom, adatbázis encryption at rest |
| 5 | **Aszimmetrikus titkosítás** | Publikus kulcs titkosít, privát kulcs fejt vissza (RSA, ECDSA). Lassabb, de nem kell megosztani a titkos kulcsot. | HTTPS handshake, SSH kulcsok, RS256 JWT |
| 6 | **Signing (aláírás)** | Privát kulccsal aláírsz, publikus kulccsal ellenőrizhető. Bizonyítja a küldő identitását és az üzenet integritását. | JWT signing, git commit signing, SSL certificate-ek |
| 7 | **JWT** | JSON Web Token - signed (és opcionálisan encrypted) token ami claim-eket hordoz. | Auth tokenek, API autorizáció, session management |

---

## Mentális modell: melyiket mire

```text
TITKOSÍTÁS (senki ne olvashassa)
├── Szimmetrikus (AES) → egy kulcs, gyors → adat titkosítás
└── Aszimmetrikus (RSA) → kulcspár, lassú → kulcscsere, HTTPS

INTEGRITÁS (ne lehessen módosítani)
├── Hashing (SHA-256) → egyirányú ujjlenyomat → jelszó, fájl check
├── Salt → hash + random → jelszó védelem rainbow table ellen
└── HMAC → hash + titkos kulcs → webhook signature

IDENTITÁS (ki küldte)
├── Signing → privát kulccsal aláír → JWT, git commit
└── JWT → signed token → auth, session
```

---

## Gyakorlati döntések

| Feladat | Mit használj | Miért |
|---------|-------------|-------|
| Jelszó tárolás | **bcrypt** (vagy argon2) | Salt-ot automatikusan generál, lassú (ez jó - brute force ellen véd) |
| JWT signing | **RS256** (aszimmetrikus) | Több service ellenőrizheti a publikus kulccsal |
| Webhook ellenőrzés | **HMAC-SHA256** | A provider (Stripe, GitHub) ad egy secret-et, azzal ellenőrzöd a signature-t |
| Adat titkosítás DB-ben | **AES-256-GCM** | Szimmetrikus, gyors, authenticated encryption |
| API kulcs generálás | **crypto.randomBytes** | Kriptográfiailag biztonságos random |

> [!tip] Gyakorlati tanács
> Auth implementációnál nem azért kell értened a kriptográfiát, hogy magad implementáld - hanem hogy pontosan meg tudd fogalmazni mit akarsz. Például: "Webhook endpoint kell Stripe-hoz - ellenőrizd a HMAC signature-t a request body-n a webhook secret-tel."

---

## AI-natív fejlesztés

Az AI toolok kiválóan tudják generálni a kriptográfiai boilerplate-et (bcrypt hashing, JWT signing, webhook verification), de a **döntést** neked kell meghoznod: melyik algoritmust, milyen kulcsméretet, milyen stratégiát. Ha érted az alapokat, pontosabb promptokat írsz.

> [!tip] Hogyan használd AI-val
> - *"Írj webhook verification middleware-t Hono-hoz HMAC-SHA256-tal - a secret env variable-ből jöjjön"*
> - *"Bcrypt-tel hash-eld a jelszót regisztrációnál, és ellenőrizd login-nál"*
> - *"RS256 JWT signing és verification - jose library-vel, kulcspár generálással"*

---

## Kapcsolódó

- [[backend/jwt|JWT]] - a 7. koncepció részletesen kifejtve
- [[backend/clerk|Clerk]] - managed auth, a kriptográfia nagy részét elvégzi
- [[backend/betterauth|BetterAuth]] - self-hosted auth, JWT signing konfiguráció
- [[foundations/halozatok-es-ip-cimek|Hálózatok és IP címek]] - HTTPS és TLS kontextus
