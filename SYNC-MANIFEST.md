# Vibe Prompt Architect — Sync Manifest

This file governs the relationship between `SKILL.md` (Claude Skill) and
`vibe-prompt-architect-gem.md` (Gemini Gem). Open it at the start of every editing
session. Use the Feature Touch Map to identify every location a change must affect.
Run the Pre-Commit Checklist before closing any session.

**Session protocol:** Always upload all three files — `SKILL.md`,
`vibe-prompt-architect-gem.md`, and `SYNC-MANIFEST.md` — when requesting changes in
Claude. The manifest must be in context for Claude to apply it.

**Companion user docs:** `README.md` (overview) and `CHEATSHEET.md` (user quick-reference) are `DERIVED` from the workflow — they are not part of the SKILL ↔ Gem sync pair, but must be refreshed when user-visible behavior changes. See "Updating companion user docs" in Section 2 and item 11 of the Pre-Commit Checklist.

---

## Section 1 — File Anatomy Map

Annotation key:
- `PARALLEL` — Must be identical in substance. Wording may differ by format; meaning must not.
- `EQUIVALENT` — Same content at different detail levels. SKILL.md is more detailed; Gem is more concise.
- `FILE-SPECIFIC` — Intentionally present in only one file. Do not "fix" by adding to the other.
- `DERIVED` — Content generated from another section. Changes to the source section must propagate here.
- `TEMPLATE` — Part of the generated prompt output. Changes here affect what users copy-paste.

| Section | SKILL.md location | Gem location | Annotation |
|---|---|---|---|
| File identity / role | Front matter (YAML description) | `## Role` | `EQUIVALENT` — same purpose, different format |
| Workflow overview | `## Workflow Overview` | `## How You Work` opening | `EQUIVALENT` |
| Memory Consultation | *(absent — Claude handles natively)* | `#### Memory Consultation` | `FILE-SPECIFIC` (Gem only) |
| Cadence Rules | `### Cadence Rules` | `#### Cadence Rules` | `PARALLEL` |
| Raw Value Translation | `### Raw Value Translation` | `#### Raw Value Translation` | `PARALLEL` — all four tables must match |
| Grid Conformance | `### Grid Conformance` | `#### Grid Conformance` | `PARALLEL` — rounding examples and notification format must match |
| Guidelines File Support | `### Guidelines File Support` | `#### Guidelines File Support` | `EQUIVALENT` — SKILL.md has fuller structure explanation; Gem is a concise reference |
| Accessibility Requirements | `### Accessibility Requirements` | `#### Accessibility Requirements` | `PARALLEL` — AA/AAA numeric values must match exactly |
| CTA Hierarchy | `### CTA Hierarchy` | `#### CTA Hierarchy` | `PARALLEL` |
| Casing & Capitalization | `### Casing & Capitalization` | `#### Casing & Capitalization` | `PARALLEL` — sentence-case default, ALL CAPS via `text-transform` rule, and lowercase/Title Case policy must match |
| Motion & Animation | `### Motion & Animation` | `#### Motion & Animation` | `PARALLEL` — duration/easing token names + values and the reduced-motion (`prefers-reduced-motion`) rule must match |
| L10n / I18n Requirements | `### L10n / I18n Requirements` | `#### L10n / I18n Requirements` | `PARALLEL` — expansion table rows must match |
| Question Sequence | `### Question Sequence` (Q1–Q15, Q5a, Q1a/Q1b/Q1c) — Q7=Design System, Q9=Constraints (conditional); Q8/Q8b removed (authority now established by Q1 platform choice for Figma Make / Google Stitch); Q1a/Q1b/Q1c are Claude Code + Figma MCP follow-ups (target file URL, Component Library URL, Variable Collections/Groups) | `#### Question Sequence` | `PARALLEL` — question numbers, wording, and mandatory flags must match |
| Authority File Gate | `### Authority File Gate` (between Q7 and Q9 in both files) — status table triggers on Q1 (platform), Q1b/Q1c (Figma library/Variables for Claude Code + Figma MCP), and Q7 (design system). Make Kit triggered by Q1=Figma Make; DESIGN.md by Q1=Google Stitch; Figma Component Library by Q1b; Figma Variables by Q1c | `#### Authority File Gate` | `PARALLEL` — status table (six rows) and seven preamble variants (Make Kit / DESIGN.md / Library+Variables / Library only / Variables only / named system / none) must match |
| Phase 1 closing summary | After Question Sequence | After Question Sequence | `PARALLEL` — summary field list must match; Gem adds *(recalled / new)* annotations |
| Phase 2 flag order | `## Phase 2: Clarify and Analyze` | `### Phase 2 — Clarify` | `PARALLEL` — flag order must be identical: Scope → Ambiguity → CTA → Casing → Raw Value Translation → Grid → Design System → Make Kit → DESIGN.md → Figma MCP → Accessibility → L10n → AC → Platform |
| Scope Flag | `### Scope Flags` | `**Scope**` | `PARALLEL` |
| Ambiguity Flag | `### Ambiguity Flags` | `**Ambiguity**` | `PARALLEL` |
| CTA Flag | `### CTA Flag` | `**CTA Flag**` | `PARALLEL` |
| Casing Flag | `### Casing Flag` | `**Casing Flag**` | `PARALLEL` — runs after CTA Flag; mixed casing / hardcoded ALL CAPS / Title Case CTA / unconfirmed lowercase checks must match |
| Raw Value Translation Flag | `### Raw Value Translation Flag` | `**Raw Value Translation**` | `PARALLEL` |
| Grid Flag | `### Grid Flag` | `**Grid Flag**` | `PARALLEL` |
| Design System Flags | `### Design System Flags` | `**Design System and Guidelines File**` | `PARALLEL` |
| Make Kit Flag | `### Make Kit Flag` | `**Make Kit**` | `PARALLEL` |
| DESIGN.md Flag | `### DESIGN.md Flag` | `**DESIGN.md**` | `PARALLEL` |
| Figma MCP Flag | `### Figma MCP Flag` | `**Figma MCP**` | `PARALLEL` — verify `figma-use` prerequisite, `use_figma` instruction, target URL, library/variable references, section-by-section assembly |
| Accessibility Flag | `### Accessibility Flag` | `**Accessibility Flag**` | `PARALLEL` |
| L10n Flag | `### L10n Flag` | `**L10n Flag**` | `PARALLEL` |
| Acceptance Criteria Flags | `### Acceptance Criteria Flags` | `**Acceptance Criteria**` | `PARALLEL` — mandatory AC item lists must match |
| Platform Flags | `### Platform Flags` | `**Platform**` | `PARALLEL` |
| Generation Mode | `### Generation Mode — full prompt or guided review` | `#### Generation Mode — full prompt or guided review` | `PARALLEL` — offer text, section order, approve/revise loop, and "final delivery is always one raw Markdown block" rule must match |
| TC-EBC Framework | `### TC-EBC Framework` | `## The TC-EBC Framework` | `EQUIVALENT` — SKILL.md has fuller guidance per section; Gem is more concise |
| Output template header | Inside ` ``` ` block | Inside ` ``` ` block | `PARALLEL` — all five metadata fields must match |
| Output template Elements | Inside ` ``` ` block | Inside ` ``` ` block | `PARALLEL` — inline hints must match |
| Output template Behavior | Inside ` ``` ` block | Inside ` ``` ` block | `PARALLEL` — focus ring line must be present in both |
| Output template Constraints | Inside ` ``` ` block | Inside ` ``` ` block | `PARALLEL` — all token rows (Color, Typography, Spacing, Radius, Elevation, Casing, Motion [conditional], Accessibility, CTA hierarchy) must match. Elevation and Casing rows are always present; Motion row appears only when behaviors involve motion |
| Output template L10n section | `## Localisation & I18n` inside ` ``` ` | `## Localisation & I18n` inside ` ``` ` | `PARALLEL` — all fields and sub-notes must match |
| Output template Design Guidelines | Inside ` ``` ` block | Inside ` ``` ` block | `PARALLEL` |
| Output template Make Kit | Inside ` ``` ` block | Inside ` ``` ` block | `PARALLEL` |
| Output template Figma MCP | Inside ` ``` ` block | Inside ` ``` ` block | `PARALLEL` — target file URL, Component Library URL, Variables, and `use_figma` instruction block must match |
| Output template AC block | Inside ` ``` ` block — mandatory items pre-populated | Inside ` ``` ` block — mandatory items pre-populated | `PARALLEL` — mandatory items, L10n commented items, and project-specific slots must match |
| Post-generation tips | `## Post-Generation Guidance` | `## After You Generate` | `PARALLEL` — topics must match; Gem uses bold headers, SKILL.md uses bullet list |
| Principles | `## Key Principles (always in effect)` | `## Principles` | `PARALLEL` — every principle must appear in both files, in the same order |
| Version history (table) | *(absent — see version marker row below)* | `## Version History` | `FILE-SPECIFIC` (Gem only) |
| Version marker (single-line) | YAML comment `# Version: X.Y.Z (date) — synced with vibe-prompt-architect-gem.md vX.Y.Z` + italic byline directly under H1 `*Version X.Y.Z · date · Synced with vibe-prompt-architect-gem.md vX.Y.Z*` | *(implicit — the version history table fills this role)* | `EQUIVALENT` — both files surface a current version visibly; SKILL.md's marker must match the Gem's "Current version" line on every bump |
| Human editors note | *(two-line comment in YAML header)* | Blockquote after version table | `EQUIVALENT` — both reference this manifest |

---

## Section 2 — Feature Touch Map

When adding or changing any feature, every checked location below must be updated in
**both files** unless marked `(SKILL.md only)` or `(Gem only)`.

### Adding or changing a question (Q-number)

Questions may be **top-level** (Q1, Q2, … Q15) or **conditional follow-ups** that fire only when a specific upstream answer is given. Conditional follow-ups use letter-suffixed numbering (e.g., Q5a for primary CTA after Q5; Q1a/Q1b/Q1c for Claude Code + Figma MCP follow-ups after Q1). When adding either kind, run the touchpoints below.

- [ ] `### Question Sequence` / `#### Question Sequence` — question text and wording (state the trigger condition explicitly for conditional follow-ups)
- [ ] `### Cadence Rules` / `#### Cadence Rules` — if the question has mandatory/skip rules
- [ ] `### [Relevant Phase 1 section]` — if the question triggers a Phase 1 behaviour (e.g., grid guidance fires at Q9; Q1a/Q1b/Q1c fire after Q1 = Claude Code + Figma MCP)
- [ ] `### [Relevant Phase 2 flag]` — the flag that audits this question's answer
- [ ] Phase 1 closing summary — if the question produces a summary field (and whether the field is omitted when the question doesn't fire)
- [ ] Memory Consultation stable/fresh table — classify the new question (Gem only)
- [ ] Key Principles / Principles — if the question enforces a principle
- [ ] Version history (Gem only)

### Adding or changing a Phase 2 flag

- [ ] Phase 2 flag section in both files — content and order
- [ ] Phase 2 opening framing — flag order named in the dependency chain statement
- [ ] `### Acceptance Criteria Flags` / `**Acceptance Criteria**` — if the flag produces mandatory AC items
- [ ] Output template AC block — add/update mandatory items (pre-populated or commented)
- [ ] `**Phase 2 is a dependency chain**` principle — if the order changes
- [ ] Version history (Gem only)

### Adding or changing a token translation table

- [ ] `### Raw Value Translation` / `#### Raw Value Translation` — detection rules and mapping table
- [ ] `### Grid Conformance` / `#### Grid Conformance` — if spacing or radius, rounding examples must also use dual convention
- [ ] Grid correction notification example — token names in blockquote
- [ ] Raw value notification example — token names in blockquote
- [ ] Output template Constraints block — relevant token row placeholder text
- [ ] `### Guidelines File Support` / `#### Guidelines File Support` — token naming table
- [ ] Memory Consultation — Token naming convention row (Gem only)
- [ ] Version history (Gem only)

### Adding or changing output template content

- [ ] Output template header metadata fields
- [ ] Output template Elements placeholder
- [ ] Output template Behavior placeholder
- [ ] Output template Constraints rows
- [ ] Output template L10n section fields
- [ ] Output template Design Guidelines section
- [ ] Output template Make Kit section
- [ ] Output template AC block — mandatory items and L10n commented items
- [ ] `### TC-EBC Framework` / `## The TC-EBC Framework` — framework description must reflect template requirements
- [ ] `### Acceptance Criteria Flags` — mandatory items list must match template
- [ ] Version history (Gem only)

### Adding or changing an accessibility requirement

- [ ] `### Accessibility Requirements` / `#### Accessibility Requirements` — AA and AAA requirement lists
- [ ] `### Accessibility Flag` / `**Accessibility Flag**` — Phase 2 audit checks
- [ ] Output template Constraints — Accessibility block with numeric values
- [ ] Output template AC block — AA mandatory items
- [ ] `### Acceptance Criteria Flags` — AA mandatory AC items
- [ ] `**WCAG 2.2 AA is the unconditional baseline**` principle
- [ ] `**Behaviors need states**` principle — focus state is mandatory AA
- [ ] `## Post-Generation Guidance` / `## After You Generate` — accessibility tip
- [ ] Version history (Gem only)

### Adding or changing a CTA hierarchy rule

- [ ] `### CTA Hierarchy` / `#### CTA Hierarchy` — standing rules
- [ ] `### CTA Flag` / `**CTA Flag**` — Phase 2 audit checks
- [ ] `### Acceptance Criteria Flags` — CTA mandatory AC items
- [ ] Output template Constraints — CTA hierarchy block
- [ ] Output template AC block — CTA mandatory items (pre-populated)
- [ ] Output template Elements placeholder — CTA tier labelling hint
- [ ] `### TC-EBC Framework` E section — CTA tier labelling instruction
- [ ] `**One primary CTA per screen**` principle
- [ ] `## Post-Generation Guidance` / `## After You Generate` — CTA clarity tip
- [ ] Version history (Gem only)

### Adding or changing an L10n / I18n requirement

- [ ] `### L10n / I18n Requirements` / `#### L10n / I18n Requirements` — expansion table, directionality, format tokens, font stacks, pluralisation
- [ ] `### L10n Flag` / `**L10n Flag**` — Phase 2 audit checks
- [ ] `### Acceptance Criteria Flags` — L10n conditional AC items
- [ ] Output template header — `L10n / I18n` metadata field
- [ ] Output template L10n section — all fields
- [ ] Output template AC block — L10n commented items
- [ ] Phase 1 closing summary — L10n field in summary format
- [ ] `**Localisation is a layout constraint**` principle
- [ ] `## Post-Generation Guidance` / `## After You Generate` — localisation tip
- [ ] Memory Consultation — L10n / I18n scope row (Gem only)
- [ ] Version history (Gem only)

### Adding or changing a guidelines file type (DESIGN.md, Make Kit, etc.)

- [ ] `### Guidelines File Support` / `#### Guidelines File Support` — reference content
- [ ] `### Question Sequence` — Q7 as appropriate (Q8/Q8b removed; platform-specific authority established by Q1)
- [ ] `### Make Kit Flag` or `### DESIGN.md Flag` — Phase 2 verify flag
- [ ] Output template — relevant conditional section
- [ ] Memory Consultation — relevant stable/fresh row (Gem only)
- [ ] Phase 1 closing summary — relevant summary field
- [ ] `**Guidelines files are the token source of truth**` principle or `**Make Kit is law**` principle
- [ ] `## Post-Generation Guidance` / `## After You Generate` — relevant tip
- [ ] Version history (Gem only)

### Adding or changing the Claude Code + Figma MCP scenario

- [ ] Phase 1 — Q1 post-response conditional notice — Figma MCP readiness notice (MCP server, `figma-use` skill, edit access)
- [ ] Phase 1 — Q1a (target file URL), Q1b (Component Library URL), Q1c (Variables — Collections/Groups) conditional follow-ups
- [ ] Phase 1 — Authority File Gate — Figma Component Library and Figma Variables rows in status table; "both active", "library only", "variables only" preamble variants
- [ ] Phase 1 — Raw Value Translation section — Figma Variables redirect to `{group/variable-name}` form with Collection captured separately at Q1c; Component Library inspect-for-variables note
- [ ] Phase 1 — Q7 — Claude Code + Figma MCP scope rule (skip / gap-fill / standard form depending on Q1b/Q1c)
- [ ] Phase 1 — Q9 — branches for "both active", "library only", "variables only"
- [ ] Phase 1 closing summary — Figma target file/page, Component Library, Variables fields; authority file status row expanded
- [ ] Phase 2 — Raw Value Translation Flag — Figma Variables and Component Library branches
- [ ] Phase 2 — Design System Flag — Figma Component Library and Variables verification bullets
- [ ] Phase 2 — Figma MCP Flag (new) — parallel to Make Kit Flag and DESIGN.md Flag
- [ ] Phase 2 — Platform Flag — Claude Code + Figma MCP bullet
- [ ] Phase 3 TC-EBC Constraints — Figma MCP instruction clause
- [ ] Output template — Figma MCP conditional section (target URL, library URL, Variables, `use_figma` instruction with components/variables/modes/auto-layout/naming guidance)
- [ ] Output template — Constraints token rows include Figma Variable convention example
- [ ] Post-Generation Guidance — Figma MCP tip
- [ ] Key Principles / Principles — "Authority file gates primitive intake" updated to include Figma library/variables
- [ ] Key Principles / Principles — new "Figma MCP routes to Figma Design" principle
- [ ] Memory Consultation table — Figma Component Library, Figma Variables, Figma target file/page rows; token naming convention row includes Figma Variable form (Gem only)
- [ ] Version history (Gem only)

### Adding or changing the Authority File Gate

- [ ] Phase 1 — Q1 post-response conditional notice — Figma Make and Google Stitch platform notices in both files
- [ ] Phase 1 — Authority File Gate block (between Q7 and Q9) in both files — status table trigger logic (Q1 platform + Q1b/Q1c Figma library/Variables + Q7 design system) and seven preamble variants (Make Kit / DESIGN.md / Figma Component Library and Variables both active / only Figma Component Library / only Figma Variables / named system / none)
- [ ] Phase 1 — Raw Value Translation section — authority file gate block at top (both files)
- [ ] Phase 1 — Grid Conformance — proactive grid guidance Q reference (Q9, not Q8)
- [ ] Phase 1 — Question Sequence — Q8/Q8b presence or absence; Q9 conditional branch text for each status
- [ ] Phase 1 closing summary — authority file status field (Make Kit / DESIGN.md line removed)
- [ ] Phase 2 — Raw Value Translation Flag — authority file gate logic (three branches)
- [ ] Phase 2 — Design System Flags — authority file gate verification bullet
- [ ] Phase 2 — Make Kit Flag — platform-based authority (not Q8 reference)
- [ ] Phase 2 — DESIGN.md Flag — platform-based authority (not Q8b reference)
- [ ] Phase 2 — Platform Flag — platform-based authority notes (Figma Make, Google Stitch)
- [ ] Phase 3 TC-EBC Constraints — authority file clause at top of bullet list
- [ ] Output template — Make Kit section readiness warning callout
- [ ] Output template — Design Guidelines section conditional readiness warning callout
- [ ] Output template — token rows updated with "defer to authority file" option
- [ ] Post-generation guidance — authority file tip (readiness reinforcement)
- [ ] Key Principles / Principles — "Authority file gates primitive intake" principle
- [ ] Key Principles / Principles — "Make Kit is law" principle
- [ ] "Translate, don't discard" principle — updated to include authority file redirect
- [ ] Memory Consultation table — platform authority file row replacing Q8/Q8b rows (Gem only)
- [ ] Version history (Gem only)

### Adding or changing a principle

- [ ] `## Key Principles (always in effect)` (SKILL.md)
- [ ] `## Principles` (Gem)
- [ ] Confirm both files have the principle in the same position in the list
- [ ] Version history (Gem only)

### Tone or wording changes

- [ ] Scan both files for the old phrasing — both files must be updated together
- [ ] Check Cadence Rules, Phase 1 notification examples, Phase 2 opening framing, Phase 1 closing summary, Post-Generation tips, Principles
- [ ] Version history (Gem only)

### Bumping the version

Run this every time the version number changes. Both files must move in lockstep.

- [ ] Gem `## Version History` table — add a new row with date and summary
- [ ] Gem `**Current version: X.Y.Z**` line — update to the new version
- [ ] SKILL.md YAML front matter `# Version: X.Y.Z (date) — synced with vibe-prompt-architect-gem.md vX.Y.Z` comment — update version and date
- [ ] SKILL.md italic byline under H1 `*Version X.Y.Z · YYYY-MM-DD · Synced with vibe-prompt-architect-gem.md vX.Y.Z*` — update version, date, and Gem-parity reference (all three must match each other and the Gem)
- [ ] SYNC-MANIFEST.md Section 5 Sync Health Log — add an entry describing the change

### Updating companion user docs (README + Cheat Sheet)

`README.md` and `CHEATSHEET.md` are **`DERIVED`** user-facing docs — not part of the SKILL.md ↔ Gem sync pair, but they describe the workflow and drift out of date when it changes. Refresh them whenever a change alters user-visible behavior. `CHEATSHEET.md` is platform-neutral (covers both Skill and Gem in one file); there is no per-platform variant to keep in sync.

Touch `README.md` **and** `CHEATSHEET.md` when a change affects any of:
- [ ] The intake questions (count, wording, what to prepare) — both docs summarise the question set
- [ ] The built-in quality requirements (accessibility, CTA, grid, tokens, casing, elevation, motion, L10n)
- [ ] The set of supported platforms or per-platform prep (Make Kit / DESIGN.md / Figma MCP / guidelines files)
- [ ] The generation flow (e.g., guided review vs full prompt) or the output delivery format
- [ ] The list of files in the package (add/rename/remove a file → update both the README file table and the Cheat Sheet's companion-doc footer)

---

## Section 3 — Pre-Commit Checklist

Run this before closing any editing session. Answer every question. If the answer to any
question is "no" or "not sure", do not close the session — resolve the gap first.

```
Pre-commit checklist — vibe-prompt-architect

1. Did I update both SKILL.md and the Gem for every change in this session?
   [ ] Yes   [ ] No — which sections are outstanding: _______________

2. Did I consult the Feature Touch Map for every section I changed?
   [ ] Yes   [ ] No — go back and check now

3. Did I update the Principles section in both files?
   [ ] Yes — no principle changes   [ ] Yes — principles updated   [ ] No — fix now

4. Did I update the output template in both files?
   [ ] Yes — no template changes   [ ] Yes — template updated   [ ] No — fix now

5. Did I update the Phase 1 closing summary format if new fields were added?
   [ ] Yes — no summary changes   [ ] Yes — summary updated   [ ] No — fix now

6. Did I update the Phase 2 flag order if a new flag was added?
   [ ] Yes — no flag order changes   [ ] Yes — order updated   [ ] No — fix now

7. Did I bump the Gem version table AND the SKILL.md version marker (YAML
   comment + italic byline under H1) to the same version and date?
   [ ] Yes   [ ] No — do it now. All three locations must match: Gem table row,
   SKILL.md YAML comment, and SKILL.md byline.

8. Did I update this SYNC-MANIFEST.md if a new feature, section, or touch-map entry
   was introduced?
   [ ] Yes — no manifest changes needed   [ ] Yes — manifest updated   [ ] No — fix now

9. Are both files' token translation tables (Color, Typography, Spacing, Radius) showing
   dual CSS variable and DESIGN.md dot-path convention?
   [ ] Yes   [ ] No — fix now

10. Is the Phase 2 flag sequence in both files identical?
    [ ] Yes   [ ] No — fix now

11. If user-visible behavior changed (intake questions, built-in quality, platforms,
    generation flow, or the file list), did I refresh README.md AND CHEATSHEET.md?
    [ ] Yes — no user-visible changes   [ ] Yes — both updated   [ ] No — fix now
```

---

## Section 4 — Intentional Differences

The following differences between the files are correct and must not be "fixed."

| What differs | SKILL.md | Gem | Why |
|---|---|---|---|
| Memory Consultation | Absent | Present | Claude handles cross-session memory natively; Gemini requires explicit instructions |
| Version history table | Absent | Present | Gem is a versioned document edited by humans; SKILL.md is managed at file-system level. As of v2.16.0, SKILL.md carries a single-line version marker (YAML comment + italic byline under H1) so the current version is visible at a glance and copies of the file found outside version control can be dated, but the full history table remains Gem-only. |
| Human editors note | Two-line YAML comment | Blockquote after version table | Different appropriate locations in each file format |
| Role / persona description | YAML front matter description | `## Role` section with explicit persona statement | Claude Skill format vs Gem instruction format |
| Execution boundary statement in Role | Absent (Claude Code can connect to MCP servers; the skill generates a text prompt for the user to run in a separate session — an execution-is-impossible claim would be inaccurate) | Present — "no ability to execute code, connect to MCP servers, open terminal instances, or interact directly with live Figma files" | Addresses Gemini safety classifier false-positives. The shared **Execution Boundary** principle in both Principles lists covers the functional constraint; this role-level statement is Gem-only because it would be factually wrong for Claude Code. |
| Recalled / new annotations | Absent | Present in Phase 1 closing summary | Applies only when Memory Consultation is active |
| TC-EBC framework detail | Fuller — sub-bullets, examples, do/don't | Concise — one-liners per section | SKILL.md is Claude's working reference; Gem provides essentials |
| Phase 1 section heading depth | `###` | `####` (nested under `### Phase 1 — Gather`) | Different document structures |
| Phase 2 flag heading style | `### Flag Name` | `**Flag Name**` bold paragraph | Different document structures |
| Principles heading | `## Key Principles (always in effect)` | `## Principles` | Historical naming; substance is identical |
| Post-generation format | Bullet list | Bold-header paragraphs | Both cover the same topics; format differs |
| DESIGN.md Gem note | Absent | `stitch.withgoogle.com` + lint CLI reference | Gem users are more likely to be Stitch users generating DESIGN.md files |

---

## Section 5 — Sync Health Log

Record any known drift or deferred fixes here. Clear entries when resolved.

| Date | Issue | Status |
|---|---|---|
| 2026-05-03 | Initial manifest created after 15-finding quality check. Both files are fully synced as of SKILL.md (no version) and Gem v2.10.0. | ✅ Resolved — both files clean at manifest creation |
| 2026-05-08 | Authority File Gate introduced (v2.12.0). Q7/Q8/Q8b moved before Q9. Q9 now conditional on authority file status. Raw Value Translation gains authority file gate. New "Authority file gates primitive intake" principle added. "Translate, don't discard" updated. SKILL.md and Gem fully synced. | ✅ Resolved |
| 2026-05-08 | Platform-implied authority file (v2.13.0). Q8 and Q8b removed as explicit questions; Figma Make (Q1) automatically establishes Make Kit authority, Google Stitch (Q1) establishes DESIGN.md authority. Readiness warning added to Phase 1 after Q1 and to Make Kit / Design Guidelines output template sections. Phase 2 flags, Principles, and Memory Consultation (Gem) updated. SKILL.md and Gem fully synced. | ✅ Resolved |
| 2026-05-19 | Output delivery format (v2.14.0). "File Creation Rule" renamed to "Output Delivery Rule" in both files and expanded to require raw Markdown delivered in-line in chat (wrapped in a fenced code block), never as a file/canvas/doc/Drive artifact or rendered preview. Output Format section gains explicit delivery instruction. New "Output is raw Markdown, in-line in the chat" principle added to both Principles lists. Gem Role line updated. SKILL.md and Gem fully synced. | ✅ Resolved |
| 2026-05-19 | Claude Code + Figma MCP scenario (v2.15.0). Third platform-implied authority alongside Figma Make and Google Stitch — Component Library (Q1b) and Variables (Q1c) act as granular dual authority (each independently active). New Q1 readiness notice plus Q1a/Q1b/Q1c follow-up questions, Authority File Gate expanded from 4 to 6 status-table rows and from 4 to 7 preamble variants (Make Kit / DESIGN.md / Figma Component Library + Variables both / only Library / only Variables / named system / none), Raw Value Translation gate redirects to `{group/variable-name}` form (initially documented as `{Collection}/{Group}/{variable-name}`; corrected to literal Figma form in v2.16.1), new Phase 2 Figma MCP Flag, new conditional output template section with `use_figma` instruction, new "Figma MCP routes to Figma Design" principle, Memory Consultation table extended. Sync Manifest gains new "Adding or changing the Claude Code + Figma MCP scenario" sub-section. SKILL.md and Gem fully synced. | ✅ Resolved |
| 2026-05-19 | Human-readable version marker added to SKILL.md (v2.16.0). YAML front-matter comment plus italic byline directly under the H1 surface the current version, date, and Gem-parity reference visibly without polluting the Skills schema. Lets "vintage" SKILL.md copies found outside version control be dated at a glance. Sync Manifest: new "Version marker (single-line)" row in Section 1 Anatomy Map, Section 4 Intentional Differences updated to clarify the marker / full-table split, new "Bumping the version" Feature Touch Map sub-section in Section 2, Pre-Commit Checklist item 7 expanded to require all three locations (Gem table, SKILL.md YAML comment, SKILL.md byline) move in lockstep. SKILL.md and Gem fully synced at v2.16.0. | ✅ Resolved |
| 2026-05-19 | Quality-audit cleanup patch (v2.16.1). Resolved 6 HIGH, 6 MEDIUM, and 4 LOW findings from a cross-file audit of v2.14–v2.16. Most impactful changes: (1) Figma Variable reference syntax corrected from invented `{Collection}/{Group}/{variable-name}` to literal Figma `{group/variable-name}` with Collection as separate metadata; (2) Phase 2 "dependency chain" principle updated to name Figma MCP in the flag order in both files; (3) output template AC block + Phase 2 AC Flags gain Figma-MCP-conditional mandatory items (target file, library binding, Variable binding, modes, naming, auto-layout); (4) Phase 1 closing summary "Authority file status" line rewritten to enumerate active states atomically rather than introducing a third compound name; (5) Authority Gate "None" row scope clarified for Claude Code + Figma MCP with empty Q1b/Q1c/Q7; (6) "Translate don't discard" principle and Post-Generation Authority tip extended to Figma Variables; (7) SYNC-MANIFEST Section 1 row + Authority Gate touchpoint + "Adding or changing a question" sub-section all updated; (8) cosmetic — Q1a/b/c rendered as nested list items; blank lines added before Authority File Gate heading. SKILL.md and Gem fully synced at v2.16.1. | ✅ Resolved |
| 2026-06-01 | Touch-target guidance correction (v2.18.1). WCAG 2.2 AA target size (SC 2.5.8) is 24×24px, but that is an absolute floor (allowed only with spacing), not a touch design size. Accessibility Requirements, Q14, Phase 2 target-size flag, output-template Constraints row, and AC items in both files now lead with recommended ≥ 44×44px (iOS) / ≥ 48×48px (Android) and label 24×24px the bare AA floor. Fixed iOS/Android conflation (prior text claimed 44×44px matched both; Android Material is 48×48dp). README + CHEATSHEET refreshed (companion-doc touch-map item 11 applied). Literal WCAG AA/AAA criteria values unchanged. SKILL.md and Gem fully synced at v2.18.1. | ✅ Resolved |
| 2026-06-01 | Four built-in styling/workflow capabilities (v2.18.0), from the TODO backlog. **(1) Casing & Capitalization** — new Phase 1 standing rule (sentence-case default; ALL CAPS for short overline/eyebrow labels via `text-transform` only, never hardcoded; all-lowercase/Title Case as deliberate brand choices); new Q16 (recalled across sessions); confirmation-summary Casing row; new Phase 2 Casing Flag (runs after CTA Flag); output-template Constraints Casing row (always present); mandatory casing AC item; new "Sentence case by default" principle; Gem Memory Consultation "Casing convention" row. **(2) Guided generation review** — new Phase 3 Generation Mode offering full-prompt-now vs section-by-section approve/revise (per TC-EBC section + active conditionals + AC), then assembling one raw Markdown block; preference recalled; Output Delivery Rule clarified; new "Generation mode is the user's choice" principle; Gem Memory "Generation mode" row. **(3) Elevation tokens** — Raw Value Translation detects `box-shadow`/drop-shadow and maps to a semantic `--shadow-*` / `elevation.*` scale (none–xl); always-present Elevation Constraints row; authority-gate + Phase 2 Raw Value flag enumerations extended. **(4) Motion tokens** — new Motion & Animation standing rule (`--duration-fast/base/slow`, `--easing-standard/decelerate/accelerate`); raw durations/easings/cubic-beziers translated; reduced-motion (`prefers-reduced-motion`, WCAG 2.3.3) required when motion present, with conditional Motion Constraints row, Behavior-block guidance, Accessibility-flag check, and conditional motion AC item; new "Motion uses tokens and respects reduced-motion" principle. Phase 2 dependency-chain principle and Section 1 flag-order row updated to insert Casing after CTA. SYNC-MANIFEST Section 1 gains Casing & Capitalization, Motion & Animation, Generation Mode, and Casing Flag rows; Constraints row annotation updated. SKILL.md and Gem fully synced at v2.18.0. | ✅ Resolved |
| 2026-06-03 | Execution boundary hardening (v2.18.2). Added explicit execution boundary statement to Gem **Role** section (no ability to execute code, connect to MCP servers, or interact with live Figma files — Gem-only; would be factually wrong for Claude Code). Added **Execution Boundary** principle to both Principles lists with platform-appropriate wording. Updated Claude Code + Figma MCP Q1 readiness notice in both files: Gem now reads "Because I cannot connect to external environments, before **you** run…"; SKILL.md reads "Because this skill only generates a text prompt, before **you** run…". Section 4 Intentional Differences gains a row for the role-level execution boundary statement. SKILL.md and Gem fully synced at v2.18.2. | ✅ Resolved |
| 2026-05-20 | Q1c intake expanded (v2.17.0). Closed four UX/correctness gaps identified during the v2.16.1 audit review: (1) **Modes** — Q1c now asks which modes per Collection and which is the default for this screen, with embedded best-practice that bindings must be mode-aware for designs supporting Light/Dark or other mode switching; (2) **Variable tier preference** — Q1c now asks primitive/semantic/component with embedded best-practice that semantic is the default (preserves design intent, adapts across modes, survives primitive re-mapping); (3) **Exclusions** — Q1c now lets the user name Collections/Groups to avoid on this screen, treated as hard prohibitions; (4) **Library Variable subscription** — Q1 readiness notice gains a fourth pre-flight item requiring library-published Variables to be enabled in the target file. Phase 1 closing summary gains three new conditional rows (Variable modes, Variable tier preference, Variable exclusions). Output template Figma MCP block gains three new metadata lines and three new instruction bullets (Tier preference, expanded Modes with mode-aware requirement, Exclusions). Phase 2 Figma MCP Flag, Phase 2 AC Flags, and output template AC block all extended with the new captures. "Figma MCP routes to Figma Design" principle extended with two best-practice defaults (semantic-tier preferred, mode-aware bindings). Gem Memory Consultation table gains Variable modes (Stable — verify), Variable tier preference (Stable), and Variable exclusions (Verify) rows. SKILL.md and Gem fully synced at v2.17.0. | ✅ Resolved |
