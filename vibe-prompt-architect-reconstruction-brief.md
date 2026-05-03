# Vibe Prompt Architect — Reconstruction Brief

This document is the authoritative briefing for recreating the **Vibe Prompt Architect**
file set from scratch. It contains everything needed to understand the project's goals,
design decisions, file structure, and maintenance protocol.

## How to use this document

**To recreate the files in a new Claude session:**

1. Start a new conversation in Claude
2. Upload this file alongside any surviving files from the set
3. Say: *"I need to recreate the Vibe Prompt Architect file set. Use this brief and any
   files I've attached to produce clean, current versions of all four files:
   SKILL.md, vibe-prompt-architect-gem.md, SYNC-MANIFEST.md, and README.md."*
4. Claude will use this brief as the design authority and any uploaded files as the
   content source, reconciling the two where they differ

**If you have all four current files and just want to share the project:**

Upload all four files directly. This reconstruction brief is for the case where files
have been lost, corrupted, or need to be rebuilt from a different starting point.

**If you are sharing with a new collaborator:**

Give them this file plus all four current files. The brief explains the *why*; the files
are the *what*. Both are needed for informed maintenance.

---

## Project overview

**Vibe Prompt Architect** is a structured requirements-gathering and prompt-generation
workflow for AI-powered UI prototyping tools (Figma Make, Google Stitch, Lovable, Cursor,
Claude Code, v0, and others). It takes a rough UI idea and produces a copy-ready Markdown
prompt through a three-phase process: Gather → Clarify → Generate.

The core problem it solves: designers and developers prompting vibe-coding tools get
inconsistent results because their prompts are underspecified. Vibe Prompt Architect
front-loads the specification work through a structured intake conversation, applies
automated quality checks, and generates a prompt that is complete enough to produce
a good result on the first run.

The workflow is implemented in two parallel AI instruction files:
- **SKILL.md** — a Claude Skill, installed in a Claude Skills directory
- **vibe-prompt-architect-gem.md** — a Gemini Gem, pasted into a Gemini Gem instructions field

Both implement the same workflow. They are maintained in sync using a third file:
- **SYNC-MANIFEST.md** — a maintenance document with a section map, feature touch map,
  pre-commit checklist, and intentional differences list

A fourth file provides user-facing documentation:
- **README.md** — describes the project for potential users evaluating fit

---

## Design principles

These principles govern every decision in the workflow. When recreating or extending the
files, every change should be checked against this list.

1. **Platform neutrality.** The workflow never names or implies a preferred tool. Q1 is
   an open question; the user names their platform.

2. **A11y and L10n are always required inputs.** Q14 (accessibility level) and Q15
   (localisation scope) are mandatory on first encounter and cannot be skipped. Every
   product has both postures, even if the answers are "AA" and "English only."

3. **Tone: professional and direct.** Senior product designer register. No filler, no
   flattery, no preambles. Warm means collegial, not effusive.

4. **Phase 2 is a dependency chain, not a checklist.** The audit flags run in order:
   Scope → Ambiguity → CTA → Raw Value Translation → Grid → Design System → Make Kit →
   DESIGN.md → Accessibility → L10n → Acceptance Criteria → Platform. Reordering breaks
   downstream dependencies.

5. **Localisation is a layout constraint, not a content task.** L10n affects string
   expansion headroom, RTL spatial layout, format tokens, font fallback stacks, and
   pluralisation — all of which must appear in the generated prompt.

6. **One primary CTA per screen, strict hierarchy below it.** No equal-weight competing
   actions. Helper text used to explain a CTA is a design smell.

7. **WCAG 2.2 AA is the unconditional baseline.** Full numeric requirements in Constraints,
   not just the level name. AAA is opt-in via Q14.

8. **Guidelines files are the token source of truth.** If a DESIGN.md, .cursorrules,
   CLAUDE.md, or equivalent file exists, the prompt instructs the AI to read it before
   generating. DESIGN.md uses dot-path syntax: `{colors.primary}`, `{spacing.lg}`.

9. **8px grid is the default.** All spacing and radius on the 8px grid (4px microgrid
   for fine detail). Off-grid values are rounded, corrected before tokenisation, and the
   user is notified.

10. **Token names beat raw values.** CSS variable or DESIGN.md dot-path convention; never
    raw hex, px, or pt values in the prompt.

11. **Translate, don't discard.** Raw values are converted to the closest semantic token
    and shown to the user. Never silently dropped or passed through.

12. **Elements list is exhaustive.** Anything not listed may be omitted or replaced.

13. **One screen at a time.** Multi-screen flows use sequential prompts.

14. **Design system = ingredient list.** Named systems constrain the AI toward the brand.

15. **Make Kit is law.** For Figma Make with an attached Make Kit, it is the sole source
    of truth. No overrides.

16. **AC makes iteration precise.** Binary pass/fail criteria replace vague dissatisfaction.

17. **Prototype type shapes AC.** Functional prototype: interaction checks. Design mockup:
    visual inspection. Same format, different subject matter.

18. **Phase 2 is a dependency chain.** Token corrections precede design system checks;
    design system checks precede authority file checks; all checks precede AC generation.

19. **Behaviors need states.** Default, focused, and active at minimum. Focus state is a
    mandatory AA requirement.

20. **Implicit context is a liability.** Make everything explicit in the prompt.

21. **Prompt drift is real.** In metered tools, refine before running, not after.

22. **Platform matters.** iOS, Android, and Web have different conventions. Always confirm.

---

## The TC-EBC prompt format

Every generated prompt uses the TC-EBC structure. This is the output format — the thing
the user copies into their vibe-coding tool.

**T — Task:** One sentence. Action verb + UI type + product context. No style adjectives.
Example: `Create a mobile onboarding screen for a personal finance tracking app.`

**C — Context:** 2–4 sentences. User type, prior step, next step, emotional context if
design-relevant.

**E — Elements:** Exhaustive component list. CTA tier labels (`[Primary]`, `[Secondary]`,
`[Tertiary]`, `[Destructive]`). Alt text for images and icon controls. Pluralisation
flags for count-dependent strings. Do not describe appearance — that belongs in Constraints.

**B — Behavior:** All interactive states and logic. Mandatory focus ring entry. Conditional
logic, transitions, scroll behavior.

**C — Constraints:** All rules. Design system reference, 8px grid with token names (CSS
variable and DESIGN.md dot-path), color/typography/spacing/radius token rows, full WCAG
2.2 AA/AAA requirements with numeric values, CTA hierarchy rules, explicit prohibitions.

**Header metadata:** Five fields above the `---` separator: Target platform, Scope,
Prototype type, Accessibility level, L10n / I18n status.

**Conditional sections** (included only when applicable):
- `## Localisation & I18n` — when L10n is required
- `## Design Guidelines` — when a DESIGN.md or guidelines file exists
- `## Make Kit` — when targeting Figma Make with an attached Make Kit

**Acceptance Criteria:** Pre-populated mandatory items (4 CTA hierarchy + 5 WCAG AA),
conditional L10n items (6, commented out by default), project-specific slots.

---

## File structure and relationships

### SKILL.md

A Claude Skill instruction file. Installed in the user's Claude Skills directory. Claude
triggers it automatically when the user asks to prompt a vibe-coding tool.

**Key structural sections (in order):**
- YAML front matter with name, sync comments, and description
- Workflow Overview (three phases)
- Phase 1: Gather Inputs
  - Cadence Rules (with mandatory exception for Q14/Q15)
  - Raw Value Translation (color, typography, spacing, radius tables — all dual convention)
  - Grid Conformance (8px base, 4px microgrid, rounding logic)
  - Guidelines File Support (reference section — intake at Q8/Q9b)
  - Accessibility Requirements (standing AA rules + conditional AAA)
  - CTA Hierarchy (standing rules, tier definitions)
  - L10n / I18n Requirements (expansion table, directionality, format tokens, font stacks)
  - Question Sequence (Q1–Q15, plus Q5a and Q9b)
  - Phase 1 closing summary (9-field structured format + correction handling)
- Phase 2: Clarify and Analyze (12 flags in dependency order)
- Phase 3: Generate the TC-EBC Prompt
  - TC-EBC Framework description
  - Output Format (complete template in fenced code block)
- Post-Generation Guidance (8 topics)
- Key Principles (21 principles)

**Does not contain:** Version history, Memory Consultation, human editors note, Role
persona statement, Gem-specific session behaviour.

### vibe-prompt-architect-gem.md

A Gemini Gem instruction file. The user pastes the full contents into a new Gem's
instructions field.

**Structural additions over SKILL.md:**
- Version History table (semantic versioning, current version 2.10.0)
- Human editors note (references this manifest, session protocol)
- Role / persona statement (explicit senior product design collaborator framing)
- Memory Consultation section (Gemini-only — scans prior context for stable inputs,
  presents recalled/new summary, validates before use)
- Recalled/new annotations in Phase 1 closing summary
- Gem-specific session behaviour notes (stitch.withgoogle.com, npx lint CLI)

**Structurally identical to SKILL.md:** All Phase 1 behaviour sections, all 12 Phase 2
flags in the same order, TC-EBC framework, output template, post-generation tips,
21 principles in the same order.

**Version numbering convention:**
- Major (X.0.0) — breaking changes to output format or workflow structure
- Minor (X.Y.0) — new capabilities
- Patch (X.Y.Z) — fixes and clarifications

### SYNC-MANIFEST.md

A maintenance document. Opened at the start of every editing session.

**Five sections:**
1. **File Anatomy Map** — every section in both files annotated as PARALLEL, EQUIVALENT,
   FILE-SPECIFIC, DERIVED, or TEMPLATE
2. **Feature Touch Map** — for each feature category, a checklist of every location that
   must be updated when that feature changes
3. **Pre-Commit Checklist** — 10 yes/no questions to answer before closing any session
4. **Intentional Differences** — 11 documented differences between the files that must
   not be "fixed"
5. **Sync Health Log** — records known drift or deferred fixes

### README.md

User-facing documentation for evaluating the project.

**Sections:** What it does, Who it's for, Supported platforms, What the generated prompt
contains, Built-in quality requirements (A11y, CTA hierarchy, 8px grid, token-first),
Localisation support, Installation and use (Skill + Gem), The TC-EBC framework, Design
token support, Files in this package, Maintenance, Known limitations.

---

## Question sequence (complete, current)

The intake questions in order. Q14 and Q15 are mandatory — cannot be skipped on first
encounter. Q5a and Q9b are conditional follow-ups.

| Q | Label | Notes |
|---|---|---|
| Q1 | Platform | Prompt-drift note fires immediately after Q1 if metered platform |
| Q2 | UI type | |
| Q3 | Product and user | |
| Q4 | User moment | |
| Q5 | Required elements | List everything — don't evaluate priority |
| Q5a | Primary action | Dedicated follow-up after Q5; designates [Primary] CTA |
| Q6 | Behaviors | |
| Q7 | Constraints | Grid guidance fires here if no grid specified |
| Q8 | Design system / tokens / guidelines file | Four options: named system, DESIGN.md, platform guidelines file, none |
| Q9 | Make Kit | Figma Make only |
| Q9b | DESIGN.md | Google Stitch only |
| Q10 | Reference images | |
| Q11 | Scope | |
| Q12 | Prototype type | |
| Q13 | Acceptance criteria | |
| Q14 | Accessibility level | **Mandatory** — AA default, AAA opt-in |
| Q15 | Localisation (L10n / I18n) | **Mandatory** — all products have a posture |

**Memory / recall behaviour:**
- Stable inputs (Q1, Q8, Q9, Q9b, Q12, Q14, Q15, grid) are recalled across sessions
  and not re-asked
- Always-fresh inputs (Q4, Q5, Q5a, Q6, Q7, Q10, Q11, Q13) are gathered fresh each screen
- Q3 (product/user) and Q2 (UI type) are ⚠️ Verify — recalled but confirmed before use

---

## Phase 2 flag sequence (current, ordered)

The 12 flags run in this exact order — changing the order breaks downstream dependencies.

1. **Scope** — catch too-broad or too-narrow requests first
2. **Ambiguity** — resolve unclear inputs before content audits
3. **CTA Flag** — verify Q5a designation; audit labels, hierarchy, compensatory affordances
4. **Raw Value Translation** — clean up tokens before design system check
5. **Grid Flag** — clean up spacing before design system check
6. **Design System** — confirm token convention and authority source
7. **Make Kit** — Figma Make authority file (verify Q9 was captured)
8. **DESIGN.md** — Stitch authority file (verify Q9b was captured)
9. **Accessibility** — contrast risks, target sizes, focus states, alt text, reflow
10. **L10n** — fixed-width containers, hardcoded formats, RTL, font stacks, pluralisation
11. **Acceptance Criteria** — generate/verify AC after all flags have run
12. **Platform** — final platform-specific checks

---

## Token naming conventions

All four value categories use dual convention throughout — both forms shown side by side.

| Category | CSS variable | DESIGN.md dot-path | Notes |
|---|---|---|---|
| Color | `--color-primary` | `colors.primary` | Map by semantic role, not visual appearance |
| Typography | `--font-size-base` | `typography.body` | Map by scale position, not absolute px value |
| Spacing | `--space-xl` | `spacing.xl` | Always grid-correct before tokenising |
| Radius | `--radius-md` | `rounded.md` | DESIGN.md uses `rounded`, not `radius` |

**Grid correction runs before tokenisation for spacing and radius.** The correct sequence
is: detect raw value → check grid conformance → round to nearest 4px if needed → tokenise
the corrected value → notify user of both the correction and the token name.

**Tailwind / Material 3 conventions** apply when those design systems are confirmed:
- Tailwind: `text-base`, `bg-primary`, `rounded-md`, `p-6`
- Material 3: `md-sys-color-primary`, `md-sys-typescale-body-large`

---

## Mandatory output template elements

Every generated prompt must include these elements regardless of what the user specified.
These are not optional even if the user did not mention them.

**In Elements block:**
- CTA tier label on every CTA (`[Primary]`, `[Secondary]`, `[Tertiary]`, `[Destructive]`)
- Exactly one `[Primary]` CTA per screen
- Alt text for all images and icon controls
- Pluralisation flag on count-dependent strings

**In Behavior block:**
- Mandatory focus ring line: `[All interactive elements show a visible focus ring on
  keyboard/switch access focus]`

**In Constraints block:**
- 8px grid with token name examples
- Color tokens row (both conventions)
- Typography tokens row (both conventions)
- Spacing tokens row (both conventions)
- Radius tokens row (both conventions)
- Full WCAG 2.2 AA block with numeric values (not just "WCAG AA")
- CTA hierarchy block with tier assignments

**In Acceptance Criteria block (pre-populated, not placeholder text):**
- 4 CTA hierarchy items (always present)
- 5 WCAG 2.2 AA items (always present)
- 6 L10n items (commented out, uncommented when L10n is required)
- Project-specific slots at the end

---

## Platform-specific authority files

| Platform | Authority file | Question | Phase 2 flag | Token convention |
|---|---|---|---|---|
| Figma Make | Make Kit (project attachment) | Q9 | Make Kit Flag | CSS variable |
| Google Stitch | DESIGN.md (project root) | Q9b | DESIGN.md Flag | dot-path with `{}` |
| Cursor | `.cursorrules` | Q8 | Design System flag | CSS variable |
| Claude Code | `CLAUDE.md` | Q8 | Design System flag | CSS variable |
| Other | Custom guidelines file | Q8 | Design System flag | Follow file's own convention |

When an authority file is present: the generated prompt instructs the AI to read it before
generating. Token values come from the file. The prompt references token names only — no
raw values. Authority file values cannot be overridden by prompt-level specifications.

---

## Intentional differences between SKILL.md and Gem

Do not "fix" these differences — they are correct.

| Feature | SKILL.md | Gem |
|---|---|---|
| Memory Consultation | Absent | Present (Gemini requires explicit instructions) |
| Version history | Absent | Present (Gem is versioned for human editors) |
| Human editors note | Two-line YAML comment | Blockquote with full session protocol |
| Role / persona | YAML description | `## Role` section |
| Recalled/new annotations | Absent | Present in Phase 1 summary |
| TC-EBC detail level | Fuller (sub-bullets, examples) | Concise (one-liners) |
| Phase 1 heading depth | `###` | `####` |
| Phase 2 flag headings | `### Flag Name` | `**Flag Name**` |
| Principles heading | `## Key Principles (always in effect)` | `## Principles` |
| Post-generation format | Bullet list | Bold-header paragraphs |
| Gem-specific tips | Absent | stitch.withgoogle.com, npx lint CLI |

---

## Maintenance protocol

**Session protocol (follow for every change):**
1. Upload all three instruction files (`SKILL.md`, `vibe-prompt-architect-gem.md`,
   `SYNC-MANIFEST.md`) to Claude
2. Open `SYNC-MANIFEST.md` and identify the Feature Touch Map entries affected by the
   change
3. Describe the change to Claude — it will apply it consistently to both files
4. Run the Pre-Commit Checklist in `SYNC-MANIFEST.md` before closing the session
5. Bump the Gem version number and add a changelog entry
6. Update `SYNC-MANIFEST.md` if the change introduces a new feature, section, or
   touch-map entry

**Feature touch map categories (see SYNC-MANIFEST.md for full checklists):**
- Adding/changing a question
- Adding/changing a Phase 2 flag
- Adding/changing a token translation table
- Adding/changing output template content
- Adding/changing an accessibility requirement
- Adding/changing a CTA hierarchy rule
- Adding/changing an L10n / I18n requirement
- Adding/changing a guidelines file type
- Adding/changing a principle
- Tone or wording changes

---

## Current file versions

| File | Version | Last updated |
|---|---|---|
| SKILL.md | No version (managed at file-system level) | 2026-05-03 |
| vibe-prompt-architect-gem.md | 2.10.0 | 2026-05-03 |
| SYNC-MANIFEST.md | No version (living document) | 2026-05-03 |
| README.md | No version | 2026-05-03 |

**Gem version history summary:**
- 1.0–1.6: TC-EBC framework, Figma Make / Make Kit, cadence rules, AC, raw value
  translation, token conventions, grid conformance, version control
- 1.7: Google Stitch, DESIGN.md, guidelines file integration
- 1.8: WCAG 2.2 AA baseline, AAA opt-in, accessibility requirements
- 1.9: CTA hierarchy rules, tier labelling, CTA Flag
- 2.0: L10n / I18n requirements, Q15, L10n Flag, localisation output section
- 2.1: Memory Consultation (Gemini-only)
- 2.2: Platform neutrality, mandatory Q14/Q15, professional tone
- 2.3: Q5/Q5a split (elements + primary action as separate questions)
- 2.4: Q9b (DESIGN.md — Stitch only), Make Kit/DESIGN.md Flag verify-not-re-ask
- 2.5: Memory Consultation table updated (Q5a, Q9b)
- 2.6: Phase 2 flag reorder to dependency sequence
- 2.7: Principles sync (dependency chain principle, three missing SKILL.md principles)
- 2.8: Phase 1 summary format, correction handling, prompt-drift timing
- 2.9: Guidelines File Support slimmed, custom guidelines catch-all
- 2.10: L10n output template sync, spacing tokens row, expansion table alignment

---

## Reconstruction instructions for Claude

If you are Claude reading this document to recreate the file set, follow these steps:

1. **Establish the design authority.** This brief is the design authority. Any uploaded
   files are the content authority. Where they agree, use the content. Where they conflict,
   flag the conflict and ask which is correct before proceeding.

2. **Recreate all four files.** Produce `SKILL.md`, `vibe-prompt-architect-gem.md`,
   `SYNC-MANIFEST.md`, and `README.md` as separate file outputs.

3. **Apply the section map.** Use the File Structure section of this brief and the
   Anatomy Map in SYNC-MANIFEST.md to ensure every section is present in the right file.

4. **Apply the intentional differences.** Do not make the Gem and SKILL.md identical.
   The differences listed above are correct.

5. **Apply all 22 design principles.** Every section of every file should be consistent
   with the principles listed in this brief.

6. **Verify the Phase 2 flag order.** Both files must have the 12 flags in the exact
   dependency order listed in this brief.

7. **Verify the token tables.** All four token categories (Color, Typography, Spacing,
   Radius) must show dual CSS variable and DESIGN.md dot-path convention in SKILL.md.
   The Gem's tables are equivalent.

8. **Verify the output template.** The template must include all five header metadata
   fields, the mandatory Elements hints, the mandatory Behavior focus ring line, all
   token rows in Constraints, the full WCAG block with numeric values, the CTA hierarchy
   block, and the pre-populated AC mandatory items.

9. **Set the Gem version.** The Gem version should be set to the latest version in the
   version history summary, unless a specific version is requested.

10. **Update the SYNC-MANIFEST.md sync health log.** Add an entry recording the date
    and reason for reconstruction.
