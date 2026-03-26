---
tags:
  - github
  - ai
  - design
  - skill
datum: 2026-03-26
szint: "🧱 Scout"
url: https://github.com/pbakaus/impeccable
kapcsolodo:
  - "[[github/ui-ux-pro-max-skill|UI UX Pro Max Skill]]"
  - "[[toolbox/claude-code|Claude Code]]"
  - "[[frontend/shadcn-ui|shadcn/ui]]"
  - "[[_moc/moc-frontend-ui|MOC - Frontend UI]]"
---

# Impeccable

**Kategória:** `design system` / `AI skill`
**URL:** https://github.com/pbakaus/impeccable

---

## Mi ez és mire jó?

> [!tldr] Egy mondatban
> Design nyelv és skill ami javítja az AI által generált frontend-ek minőségét - explicit anti-pattern-ekkel és 20 paranccsal az AI "design slop" ellen.

Az AI-ok hajlamosak mindig ugyanazt a vizuális stílust generálni: lila gradiensek, túl sok kártya, rossz kontraszt, Inter font mindenre. Az Impeccable 7 referencia-dokumentummal (tipográfia, szín, kontraszt, layout, animáció, interakció, UX writing) és explicit anti-pattern-ekkel tanítja meg az AI-t jobb döntéseket hozni.

---

## Mikor hasznos?

- **Bármilyen frontend generálásnál** - ha [[toolbox/claude-code|Claude Code]]-dal UI-t építesz, ez javítja a vizuális minőséget
- **Design audit** - `/audit` paranccsal átnézi a meglévő UI-t és javításokat javasol
- **Landing page / marketing site** - ahol a vizuális minőség különösen fontos
- **Prototípus csiszolás** - `/polish` paranccsal finomítja a meglévő komponenseket

### Mikor NE használd

- Ha saját design system-ed van (pl. Figma tokenek) - akkor az a mérvadó, nem az Impeccable
- Backend-only munkánál - nincs vizuális elem amit javítson

---

## AI-natív fejlesztés

Skill-ként telepíthető [[toolbox/claude-code|Claude Code]]-ba. A [[github/ui-ux-pro-max-skill|UI UX Pro Max Skill]]-lel kiegészítik egymást: az UI UX Pro Max a design system generálásban erős (161 iparág-specifikus szabály), az Impeccable a vizuális minőség-ellenőrzésben (anti-pattern-ek, audit, polish).

Jól kombinálható [[frontend/shadcn-ui|shadcn/ui]] és [[frontend/tailwind-css|Tailwind CSS]] alapú projektekben.

> [!tip] Hogyan használd AI-val
> "Használd az Impeccable skill-t és auditáld a landing page komponenseket. Javítsd a kontrasztot és a tipográfiát." - az AI a 7 referencia-dokumentum alapján értékeli és javítja a UI-t.

---

## Kapcsolódó

- [[github/ui-ux-pro-max-skill|UI UX Pro Max Skill]] - design system generálás (Impeccable a minőség-ellenőrzés)
- [[toolbox/claude-code|Claude Code]] - skill-ként integrálódik
- [[frontend/shadcn-ui|shadcn/ui]] - UI component library amihez jól illeszkedik
