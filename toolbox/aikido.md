---
tags:
  - eszkoz
  - biztonsag
  - security
datum: 2026-03-06
szint: "🧱 Scout"
kapcsolodo:
  - "[[database/supabase|Supabase]]"
  - "[[cloud/vercel|Vercel]]"
  - "[[cloud/railway|Railway]]"
  - "[[cloud/docker-alapok|Docker alapok]]"
---

# Aikido

**Kategória:** `security` / `dev tool`
**URL:** https://aikido.dev
**Ár/Terv:** Free (1 repo) / Business (~$200/hó)

---

## Mi ez és mire jó?

> [!tldr] Egy mondatban
> Az Aikido egy fejlesztő-barát security platform: csatlakoztatod a GitHub repóhoz, és automatikusan megmondja mi a biztonsági kockázat az appodban — kód, dependency, container, cloud egyszerre.

A fejlesztők általában nem security szakértők, de deploy előtt tudni kell: nincs-e ismert CVE a dependency-kben, nincs-e hardcoded secret a kódban, nincs-e nyitva hagyva valami a cloud configban. Az Aikido ezt elvégzi helyetted.

**Mit vizsgál:**

| Kategória | Mit keres |
|---|---|
| **SAST** (kód) | SQL injection, XSS, path traversal, insecure deserialization |
| **SCA** (dependency) | Ismert CVE-k az npm/pip/composer csomagokban |
| **Secret detection** | Hardcoded API key, token, jelszó a kódban |
| **DAST** | Futó app-ot tesztel (dinamikus) |
| **Container scanning** | Docker image sebezhetőségek |
| **Cloud security** | AWS/GCP/Azure misconfigurációk |
| **IaC** | Terraform, CloudFormation hibák |
| **License compliance** | GPL/AGPL licenszek amik problémásak lehetnek |

**Mikor használd:**
- App kész van, deploy előtt biztonsági review → **Aikido futtatás**
- Open source dependency-k frissítésekor
- SOC2 / ISO27001 compliance-hez riportot kell generálni
- Ha egy új feature-t Claude Code-dal írattál és nem vagy biztos a biztonságban

**Mikor NE használd:**
- Ha még fejlesztési fázisban vagy és nap mint nap változik minden → felesleges zaj
- Teljes penetration test helyettesítőjeként → az Aikido automatizált scan, nem pentest

---

## Setup — lépésről lépésre

### 1. Regisztráció

- aikido.dev → Sign up with GitHub
- Free tier: 1 repository ingyen

### 2. Repo csatlakoztatása

- Dashboard → "Add repository" → GitHub OAuth → választod a repót
- Aikido azonnal elkezdi az első scan-t (5-10 perc)

### 3. Eredmények értelmezése

A findings-ek **severity** szerint vannak csoportosítva:

```
Critical  → Azonnal javítani, ne deploy-olj
High      → Mielőbb javítani
Medium    → Következő sprint-ben
Low       → Nice-to-have, alacsony kockázat
Info      → Tájékoztató jellegű
```

### 4. GitHub Actions integráció (CI/CD-be)

```yaml
# .github/workflows/security.yml
name: Security Scan

on:
  pull_request:
    branches: [main]

jobs:
  aikido-scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Aikido Security Scan
        uses: AikidoSec/github-actions-workflow@v1.0.12
        with:
          secret-key: ${{ secrets.AIKIDO_SECRET_KEY }}
          fail-on-timeout: false
          minimum-severity-to-fail: HIGH
```

Ez PR-onként automatikusan futtatja és elbuktatja a PR-t ha HIGH vagy CRITICAL finding van.

---

## Best Practices

### Mikor futtass scan-t

```
Feature kész
    ↓
Claude Code segítségével megírva / refaktorálva
    ↓
Aikido scan futtatása
    ↓
Critical/High finding-ek javítása
    ↓
Deploy / PR merge
```

### Dependency frissítés stratégia

- Az Aikido automatikusan jelzi ha egy dependency-nek ismert CVE-je van
- Ne frissíts vakra mindent — nézd meg mi változott (breaking changes?)
- Aikido-ban a finding-ekhez van "Fix version" info: mire kell frissíteni

### False positive kezelés

Az Aikido-ban lehet finding-eket "ignore"-olni, de mindig adj hozzá kommentet miért (pl. "Ez a kód nem érhető el publikusan, csak admin route").

---

## Hasznos linkek

- Docs: https://help.aikido.dev
- Dashboard: https://app.aikido.dev
- GitHub Action: https://github.com/AikidoSec/github-actions-workflow

---

## Kapcsolódó

- [[database/supabase|Supabase]] — adatbázis és auth, aminek a konfigját szintén vizsgálja
- [[cloud/vercel|Vercel]] — deployment platform, ahova deploy előtt futtatd az Aikido-t
- [[cloud/railway|Railway]] — backend hosting, container scanning releváns
- [[cloud/docker-alapok|Docker alapok]] — container scanning: Aikido Docker image sebezhetőségeket is vizsgál
