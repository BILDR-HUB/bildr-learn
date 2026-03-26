---
tags:
  - frontend
  - ui
  - referencia
datum: 2026-03-26
szint: "🧱 Brick"
kapcsolodo:
  - "[[frontend/react-vs-spa-vs-preact|React]]"
  - "[[frontend/nextjs|Next.js]]"
---

# Data Table Filters - TanStack Table referencia

**URL:** https://github.com/openstatusHQ/data-table-filters
**Stars:** ~2K

> [!tldr] Egy mondatban
> TanStack Table playground fejlett szűrőkkel - kész referencia implementáció dashboard projektek data table komponenséhez.

---

## Mi ez és mire jó?

Egy interaktív playground ami megmutatja hogyan építs komplex, szűrhető, rendezhető data table-t TanStack Table-lel. Faceted filterek, search, column visibility, pagination - mind implementálva, kóddal.

A shadcn/ui + TanStack Table kombó a legelterjedtebb megoldás [[frontend/react-vs-spa-vs-preact|React]] alapú dashboard projekteknél.

---

## Mikor hasznos?

- **Dashboard projekt** - ha táblázatos adatmegjelenítés kell (admin panel, CRM, analytics)
- **Referencia implementáció** - nem kell nulláról kitalálni a filter logikát
- **shadcn/ui + TanStack Table kombó** - pont ez a stack amit a legtöbb [[frontend/nextjs|Next.js]] projektben használnak

### Mikor NE használd

- Egyszerű lista megjelenítéshez - túlzás ha nincs szűrés/rendezés igény
- Ha nem React a frontend - ez React-specifikus

---

## Hogyan használd

Nem skill, hanem **referencia kód**. Amikor AI coding agent-tel data table-t építesz, hivatkozhatsz erre: "Nézd meg a data-table-filters repót és implementálj hasonló faceted filter-t."

> [!tip] Példa prompt
> "Építs egy data table komponenst TanStack Table-lel, faceted filterekkel. Referencia: openstatusHQ/data-table-filters repó pattern-jei."

---

## AI-natív fejlesztés

A data table az egyik leggyakoribb komponens amit AI coding tool-okkal építenek - a TanStack Table API komplex, de jól dokumentált, és a referencia repó mintái kiválóan másolhatók. Claude Code-dal a teljes filter + rendezés + pagination logikát egyetlen promptból felépítheted.

> [!tip] Hogyan használd AI-val
> - *"Építs egy TanStack Table data table-t shadcn/ui-val, faceted filterekkel a data-table-filters repó mintájára"*
> - *"Adj hozzá server-side pagination-t és szűrést ehhez a data table-höz, az URL search params-ban tartsd a filter state-et"*

---

## Kapcsolódó

- [[frontend/react-vs-spa-vs-preact|React]] - a referencia React-specifikus
- [[frontend/nextjs|Next.js]] - a leggyakoribb framework amihez data table kell
