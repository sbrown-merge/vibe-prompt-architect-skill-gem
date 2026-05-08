# Vibe Prompt Architect — Sync Manifest

This file governs the relationship between `SKILL.md` (Claude Skill) and
`vibe-prompt-architect-gem.md` (Gemini Gem). Open it at the start of every editing
session. Use the Feature Touch Map to identify every location a change must affect.
Run the Pre-Commit Checklist before closing any session.

**Session protocol:** Always upload all three files — `SKILL.md`,
`vibe-prompt-architect-gem.md`, and `SYNC-MANIFEST.md` — when requesting changes in
Claude. The manifest must be in context for Claude to apply it.

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
| L10n / I18n Requirements | `### L10n / I18n Requirements` | `#### L10n / I18n Requirements` | `PARALLEL` — expansion table rows must match |
| Question Sequence | `### Question Sequence` (Q1–Q15, Q5a, Q8b) — Q7=Design System, Q8=Make Kit, Q8b=DESIGN.md, Q9=Constraints (conditional) | `#### Question Sequence` | `PARALLEL` — question numbers, wording, and mandatory flags must match |
| Authority File Gate | `### Authority File Gate` (between Q8b and Q9 in both files) | `#### Authority File Gate` | `PARALLEL` — status table and four preamble variants must match |
| Phase 1 closing summary | After Question Sequence | After Question Sequence | `PARALLEL` — summary field list must match; Gem adds *(recalled / new)* annotations |
| Phase 2 flag order | `## Phase 2: Clarify and Analyze` | `### Phase 2 — Clarify` | `PARALLEL` — flag order must be identical: Scope → Ambiguity → CTA → Raw Value Translation → Grid → Design System → Make Kit → DESIGN.md → Accessibility → L10n → AC → Platform |
| Scope Flag | `### Scope Flags` | `**Scope**` | `PARALLEL` |
| Ambiguity Flag | `### Ambiguity Flags` | `**Ambiguity**` | `PARALLEL` |
| CTA Flag | `### CTA Flag` | `**CTA Flag**` | `PARALLEL` |
| Raw Value Translation Flag | `### Raw Value Translation Flag` | `**Raw Value Translation**` | `PARALLEL` |
| Grid Flag | `### Grid Flag` | `**Grid Flag**` | `PARALLEL` |
| Design System Flags | `### Design System Flags` | `**Design System and Guidelines File**` | `PARALLEL` |
| Make Kit Flag | `### Make Kit Flag` | `**Make Kit**` | `PARALLEL` |
| DESIGN.md Flag | `### DESIGN.md Flag` | `**DESIGN.md**` | `PARALLEL` |
| Accessibility Flag | `### Accessibility Flag` | `**Accessibility Flag**` | `PARALLEL` |
| L10n Flag | `### L10n Flag` | `**L10n Flag**` | `PARALLEL` |
| Acceptance Criteria Flags | `### Acceptance Criteria Flags` | `**Acceptance Criteria**` | `PARALLEL` — mandatory AC item lists must match |
| Platform Flags | `### Platform Flags` | `**Platform**` | `PARALLEL` |
| TC-EBC Framework | `### TC-EBC Framework` | `## The TC-EBC Framework` | `EQUIVALENT` — SKILL.md has fuller guidance per section; Gem is more concise |
| Output template header | Inside ` ``` ` block | Inside ` ``` ` block | `PARALLEL` — all five metadata fields must match |
| Output template Elements | Inside ` ``` ` block | Inside ` ``` ` block | `PARALLEL` — inline hints must match |
| Output template Behavior | Inside ` ``` ` block | Inside ` ``` ` block | `PARALLEL` — focus ring line must be present in both |
| Output template Constraints | Inside ` ``` ` block | Inside ` ``` ` block | `PARALLEL` — all token rows (Color, Typography, Spacing, Radius, Accessibility, CTA hierarchy) must match |
| Output template L10n section | `## Localisation & I18n` inside ` ``` ` | `## Localisation & I18n` inside ` ``` ` | `PARALLEL` — all fields and sub-notes must match |
| Output template Design Guidelines | Inside ` ``` ` block | Inside ` ``` ` block | `PARALLEL` |
| Output template Make Kit | Inside ` ``` ` block | Inside ` ``` ` block | `PARALLEL` |
| Output template AC block | Inside ` ``` ` block — mandatory items pre-populated | Inside ` ``` ` block — mandatory items pre-populated | `PARALLEL` — mandatory items, L10n commented items, and project-specific slots must match |
| Post-generation tips | `## Post-Generation Guidance` | `## After You Generate` | `PARALLEL` — topics must match; Gem uses bold headers, SKILL.md uses bullet list |
| Principles | `## Key Principles (always in effect)` | `## Principles` | `PARALLEL` — every principle must appear in both files, in the same order |
| Version history | *(absent)* | `## Version History` | `FILE-SPECIFIC` (Gem only) |
| Human editors note | *(two-line comment in YAML header)* | Blockquote after version table | `EQUIVALENT` — both reference this manifest |

---

## Section 2 — Feature Touch Map

When adding or changing any feature, every checked location below must be updated in
**both files** unless marked `(SKILL.md only)` or `(Gem only)`.

### Adding or changing a question (Q-number)

- [ ] `### Question Sequence` / `#### Question Sequence` — question text and wording
- [ ] `### Cadence Rules` / `#### Cadence Rules` — if the question has mandatory/skip rules
- [ ] `### [Relevant Phase 1 section]` — if the question triggers a Phase 1 behaviour (e.g., grid guidance fires at Q9)
- [ ] `### [Relevant Phase 2 flag]` — the flag that audits this question's answer
- [ ] Phase 1 closing summary — if the question produces a summary field
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
- [ ] `### Question Sequence` — Q7, Q8, or Q8b as appropriate
- [ ] `### Make Kit Flag` or `### DESIGN.md Flag` — Phase 2 verify flag
- [ ] Output template — relevant conditional section
- [ ] Memory Consultation — relevant stable/fresh row (Gem only)
- [ ] Phase 1 closing summary — relevant summary field
- [ ] `**Guidelines files are the token source of truth**` principle or `**Make Kit is law**` principle
- [ ] `## Post-Generation Guidance` / `## After You Generate` — relevant tip
- [ ] Version history (Gem only)

### Adding or changing the Authority File Gate

- [ ] Phase 1 — Authority File Gate block (between Q8b and Q9) in both files — status table and four preamble variants
- [ ] Phase 1 — Raw Value Translation section — authority file gate block at top (both files)
- [ ] Phase 1 — Grid Conformance — proactive grid guidance Q reference (Q9, not Q8)
- [ ] Phase 1 — Question Sequence — Q9 conditional branch text for each status
- [ ] Phase 1 closing summary — authority file status field
- [ ] Phase 2 — Raw Value Translation Flag — authority file gate logic (three branches)
- [ ] Phase 2 — Design System Flags — authority file gate verification bullet
- [ ] Phase 2 — Make Kit Flag — Q reference (Q8 not Q9)
- [ ] Phase 2 — DESIGN.md Flag — Q reference (Q8b not Q9b)
- [ ] Phase 2 — Platform Flag — Q references (Q8, Q8b)
- [ ] Phase 3 TC-EBC Constraints — authority file clause at top of bullet list
- [ ] Output template — token rows updated with "defer to authority file" option
- [ ] Post-generation guidance — authority file tip
- [ ] Key Principles / Principles — "Authority file gates primitive intake" principle
- [ ] "Translate, don't discard" principle — updated to include authority file redirect
- [ ] Memory Consultation table Q-numbers (Gem only)
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

7. Did I bump the Gem version table with a changelog entry?
   [ ] Yes   [ ] No — do it now

8. Did I update this SYNC-MANIFEST.md if a new feature, section, or touch-map entry
   was introduced?
   [ ] Yes — no manifest changes needed   [ ] Yes — manifest updated   [ ] No — fix now

9. Are both files' token translation tables (Color, Typography, Spacing, Radius) showing
   dual CSS variable and DESIGN.md dot-path convention?
   [ ] Yes   [ ] No — fix now

10. Is the Phase 2 flag sequence in both files identical?
    [ ] Yes   [ ] No — fix now
```

---

## Section 4 — Intentional Differences

The following differences between the files are correct and must not be "fixed."

| What differs | SKILL.md | Gem | Why |
|---|---|---|---|
| Memory Consultation | Absent | Present | Claude handles cross-session memory natively; Gemini requires explicit instructions |
| Version history table | Absent | Present | Gem is a versioned document edited by humans; SKILL.md is managed at file-system level |
| Human editors note | Two-line YAML comment | Blockquote after version table | Different appropriate locations in each file format |
| Role / persona description | YAML front matter description | `## Role` section with explicit persona statement | Claude Skill format vs Gem instruction format |
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
