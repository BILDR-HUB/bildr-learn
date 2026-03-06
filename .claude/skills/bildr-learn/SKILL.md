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

**Path:** `/Users/kovacsbence/Projects/bildr-learn/`

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
| 🧱 Brick | Középhaladó, már van alapvető tapasztalat, építés |
| 🏗️ Builder | Haladó, architekturális döntések, összetett rendszerek |

## Trigger Rules

### WHEN to use this skill

- "ird meg a tudasbazisba" / "add hozza a bildr-learn-hoz"
- "knowledge base note" / "tudasbazis"
- "irj egy jegyzetet a learn repo-ba"
- Any explicit mention of "bildr-learn" + create/write/update intent

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
szint: "🌱 Newcomer"   # vagy "🧱 Brick" vagy "🏗️ Builder"
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
