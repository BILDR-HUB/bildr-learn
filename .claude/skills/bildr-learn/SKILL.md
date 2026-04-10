---
name: bildr-learn
description: "Manage the bildr.hub public knowledge base (bildr-learn repo). Use when the user asks to add, update, or organize notes in the bildr-learn knowledge base. Trigger phrases include: ird meg a tudasbazisba, add hozza a bildr-learn-hoz, knowledge base note, tudasbazis, learn repo. This skill should NOT be used for: personal Obsidian vault notes (use obsidian-note), writing code files, updating MEMORY.md or CLAUDE.md files, creating project documentation, or any task where the user does not explicitly mention bildr-learn, tudasbazis, or knowledge base."
---

# bildr-learn — Knowledge Base Manager

## Overview

Create and update markdown notes in the bildr.hub public knowledge base (`bildr-learn` git repository). This skill functions as an **active knowledge base manager** — not just a note writer, but a knowledge graph maintainer with content sanitization.

**Core capabilities:**
- **Note creation and update** — correct folder placement, frontmatter, Obsidian features (backlinks, tags)
- **Auto-backlink discovery** — discovers related notes in the repo and links them automatically
- **Knowledge-aware context** — reads related notes before writing to build on existing knowledge
- **Bidirectional linking** — updates existing notes' Kapcsolodo sections when a new note references them
- **MOC (Map of Content) management** — maintains topic hub notes that index related notes
- **Dynamic folder creation** — cluster detection, subfolder suggestion when a topic grows
- **Repo reorganization suggestions** — proactive structure improvement proposals
- **Content sanitization** — ensures no personal names, client references, or project-specific content
- **PR workflow awareness** — suggests branch creation for contributions

## Repo Configuration

**Path:** A skill mindig az aktuális working directory-ból dolgozik (a `bildr-learn` repo gyökere).

**Structure:** Reteg-alapu (layer-based) — see `references/repo-structure.md` for full details.

## bildr.hub Brand Tone

- **Nyelv:** Magyar, tegezős, közvetlen, barátságos
- **Technikai kifejezések:** angolul (Docker, Kubernetes, ORM, API, stb.)
- **Hang:** Motiváló, őszinte, növekedés-orientált
- **Szlogen:** "Build. Learn. Grow."
- **Stílusminták:**
  - Aktív igék ("Használd", "Konfiguráld", nem "Konfigurálásra kerül")
  - Gyakorlatias példák, nem akadémikus
  - "Mikor használd / Mikor NE" minták eszköz-jegyzeteknél
  - Bold kulcsfogalmakra az első előforduláskor

## Közösségi Kontextus — AI-natív Builders

A bildr.hub közössége **fiatal fejlesztőkből** áll, akik **AI-natív módon** építenek alkalmazásokat. Ez azt jelenti:

### Célközönség profil
- Claude Code, Cursor, Copilot és hasonló AI coding tool-okat használnak napi szinten
- Nem "hagyományos" módon tanulnak kódolni — az AI az elsődleges pair programming partnerük
- Gyorsan akarnak haladni, prototípusból production-be vinni dolgokat
- Inkább buildelnek mint olvasnak — a jegyzeteknek **actionable** tudást kell adniuk

### Írási irányelvek a közönségre szabva
1. **AI-first kontextus** — Ahol releváns, mindig írd le hogyan kapcsolódik az AI-asszisztált fejlesztéshez. Pl. "Ezt a tool-t Claude Code-ból is vezérelheted" vagy "MCP szerveren keresztül integrálható"
2. **Prompt-tippek** — Eszköz és framework jegyzeteknél adj `> [!tip] Hogyan használd AI-val` callout-ot: hogyan kérd el az AI coding tool-tól hogy ezt használja/konfigurálja
3. **Gyakorlatiasság** — Ne akadémikus összefoglaló legyen, hanem "így építesz ezzel valamit" megközelítés
4. **Döntéstámogatás** — A közönség gyakran választ eszközök között. Adj "Mikor használd / Mikor NE" szekciót és összehasonlító táblákat
5. **Builder hangvétel** — Motiváló, de nem cringe. Nem "király lesz!" hanem "ezzel gyorsabban haladsz" típusú pozitív framing
6. **Rövid, szkennelhető** — H2 szekciók, táblázatok, code snippet-ek, callout-ok. Hosszú bekezdések helyett strukturált tartalom

## Content Sanitization Rules

**KÖTELEZŐ** — Minden jegyzet írásánál ellenőrizd:

1. **Személyes nevek** — SOHA ne írj személyes neveket. Használj generikus kifejezéseket:
   - "Mentor", "tapasztalt fejlesztő", "senior fejlesztő", "csapattag", "a csapat"
2. **Ügyfél/projekt referencia** — SOHA ne hivatkozz konkrét ügyfélre, projektre vagy szervezetre
   - Használj: "egy belső projekt", "példaprojekt", "a csapat projektje"
3. **Céges hivatkozások** — Csak "bildr.hub" használható; ne írj más cégneveket
4. **Kód példák** — Ne használj valós neveket példákban: `{ name: 'User' }` nem `{ name: 'Bence' }`
5. **Embedded képek** — NE használj `![[Pasted image...]]` szintaxist. Használj Mermaid diagramot vagy leírásokat.

## Szint (Difficulty Level) Assignment

Minden jegyzethez rendelj `szint` értéket a bildr.hub karrierút alapján:

| Szint | Mikor |
|-------|-------|
| 🌱 Newcomer | Bevezető tartalom, első lépések, alapfogalmak |
| 🧱 Scout | Középhaladó, már van alapvető tapasztalat, építés |
| 🏗️ Builder | Haladó, architekturális döntések, összetett rendszerek |

## Trigger Rules

### WHEN to use this skill

- "ird meg a tudasbazisba" / "add hozza a bildr-learn-hoz"
- "knowledge base note" / "tudasbazis"
- "irj egy jegyzetet a learn repo-ba"
- Any explicit mention of "bildr-learn" + create/write/update intent
- URL beillesztése + bildr-learn kontextus (pl. "írd meg a tudásbázisba ezt a repo-t")
- GitHub repo URL + "learn" / "tudásbázis" / "bildr" említés

### WHEN NOT to use this skill

- Writing personal Obsidian vault notes (use obsidian-note skill instead)
- Writing or editing code files in a project
- Updating MEMORY.md, CLAUDE.md, or project docs
- General file write/edit operations not targeting bildr-learn

## Repo Index and Relationship Discovery

Every note creation or update starts with building a runtime repo index.

### Building the Repo Index

1. Run `Glob("**/*.md")` on the repo path to get all note filenames
2. Extract note titles from filenames (strip `.md` extension and path prefix, convert kebab-case to readable)
3. The full index fits in context and enables title-based matching

### Relationship Discovery (Kapcsolat-felderites)

Use the repo index to discover related notes:

**Step 1 — Title matching:**
From the repo index, identify notes whose titles are conceptually related:
- Exact substring match (e.g., "Docker multi-stage builds" matches "docker-alapok", "docker-compose")
- Semantic relationship (e.g., "PostgreSQL" matches "sql-adatbazisok", "drizzle", "prisma", "supabase")
- Technology stack relationship (e.g., "Next.js API Routes" matches "clerk", "vercel", "jwt")

**Step 2 — Content search (for top candidates):**
For the top 5 most likely related notes, use Grep to search for key terms. Maximum 3 Grep calls per operation.

**Step 3 — Classify relationships:**
- **STRONG** — Direct dependency or same topic (e.g., Docker Compose → Docker alapok)
- **MEDIUM** — Used together or complementary (e.g., Clerk → JWT)
- **WEAK** — Tangential reference — only links if content directly discusses that topic

Only STRONG and MEDIUM become backlinks. WEAK only links if content directly references that topic.

## Knowledge-Aware Context Loading

Before writing a note, load existing knowledge to build on it rather than repeat it.

1. **Select related notes** — From relationship discovery, take the top 3-5 STRONG + MEDIUM notes
2. **Read them** — Use Read tool (max 5 reads)
3. **Extract context:**
   - Key terminology used (to maintain consistency)
   - Concepts already explained in detail (to reference, not repeat)
   - Existing backlinks (to understand knowledge graph neighborhood)
   - Diagram style used (to maintain visual consistency)
4. **Apply context while writing:**
   - Reference existing explanations: "Ahogy a [[cloud/docker-alapok|Docker alapok]] jegyzetben lattuk..."
   - Don't re-explain concepts that have their own note — link to them instead
   - Fill knowledge GAPS — focus on what existing notes DON'T cover
5. **Cross-reference report** — After writing, report:
   - "Ezekre a jegyzetekre epitettem: [[Note1]], [[Note2]]"
   - "Nem ismeteltem meg: [[Topic1]], [[Topic2]] — ezeknek van sajat jegyzetuk"
   - "Hezag amit ez a jegyzet kitolt: [brief description]"

## Smart Inline Linking

After drafting note content, scan for mentions of existing repo note titles and convert to backlinks.

### Inline Linking Rules

1. For each note title in the repo index, check if that phrase (or alias) appears in the draft
2. Convert the **first occurrence** to a `[[backlink]]` — only the first mention per note
3. **Do NOT link** inside code blocks
4. Handle aliases — map common short forms to full note titles:
   - "Docker" → `[[cloud/docker-alapok|Docker]]`
   - "K8s" or "Kubernetes" → `[[cloud/kubernetes-bevezeto|Kubernetes]]`
   - Build alias mappings from the repo index at runtime
5. Do NOT link to the note being created itself
6. Backlink format: `[[mappa/fajlnev|Megjeleno nev]]`

## Bidirectional Linking Rules

After writing a new note, update related existing notes to reference it back.

### Bidirectional Update Steps

1. **Identify targets** — From relationship discovery, take all STRONG related notes (max 5 updates per operation)
2. **Read each target note** — Use Read tool
3. **Check for duplicates (idempotency)** — Verify the new note link doesn't already exist
4. **Edit:** Find the `## Kapcsolodo` section (may also be `## Kapcsolodo temak` or `## Kapcsolodo anyagok`)
   - Add `- [[mappa/fajlnev|Megjeleno nev]] — brief description` as a new bullet
5. **Handle missing section** — If no Kapcsolodo section exists:
   - Add `\n\n## Kapcsolodo\n\n- [[mappa/fajlnev|Megjeleno nev]] — brief description` at the end
6. **Use Edit tool ONLY on the Kapcsolodo section** — NEVER modify other content in existing notes
7. **One-level only** — Update direct relationships, never cascade

## MOC (Map of Content) Management

MOC notes are topic hub pages that index all related notes for a given area.

### MOC Conventions

- **Location:** `_moc/` folder
- **Naming:** `moc-[topic].md` (e.g., `moc-docker.md`)
- **Format:** Freeform with tables

### MOC Structure Template

```markdown
# MOC - [Topic]

> [!tldr]
> Ez a Map of Content osszegyujti a repo osszes [topic]-hoz kapcsolodo jegyzetet.

---

## [Sub-topic 1]

| Jegyzet | Leiras |
|---------|--------|
| [[mappa/fajlnev\|Nev]] | Egysoros leiras |

---

## Tanulasi utvonal

Ajanlott sorrend ha nullarol kezded:
1. [[mappa/alap\|Alap]] — alapok
2. [[mappa/kovetkezo\|Kovetkezo]] — erre epit
3. [[mappa/halado\|Halado]] — halado szint

---

## Hezagok

- [ ] [Topic the repo doesn't cover yet]
```

### MOC Update Logic

1. **On note creation:** Run `Glob("_moc/moc-*.md")` to find existing MOCs
2. **If relevant MOC exists:**
   - Read the MOC, find the appropriate sub-topic table
   - Check idempotency (is the note already listed?)
   - Add a new row: `| [[mappa/fajlnev\|Nev]] | One-sentence description |`
3. **If no relevant MOC exists:**
   - Check if the topic cluster has 4+ notes in the repo
   - If yes, suggest: "4+ [topic] jegyzet van a repoban. Keszitsek egy moc-[topic].md hub jegyzetet?"
4. **Gap management:**
   - When creating a MOC, identify knowledge gaps → add to "Hezagok" section
   - When a new note fills a gap, check it off: `- [x] [Topic] → [[mappa/fajlnev|Nev]]`

## Dynamic Folder Creation

The skill can suggest subfolders when a topic cluster grows large enough within a layer.

### Rules

- **Threshold:** 4+ notes belong to one sub-topic within a layer → suggest a dedicated subfolder
- **Detection:** When creating the 4th note for a sub-topic:
  1. Notice the cluster (e.g., 3 existing Kubernetes notes in `cloud/`)
  2. Suggest: "Kubernetes-hez mar 4 jegyzet tartozik a `cloud/` mappaban. Keszitsek egy `cloud/kubernetes/` almappat es mozgassam oda?"
  3. If user accepts → move notes + update all backlinks
- **Never move files without user approval**

### Repo Reorganization Suggestions

Proactively suggest reorganization when detecting:
- Scattered notes that belong to the same cluster (same sub-topic in 3+ notes across different layers)
- Notes in the wrong layer (e.g., a deployment note in `toolbox/` that belongs in `cloud/`)
- A folder with too many unrelated notes

**IMPORTANT:** Reorganization ALWAYS requires user approval. Always explain what would be moved and why.

When moving notes after user approval:
1. Move the files to the new location
2. Update all `[[backlinks]]` that reference the moved notes (Grep for the old path across the repo)
3. Update relevant MOC entries with new paths

## PR Workflow Awareness

When writing or modifying notes:

1. **Check current branch:** If on `main`, suggest: "Javaslom, hogy nyiss egy uj branch-et ehhez a valtoztatashoz: `git checkout -b note/[tema-neve]`"
2. **After writing:** Suggest committing and opening a PR
3. **For minor fixes:** A direct commit to a feature branch is acceptable

## Workflow: Creating a New Note

### Phase 1: ELOKESZITES (Preparation)

**Step 1: Build repo index**
Run `Glob("**/*.md")` on the repo path. Extract all note titles. Exclude `.claude/` and root files (README, CONTRIBUTING, CLAUDE.md).

**Step 2: Check for existing notes**
Search for existing notes on the same topic. If found, ask whether to update or create new.

**Step 3: Determine format**
All bildr-learn notes use freeform format with mandatory frontmatter:

```yaml
---
tags:
  - [fo-tema-tag]
datum: YYYY-MM-DD
szint: "🌱 Newcomer"   # vagy "🧱 Scout" vagy "🏗️ Builder"
kapcsolodo:
  - "[[mappa/fajlnev|Megjeleno nev]]"
---
```

### Phase 2: ROUTING (Folder Selection)

**Step 4: Determine target folder**
Route based on content type (réteg-alapú):

| Réteg | Mappa | Ide kerül |
|-------|-------|-----------|
| Alapok | `foundations/` | Kódolási koncept, nyelvi feature, OS, hálózat, dev alapok |
| Kliens | `frontend/` | Kliens-oldali keretrendszerek, UI, SSR/SSG |
| Szerver | `backend/` | Szerver-oldali logika, API-k, auth, edge functions |
| Adatbázis | `database/` | Adatbázis, ORM, cache, BaaS |
| Felhő | `cloud/` | Deploy, konténerizáció, orchestration, hosting platformok |
| Eszköztár | `toolbox/` | Fejlesztői eszközök, monitoring, IDE, setup |
| GitHub | `github/` | GitHub repókból felfedezett tool-ok, library-k, projektek |
| Gyakorlat | `guides/` | Step-by-step hands-on útmutatók |
| Térkép | `_moc/` | Map of Content összefoglaló (csak ha 4+ jegyzet van egy témában) |

**Step 5: File naming**
- Kebab-case, kisbetus, ASCII (ekezet nelkul)
- Jo: `docker-alapok.md` — Rossz: `Docker Alapok.md`
- NE tegyel datumot a fajlnevbe

**Step 6: Check for clusters**
If this is the 4th+ note for a sub-topic, suggest subfolder creation (see Dynamic Folder Creation).

### Phase 3: TUDASBETOLTES (Knowledge Loading)

**Step 7: Relationship discovery**
Run the Kapcsolat-felderites process:
- Title matching against repo index
- Content search with Grep (max 3 calls)
- Classify as STRONG / MEDIUM / WEAK

**Step 8: Context loading**
Read the top 3-5 STRONG + MEDIUM related notes. Extract terminology, existing concepts, and knowledge gaps.

### Phase 4: IRAS (Writing)

**Step 9: Draft note content**
- Build on loaded context, reference existing notes
- Fill knowledge gaps
- Maintain consistent terminology
- Apply bildr.hub brand tone (motivalo, kozvetlen, tegezos)

**Step 10: Content sanitization check**
Before writing, verify:
- No personal names
- No client/project references
- No company names other than bildr.hub
- No embedded images
- Code examples use generic names

**Step 11: Apply smart inline linking**
Scan draft for existing repo note titles, convert first occurrences to `[[backlinks]]`.

**Step 12: Write the file**
Use the Write tool to create the file at the determined path.

### Phase 5: GRAF KARBANTARTAS (Graph Maintenance)

**Step 13: Bidirectional link updates**
Update STRONG related notes' Kapcsolodo sections (max 5 updates).

**Step 14: MOC update**
If a relevant MOC exists, add the new note. If no MOC but 4+ notes form a cluster, suggest creating one.

**Step 15: Report to user**
Confirm with a summary:
- File path and folder
- Backlinks created (inline + Kapcsolodo)
- Existing notes updated (bidirectional links)
- MOC changes (if any)
- Reorganization suggestions (if any)
- Cross-reference report (built on, not repeated, gaps filled)
- PR suggestion (if on main branch)

## Workflow: Updating an Existing Note

1. **Find the note** — Search by name or content using Glob and Grep
2. **Read current content** — Understand structure and style
3. **Edit** — Use Edit tool on specific sections. Preserve existing structure and formatting.
4. **Knowledge loading** — If adding substantial new content, run context loading
5. **Bidirectional link updates** — If new backlinks were added
6. **MOC update** — If scope changed significantly
7. **Content sanitization check** — Verify no personal references were introduced
8. **Report to user** — Confirm changes, new links, MOC changes

## Workflow: URL-to-Note (GitHub repo / URL → jegyzet)

Amikor a user egy **URL-t** oszt meg (GitHub repo, blog post, docs oldal, tool website), hozz létre egy bildr-learn jegyzetet ami a bildr.hub közösség számára kontextualizálja a tartalmat.

### Trigger

- User beilleszt egy URL-t és azt kéri hogy legyen belőle bildr-learn jegyzet
- Explicit: "írd meg a tudásbázisba ezt a repo-t / cikket / tool-t"

### URL Type Detection

| URL pattern | Típus | Note fókusz |
|---|---|---|
| `github.com/user/repo` | GitHub repo | Eszköz note — mi ez, mikor hasznos, AI-natív workflow integráció |
| Blog post / cikk | Tanulás note | Kulcs tanulságok, gyakorlati alkalmazás, mikor releváns |
| Docs oldal | Referencia note | Összefoglaló, mikor kell, fontos szekciók |
| Tool/service website | Eszköz note | Mint GitHub repo, de a website-ról kinyerve |

### Phase 1: TARTALOM KINYERÉS

**Step 1: Fetch URL content**
Preferált sorrend:
1. `defuddle parse <url> --md` — tiszta markdown, kevesebb token
2. WebFetch fallback

Extract:
- Title / H1
- Main content (README, article body, product description)
- Metadata (author, date, language/framework)
- Ha GitHub repo: nyelv, stars, license, setup mód

**Step 2: Lényeg kinyerése**
- Egysoros összefoglaló (mi ez / miről szól?)
- Fő funkciók vagy tanulságok (max 3-5)
- Kapcsolódó technológiák (milyen stackbe illik)
- AI-natív releváns (MCP, CLI, SDK, agentic workflow?)

### Phase 2: KONTEXTUS ÉS ROUTING

**Step 3: Repo index + routing**
- Futtasd a standard repo index + relationship discovery flow-t
- Határozd meg a célmappát:
  - **GitHub repo** → MINDIG `github/` mappába (függetlenül a repo témájától)
  - Blog post / tutorial → `guides/` vagy a téma szerinti réteg
  - Docs page → a téma szerinti réteg
  - Tool/service website → a téma szerinti réteg (toolbox/, backend/, stb.)

**Step 4: AI-natív releváns meghatározása**
Minden URL-ből készült jegyzethez határozd meg az AI-natív kontextust:
- **MCP szerver** → hogyan integrálható Claude Code-ba vagy más AI tool-ba
- **CLI tool** → hogyan használható AI coding session-ben
- **Library / SDK** → hogyan használható AI-asszisztált fejlesztésnél
- **Infra tool** → hogyan segíti az AI-natív workflow-t
- **AI/LLM tool** → hogyan egészíti ki a meglévő AI tool-okat
- Ha NINCS közvetlen AI kapcsolat → a fejlesztői workflow kontextusban írd le

### Phase 3: NOTE ÍRÁS

**Step 5: Note struktúra — URL típustól függően**

#### GitHub repo / Tool website → Eszköz note:

```markdown
---
tags:
  - [fo-tema-tag]
  - eszkoz
datum: YYYY-MM-DD
szint: "[szint]"
url: [URL]
kapcsolodo:
  - "[[mappa/fajlnev|Megjelenő név]]"
---

# [Eszköz neve]

**Kategória:** `[kategória]`
**URL:** [link]

---

## Mi ez és mire jó?

> [!tldr] Egy mondatban
> [Gyakorlati összefoglaló]

[2-4 bekezdés, AI-natív builder-ek számára kontextualizálva]

---

## Mikor használd / Mikor NE

**Mikor jó:**
- [Konkrét szituációk]

**Mikor NE használd:**
- [Korlátok, jobb alternatívák]

---

## AI-natív fejlesztés

[Hogyan illeszkedik az AI-asszisztált fejlesztői workflow-ba]

> [!tip] Hogyan használd AI-val
> [1-2 példa prompt vagy integráció]

---

## Kapcsolodo

[Vault backlink-ek]
```

#### Blog post / Article → Tanulás note:

```markdown
---
tags:
  - [fo-tema-tag]
  - tanulas
datum: YYYY-MM-DD
szint: "[szint]"
forras: [URL]
kapcsolodo:
  - "[[mappa/fajlnev|Megjelenő név]]"
---

# [Téma — saját szavakkal]

> [!tldr] Miért releváns
> [1-2 mondat: miért érdemes tudni erről]

---

## Kulcs tanulságok

[3-5 pont — actionable insight-ok, nem csak összefoglaló]

---

## Hogyan alkalmazd

[Konkrét szituációk a bildr.hub builders számára]

> [!tip] Hogyan használd AI-val
> [Mikor hivatkozz erre a koncepcióra AI-val dolgozva]

---

## Kapcsolodo

[Vault backlink-ek]
```

**Szekció szabályok:**
- Az **"AI-natív fejlesztés"** vagy **"Hogyan alkalmazd"** szekció MINDIG legyen benne
- A **"Hogyan használd AI-val"** callout mindig legyen benne
- Max 80-120 sor — tömör, szkennelhető
- Setup szekció CSAK ha az eszköz közvetlenül releváns a builders számára

### Phase 4: GRÁF KARBANTARTÁS

Futtasd a standard Phase 5 (Gráf Karbantartás) flow-t: inline linking + bidirectional updates.

## Style Guidelines

### Language
- Always **Hungarian**, tegezős, közvetlen
- English technical terms preserved (Docker, API, ORM, etc.)
- NE használj `&` jelet — írd ki "és"
- NE használj `–` (en-dash) — használj `-` (kötőjel)

### Obsidian Syntax
- **Backlinks:** `[[mappa/fajlnev|Megjeleno nev]]`
- **Tags:** Use in YAML frontmatter only
- **Code blocks:** Always include language identifier
- **Tables:** Markdown tables for comparisons and structured data
- **Horizontal rules:** `---` between major sections

### Mermaid Diagrams
Use for architecture, flows, decision trees. One diagram per note is usually enough. Do NOT use embedded images.

### Callout Blocks
Use sparingly (1-3 per note):
- `> [!tip]` — Practical advice, best practices
- `> [!warning]` — Pitfalls, common mistakes
- `> [!info]` — Additional context
- `> [!tldr]` — TL;DR summaries

## Resources

### references/
- `repo-structure.md` — Complete folder structure, file naming rules, routing guide, Dynamic Folder Creation rules
