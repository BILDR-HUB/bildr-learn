---
tags: [frontend, shadcn, ui, komponensek, tailwind]
datum: 2026-03-26
szint: "🧱 Scout"
kapcsolodo:
  - "[[frontend/tailwind-css|Tailwind CSS]]"
  - "[[frontend/base-ui-vs-radix|Base UI vs Radix]]"
  - "[[frontend/react|React]]"
  - "[[frontend/nextjs|Next.js]]"
  - "[[frontend/react-hook-form|React Hook Form]]"
---

## Mi ez és mire jó?

A **shadcn/ui** **nem** egy hagyományos komponens library (mint MUI vagy Chakra) - hanem egy **komponens gyűjtemény amit a saját kódbázisodba másolsz**. A komponensek [[frontend/base-ui-vs-radix|Radix UI]] primitívekre és [[frontend/tailwind-css|Tailwind CSS]]-re épülnek, de a kód a tiéd: módosíthatod, kiterjesztheted, törölheted.

> [!tldr] Egy mondatban
> A shadcn/ui "kész, jól kinéző, akadálymentes komponensek" amik a `components/ui/` mappádba kerülnek - nem npm dependency, hanem a te kódod, Radix + Tailwind alapokkal.

### A kulcs különbség

| | MUI / Chakra / Ant Design | shadcn/ui |
|---|---|---|
| **Telepítés** | `npm install` - dependency | `npx shadcn@latest add` - kód másolás |
| **Frissítés** | npm update (breaking changes) | Te döntöd el mikor/mit frissítesz |
| **Testreszabás** | Theme override, sx prop, hacks | Közvetlenül szerkeszted a fájlt |
| **Eltávolítás** | Uninstall + refaktor | Törlöd a fájlt |
| **Bundle méret** | Teljes lib betöltődik | Csak amit használsz |
| **Stílus** | Saját design system | Tailwind CSS + CSS változók |

---

## Hogyan működik?

### CLI - komponens hozzáadás

```bash
# Inicializálás (egyszer, projekt elején)
npx shadcn@latest init

# Komponens hozzáadása
npx shadcn@latest add button
npx shadcn@latest add dialog
npx shadcn@latest add form    # React Hook Form integráció!
```

A CLI a `components/ui/` mappába másolja a komponens fájlokat. Utána te birtoklod a kódot.

### Testreszabás - CSS változók

A `globals.css`-ben CSS változókkal irányítod a teljes design-t (színek, border-radius, stb.). Nem kell theme provider, nem kell wrapper - sima CSS.

---

## Mikor használd (és mikor NE)?

| Szituáció | shadcn/ui? | Alternatíva |
|-----------|-----------|-------------|
| Új [[frontend/react|React]]/[[frontend/nextjs|Next.js]] projekt | **Igen** - legjobb kiindulópont | - |
| Gyors prototípus | **Igen** - azonnal jól néz ki | - |
| Erős brand / egyedi design | **Igen** - teljes kontroll a kód felett | - |
| Nem React (Vue, Svelte) | Nem (React-only) | Korábbi Radix primitívek |
| Meglévő MUI/Chakra projekt | Nem - ne mixeld | Maradj a meglévőnél |

---

## shadcn/ui + React Hook Form + Zod

A shadcn/ui `<Form>` komponense **natívan integrálódik** a [[frontend/react-hook-form|React Hook Form]]-mal és Zod-dal:

```bash
npx shadcn@latest add form input select
```

A `form` komponens tartalmazza a `FormField`, `FormItem`, `FormLabel`, `FormMessage` wrappereket - ezek automatikusan kezelik a React Hook Form `register`-t és a validációs hibaüzeneteket. Nem kell kézzel kötögetni.

---

## Buktatók

- **Ne telepítsd npm-ből** - a `shadcn-ui` npm csomag nem hivatalos. Mindig a CLI-t használd (`npx shadcn@latest add`)
- **Tailwind CSS kötelező** - shadcn/ui Tailwind CSS nélkül nem működik, ez nem opcionális
- **Radix primitívek** - a shadcn/ui a Radix UI primitívekre épít, szóval a Radix docs is releváns ha mélyen testre akarsz szabni
- **Frissítés nem automatikus** - ha a shadcn/ui GitHub-on frissül egy komponens, neked manuálisan kell átvenni (vagy `npx shadcn@latest diff`)

---

## AI-natív fejlesztés

AI coding tool-ok kiválóan generálnak shadcn komponenseket - add meg a komponens nevét és a Claude Code telepíti és használja. A shadcn MCP skill-lel az AI pontosan tudja milyen komponensek vannak, milyen variánsok léteznek, és hogyan kell összerakni őket.

> [!tip] Hogyan használd AI-val
> - *"Adj hozzá shadcn/ui Dialog, Form és Select komponenseket, és építs egy szerkesztő modalt velük"*
> - *"Szabd testre a shadcn Button komponenst: adj hozzá egy loading variánst spinner-rel"*
> - *"Telepítsd a shadcn MCP-t (`claude mcp add shadcn -- npx shadcn@latest mcp`) és használd a teljes komponens registryt"*

---

## Kapcsolódó

- [[frontend/tailwind-css|Tailwind CSS]] - a stílus alap, shadcn/ui erre épül
- [[frontend/base-ui-vs-radix|Base UI vs Radix]] - a primitívek amikre a shadcn komponensek épülnek
- [[frontend/react-hook-form|React Hook Form]] - form kezelés, natív shadcn/ui integráció
- [[frontend/react|React]] - React alapok
- [[frontend/nextjs|Next.js]] - a legtöbb shadcn/ui projekt Next.js-ben fut
