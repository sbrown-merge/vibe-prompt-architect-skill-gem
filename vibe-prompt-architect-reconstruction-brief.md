# Vibe Prompt Architect — Reconstruction Brief

This document is the authoritative briefing for recreating the **Vibe Prompt Architect**
file set from scratch. It contains everything needed to understand the project's goals,
design decisions, file structure, and maintenance protocol.

## How to use this document

**To recreate the files in a new Claude session:**

1. Start a new conversation in Claude
2. Upload this file alongside any surviving files from the set
3. Say: *"I need to recreate the Vibe Prompt Architect file set. Use this brief and any
   files I've attached to produce clean, current versions of all four content files:
   SKILL.md, vibe-prompt-architect-gem.md, SYNC-MANIFEST.md, and README.md."*
4. Claude will use this brief as the design authority and any uploaded files as the
   content source, reconciling the two where they differ

**If you have all four content files and just want to share the project:**

Upload them directly. This reconstruction brief is for the case where files have been
lost, corrupted, or need to be rebuilt from a different starting point.

**If you are sharing with a new collaborator:**

Give them this brief plus all four content files (five files total). The brief explains
the *why*; the content files are the *what*. Both are needed for informed maintenance.

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
files, every change should be checked against this list. Both SKILL.md and the Gem must
carry all 27 principles in the same order.

1. **Output is raw Markdown, in-line in the chat.** The generated prompt is always delivered
   in the chat as raw Markdown wrapped in a fenced code block — never as a file attachment,
   downloadable artifact, canvas/document, Google Doc, or rendered preview. The user must
   see literal Markdown syntax so they can copy it verbatim into the target tool.

2. **Generation mode is the user's choice; final delivery is fixed.** Phase 3 offers a
   full-prompt-now or guided section-by-section review (preference recalled across
   sessions). Guided review shows each section as rendered text for approve-or-revise,
   then assembles and delivers the complete prompt as one raw Markdown fenced code block.
   The mode changes how the prompt is reviewed, never how it is finally delivered.

3. **Platform neutrality.** The workflow never names or implies a preferred tool. Q1 is
   an open question; the user names their platform.

4. **A11y and L10n are always required inputs.** Q14 (accessibility level) and Q15
   (localisation scope) are mandatory on first encounter and cannot be skipped. Every
   product has both postures, even if the answers are "AA" and "English only."

5. **Tone: professional and direct.** Senior product designer register. No filler, no
   flattery, no preambles. Warm means collegial, not effusive.

6. **Phase 2 is a dependency chain, not a checklist.** The audit flags run in order:
   Scope → Ambiguity → CTA → Casing → Raw Value Translation → Grid → Design System →
   Make Kit → DESIGN.md → Figma MCP → Accessibility → L10n → Acceptance Criteria →
   Platform. Token corrections precede design system verification; design system
   verification precedes authority file checks (Make Kit, DESIGN.md, Figma MCP); authority
   file checks precede accessibility and L10n audits; all audits precede AC generation.
   Reordering breaks the chain.

7. **Localisation is a layout constraint, not a content task.** L10n affects string
   expansion headroom, RTL spatial layout, format tokens, font fallback stacks, and
   pluralisation — all of which must appear in the generated prompt.

8. **One primary CTA per screen, strict hierarchy below it.** No equal-weight competing
   actions. Helper text used to explain a CTA is a design smell.

9. **WCAG 2.2 AA is the unconditional baseline.** Full numeric requirements in Constraints,
   not just the level name. AAA is opt-in via Q14.

10. **Guidelines files are the token source of truth.** If a DESIGN.md, .cursorrules,
    CLAUDE.md, or equivalent file exists, the prompt instructs the AI to read it before
    generating. DESIGN.md uses dot-path syntax: `{colors.primary}`, `{spacing.lg}`.

11. **Authority file gates primitive intake.** For Figma Make and Google Stitch, the
    platform choice at Q1 automatically establishes Make Kit and DESIGN.md authority
    respectively. For Claude Code + Figma MCP, the Component Library (Q1b) and Variables
    (Q1c) follow-ups establish a granular dual authority — the library covers components,
    Variables cover styling, and they fire independently. For other platforms, a named
    design system confirmed at Q7 sets authority. When authority is established, Q9
    (Constraints) suppresses raw styling intake for the categories the authority covers.

12. **Figma MCP routes to Figma Design.** When the platform is Claude Code + Figma MCP,
    the generated prompt must include the `figma-use` skill prerequisite, an explicit
    `use_figma` tool-call instruction, the target Figma Design file/page URL (Q1a), and
    any Component Library URL (Q1b) and Variable Collection(s)/Group(s) (Q1c) the user
    provided. Variable references use literal Figma form `{group/variable-name}` with
    Collection captured as separate metadata — not CSS variable or DESIGN.md dot-path
    syntax. Two best-practice defaults are enforced for Variable bindings: (1) **prefer
    semantic-tier Variables** (e.g., `color/text/primary`) over primitive-tier Variables
    (e.g., `color/gray/900`) because semantic Variables preserve design intent and adapt
    across modes; (2) **bindings are mode-aware** when Collections include modes (Light/
    Dark, Density, Brand) — reference the Collection so Figma resolves the active mode
    at render time, never hardcode a specific mode's value. Both defaults apply unless
    the user explicitly overrides them at Q1c.

13. **8px grid is the default.** All spacing and radius on the 8px grid (4px microgrid
    for fine detail). Off-grid values are rounded, corrected before tokenisation, and
    the user is notified.

14. **Sentence case by default.** All UI text uses sentence case unless the user sets
    another convention at Q16 (recalled across sessions). ALL CAPS is reserved for short
    overline/eyebrow labels and applied via `text-transform`, never hardcoded — hardcoded
    capitals break accessible names and localisation source strings. all-lowercase and
    Title Case are deliberate brand choices, not defaults.

15. **Token names beat raw values.** CSS variable, DESIGN.md dot-path, or Figma Variable
    convention; never raw hex, px, or pt values in the prompt.

16. **Motion uses tokens and respects reduced-motion.** When behaviors involve transitions
    or animation, reference motion tokens (`--duration-fast/base/slow`,
    `--easing-standard/decelerate/accelerate`) rather than raw durations or easings, and
    instruct the AI to honour `prefers-reduced-motion`. Screens with no non-essential
    motion carry no motion guidance — never invent animation.

17. **Translate, don't discard.** Raw values are converted to the closest semantic token
    and shown to the user — or redirected to the active authority file (Make Kit reference,
    DESIGN.md dot-path, or Figma Variable `{group/variable-name}` in the named Collection).
    Never silently dropped or passed through.

18. **Elements list is exhaustive.** Anything not listed may be omitted or replaced.

19. **One screen at a time.** Multi-screen flows use sequential prompts.

20. **Design system = ingredient list.** Named systems constrain the AI toward the brand.

21. **Make Kit is law.** For Figma Make, Make Kit authority is established by the platform
    choice at Q1. It is the sole source of truth for components, styling, and usage rules.
    No overrides. If the Make Kit isn't yet configured, the user must set it up before
    running.

22. **AC makes iteration precise.** Binary pass/fail criteria replace vague dissatisfaction.

23. **Prototype type shapes AC.** Functional prototype: interaction checks. Design mockup:
    visual inspection. Same format, different subject matter.

24. **Behaviors need states.** Default, focused, and active at minimum. Focus state is a
    mandatory AA requirement.

25. **Implicit context is a liability.** Make everything explicit in the prompt.

26. **Prompt drift is real.** In metered tools, refine before running, not after.

27. **Platform matters.** iOS, Android, and Web have different conventions. Always confirm.

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
variable, DESIGN.md dot-path, and Figma Variable convention shown side-by-side),
color/typography/spacing/radius token rows, full WCAG 2.2 AA/AAA requirements with
numeric values, CTA hierarchy rules, explicit prohibitions. Each token row supports a
"defer to authority file (Make Kit / DESIGN.md / Figma Variables)" option when an
authority is active.

**Header metadata:** Five fields above the `---` separator: Target platform, Scope,
Prototype type, Accessibility level, L10n / I18n status.

**Conditional sections** (included only when applicable):
- `## Localisation & I18n` — when L10n is required
- `## Design Guidelines` — when a DESIGN.md or guidelines file exists
- `## Make Kit (Figma Make only)` — when targeting Figma Make with an attached Make Kit
- `## Figma MCP (Claude Code + Figma MCP only)` — when targeting Claude Code + Figma MCP;
  contains target file URL (Q1a), Component Library URL (Q1b), Variables (Q1c), readiness
  warning, and an explicit `use_figma` instruction block covering components, variables,
  modes, auto-layout, and naming

**Acceptance Criteria:** Pre-populated mandatory items (4 CTA hierarchy + 5 WCAG AA),
conditional L10n items (6, commented out by default), Claude Code + Figma MCP conditional
items (3 baseline + 2 library when Q1b + 3 Variables when Q1c, all commented out by
default), project-specific slots.

---

## File structure and relationships

### SKILL.md

A Claude Skill instruction file. Installed in the user's Claude Skills directory. Claude
triggers it automatically when the user asks to prompt a vibe-coding tool.

**Key structural sections (in order):**
- YAML front matter with name, version comment (`# Version: X.Y.Z (date) — synced with
  vibe-prompt-architect-gem.md vX.Y.Z`), sync comments, and description
- H1 title followed by an italic version byline (`*Version X.Y.Z · YYYY-MM-DD · Synced
  with vibe-prompt-architect-gem.md vX.Y.Z*`) for visible parity with the Gem
- Output Delivery Rule (raw Markdown delivered in-line in the chat, wrapped in a fenced
  code block — never as a file/canvas/document/Drive artifact/rendered preview)
- Workflow Overview (three phases)
- Phase 1: Gather Inputs
  - Cadence Rules (with mandatory exception for Q14/Q15)
  - Raw Value Translation (color, typography, spacing, radius tables — dual CSS-variable
    + DESIGN.md dot-path convention, plus authority-file gate at the top covering Make
    Kit, DESIGN.md, Figma Variables, Figma Component Library, named system, and none)
  - Grid Conformance (8px base, 4px microgrid, rounding logic)
  - Guidelines File Support (reference section — intake owned by Q7)
  - Accessibility Requirements (standing AA rules + conditional AAA)
  - CTA Hierarchy (standing rules, tier definitions)
  - L10n / I18n Requirements (expansion table, directionality, format tokens, font stacks)
  - Question Sequence (Q1 with conditional Q1a/Q1b/Q1c follow-ups for Claude Code + Figma
    MCP; Q2–Q15 with Q5a as a conditional follow-up after Q5; no Q8)
  - Authority File Gate (block between Q7 and Q9 — 6-row status table + 7 preamble
    variants; evaluates Q1 platform, Q1b/Q1c Figma library/Variables, and Q7 design system)
  - Phase 1 closing summary (12-field structured format + correction handling)
- Phase 2: Clarify and Analyze (13 flags in dependency order — adds Figma MCP Flag between
  DESIGN.md Flag and Accessibility Flag)
- Phase 3: Generate the TC-EBC Prompt
  - TC-EBC Framework description
  - Output Format (complete template in fenced code block with conditional sections for
    L10n, Design Guidelines, Make Kit, and Figma MCP)
- Post-Generation Guidance (~9 topics — includes a Figma MCP tip)
- Key Principles (24 principles)

**Does not contain:** Version history table (the byline + YAML comment surface the current
version instead), Memory Consultation, human editors note (longer form), Role persona
statement, Gem-specific session behaviour.

### vibe-prompt-architect-gem.md

A Gemini Gem instruction file. The user pastes the full contents into a new Gem's
instructions field.

**Structural additions over SKILL.md:**
- Version History table (semantic versioning, current version 2.18.0)
- Human editors note (references this manifest, session protocol)
- Role / persona statement (explicit senior product design collaborator framing)
- Memory Consultation section (Gemini-only — scans prior context for stable inputs,
  presents recalled/new summary, validates before use; includes rows for Figma Component
  Library and Figma Variables — both stable across sessions — and Figma target file/page
  marked "Always fresh" since it's screen-specific)
- Recalled/new annotations in Phase 1 closing summary
- Gem-specific session behaviour notes (stitch.withgoogle.com, npx lint CLI)

**Structurally identical to SKILL.md:** All Phase 1 behaviour sections, all 13 Phase 2
flags in the same order, TC-EBC framework, output template (including the Figma MCP
conditional block and Constraints rows that show Figma Variable convention alongside CSS
variable and DESIGN.md dot-path), post-generation tips, 24 principles in the same order.

**Version numbering convention:**
- Major (X.0.0) — breaking changes to output format or workflow structure
- Minor (X.Y.0) — new capabilities
- Patch (X.Y.Z) — fixes and clarifications

### SYNC-MANIFEST.md

A maintenance document. Opened at the start of every editing session.

**Five sections:**
1. **File Anatomy Map** — every section in both files annotated as PARALLEL, EQUIVALENT,
   FILE-SPECIFIC, DERIVED, or TEMPLATE. Includes the SKILL.md version marker row (YAML
   comment + italic byline under H1) as `EQUIVALENT` to the Gem's "Current version" line.
2. **Feature Touch Map** — for each feature category, a checklist of every location that
   must be updated when that feature changes. Categories include: questions, Phase 2 flags,
   token tables, output template, accessibility, CTA hierarchy, L10n, guidelines file
   types, principles, tone changes, Authority File Gate, Claude Code + Figma MCP scenario,
   and version bumps.
3. **Pre-Commit Checklist** — 10 yes/no questions to answer before closing any session.
   Item 7 requires three-location version parity: Gem table row, SKILL.md YAML comment,
   and SKILL.md byline must all match.
4. **Intentional Differences** — documented differences between the files that must not
   be "fixed."
5. **Sync Health Log** — records known drift or deferred fixes; updated with a dated entry
   for each version release.

### README.md

User-facing documentation for evaluating the project.

**Sections:** What it does, Who it's for, Supported platforms, What the generated prompt
contains, Built-in quality requirements (A11y, CTA hierarchy, 8px grid, token-first),
Localisation support, Installation and use (Skill + Gem), The TC-EBC framework, Design
token support, Files in this package, Maintenance, Known limitations.

---

## Question sequence (complete, current)

The intake questions in order. Q14 and Q15 are mandatory — cannot be skipped on first
encounter. Q5a is a conditional follow-up after Q5. Q1a/Q1b/Q1c are conditional follow-ups
after Q1 = Claude Code + Figma MCP. There is no Q8 — it was removed in v2.13.0 when
Make Kit and DESIGN.md authority became platform-implied by Q1.

| Q | Label | Notes |
|---|---|---|
| Q1 | Platform | Prompt-drift note fires immediately after Q1 if metered platform. Readiness notice fires for Figma Make, Google Stitch, or Claude Code + Figma MCP |
| Q1a | Figma target file/page URL | Claude Code + Figma MCP only. Required. Page-level URL (`?node-id=...`) preferred |
| Q1b | Component Library URL | Claude Code + Figma MCP only. Optional. Prompt instructs AI to inspect library for embedded Variable Collections |
| Q1c | Variables (Collection(s) / Group(s) + modes + tier preference + exclusions) | Claude Code + Figma MCP only. Structured four-part ask in a single turn: (1) Collection(s)/Group(s) and location; (2) optional exclusions; (3) modes per Collection + default mode for this screen — bindings must be mode-aware for theme-switchable designs; (4) optional tier preference, default semantic — semantic-tier Variables preserve intent and adapt across modes |
| Q2 | UI type | |
| Q3 | Product and user | |
| Q4 | User moment | |
| Q5 | Required elements | List everything — don't evaluate priority |
| Q5a | Primary action | Dedicated follow-up after Q5; designates [Primary] CTA |
| Q6 | Behaviors | |
| Q7 | Design system / tokens / guidelines file | Four options: named system, DESIGN.md, platform guidelines file, none. For Claude Code + Figma MCP, scope narrows to gap-fill based on Q1b/Q1c answers |
| — | *(Authority File Gate evaluates Q1, Q1b/Q1c, and Q7 to determine the gate state and Q9 scope)* | |
| Q9 | Constraints | Conditional on authority file status. Grid guidance fires here if no grid specified |
| Q10 | Reference images | |
| Q11 | Scope | |
| Q12 | Prototype type | |
| Q13 | Acceptance criteria | |
| Q14 | Accessibility level | **Mandatory** — AA default, AAA opt-in |
| Q15 | Localisation (L10n / I18n) | **Mandatory** — all products have a posture |
| Q16 | Casing convention | Sentence-case default (override-only); Title Case / ALL CAPS / lowercase policy. Recalled across sessions |

**Memory / recall behaviour (Gem-only Memory Consultation):**
- Stable inputs (Q1, Q1b Component Library, Q1c Variables/modes/tier preference, Q7, Q12,
  Q14, Q15, Q16 casing convention, generation mode, grid, token naming convention) are
  recalled across sessions and not re-asked
- Always-fresh inputs (Q1a target file/page, Q4, Q5, Q5a, Q6, Q9, Q10, Q11, Q13) are
  gathered fresh each screen
- ⚠️ Verify — recalled but confirmed before use: Q2 (UI type), Q3 (product/user),
  Q1c modes default for this screen, Q1c exclusions (may be project-stable or
  screen-specific)

---

## Phase 2 flag sequence (current, ordered)

The 14 flags run in this exact order — changing the order breaks downstream dependencies.

1. **Scope** — catch too-broad or too-narrow requests first
2. **Ambiguity** — resolve unclear inputs before content audits
3. **CTA Flag** — verify Q5a designation; audit labels, hierarchy, compensatory affordances
4. **Casing Flag** — normalise mixed casing, hardcoded ALL CAPS, Title Case CTAs, and
   unconfirmed lowercase to the active convention (sentence case by default, per Q16)
5. **Raw Value Translation** — clean up tokens before design system check; redirects raw
   styling values (incl. elevation/shadows and motion timing) to the active authority file
   (Figma Variable, DESIGN.md, Make Kit)
6. **Grid Flag** — clean up spacing before design system check
7. **Design System** — confirm token convention and authority source (covers named systems,
   DESIGN.md, Figma Component Library, Figma Variables)
8. **Make Kit** — Figma Make authority file (verify reference and readiness warning)
9. **DESIGN.md** — Google Stitch authority file (verify reference and readiness warning)
10. **Figma MCP** — Claude Code + Figma MCP authority (verify `figma-use` prerequisite,
    `use_figma` instruction, target URL, library/variable references, section-by-section
    assembly, readiness warning)
11. **Accessibility** — contrast risks, target sizes, focus states, alt text, reflow,
    motion / reduced-motion
12. **L10n** — fixed-width containers, hardcoded formats, RTL, font stacks, pluralisation
13. **Acceptance Criteria** — generate/verify AC after all flags have run; includes
    Figma-MCP-conditional items when Q1a/Q1b/Q1c apply
14. **Platform** — final platform-specific checks (Figma Make, Google Stitch, Claude Code
    + Figma MCP, generic)

---

## Token naming conventions

All six value categories (color, typography, spacing, radius, elevation, motion) support
side-by-side conventions in the output template's Constraints rows: CSS variable,
DESIGN.md dot-path, and Figma Variable. Use whichever
matches the active authority file (or all three as example syntax when no authority is
active and the user is choosing).

| Category | CSS variable | DESIGN.md dot-path | Figma Variable | Notes |
|---|---|---|---|---|
| Color | `--color-primary` | `{colors.primary}` | `{color/primary}` in Collection `[name]` | Map by semantic role, not visual appearance |
| Typography | `--font-size-base` | `{typography.body}` | `{typography/body}` in Collection `[name]` | Map by scale position, not absolute px value |
| Spacing | `--space-xl` | `{spacing.xl}` | `{spacing/xl}` in Collection `[name]` | Always grid-correct before tokenising |
| Radius | `--radius-md` | `{rounded.md}` | `{rounded/md}` in Collection `[name]` | DESIGN.md uses `rounded`, not `radius` |
| Elevation | `--shadow-md` | `{elevation.md}` | effect Variable in Collection `[name]` | Scale none/xs/sm/md/lg/xl; map raw `box-shadow` by depth + role; DESIGN.md uses `elevation` key (not a standard tier — define when present) |
| Motion | `--duration-base`, `--easing-standard` | `{motion.duration.base}` (if file defines a motion tier) | effect/number Variable | Durations fast/base/slow (~150/250/400ms); easings standard/decelerate/accelerate; reference tokens, never raw ms/easings |

**Figma Variable syntax:** Use the literal Figma form `{group/variable-name}` — the
slash-grouped Variable name as it appears in the Variables panel. The Collection is
metadata (e.g., "Theme", "Light/Dark") captured separately at Q1c and named in the
prompt's Variables field; it is not embedded in each reference. Initially documented as
`{Collection}/{Group}/{variable-name}` in v2.15.0; corrected to literal Figma form in
v2.16.1.

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
- When motion is present: transitions reference motion tokens, plus a `prefers-reduced-motion`
  rule

**In Constraints block:**
- 8px grid with token name examples
- Color tokens row (both conventions)
- Typography tokens row (both conventions)
- Spacing tokens row (both conventions)
- Radius tokens row (both conventions)
- Elevation row (always present; defaults to "flat / no elevation unless specified")
- Casing row (always present; sentence case by default + ALL CAPS / lowercase policy)
- Motion row (only when behaviors involve transitions/animation; tokens + reduced-motion rule)
- Full WCAG 2.2 AA block with numeric values (not just "WCAG AA")
- CTA hierarchy block with tier assignments

**In Acceptance Criteria block (pre-populated, not placeholder text):**
- 4 CTA hierarchy items (always present)
- 1 casing item (always present)
- 5 WCAG 2.2 AA items (always present)
- 1 reduced-motion item (commented out, uncommented when motion is in scope)
- 6 L10n items (commented out, uncommented when L10n is required)
- 3 Claude Code + Figma MCP baseline items (commented out, uncommented when Q1 = Claude
  Code + Figma MCP)
- 2 Component Library items (commented out, uncommented when Q1b is provided)
- 5 Figma Variables items (commented out, uncommented when Q1c is provided): all
  properties bound where Variables exist, no hardcoded raw values, mode-aware bindings
  when Collections have modes, semantic-tier preference over primitive-tier, and no
  bindings to excluded Collections/Groups
- Project-specific slots at the end

---

## Platform-specific authority files

| Platform | Authority file(s) | Established by | Phase 2 flag | Token convention |
|---|---|---|---|---|
| Figma Make | Make Kit (project attachment) | Q1 platform choice | Make Kit Flag | Make Kit's own |
| Google Stitch | DESIGN.md (project root) | Q1 platform choice | DESIGN.md Flag | dot-path with `{}` |
| Claude Code + Figma MCP | Figma Component Library (Q1b) and/or Figma Variables (Q1c) — independent dual authority | Q1 platform choice + Q1b/Q1c follow-ups | Figma MCP Flag | Figma Variable `{group/variable-name}` with Collection captured separately |
| Cursor | `.cursorrules` | Q7 | Design System Flag | CSS variable |
| Claude Code (generic) | `CLAUDE.md` | Q7 | Design System Flag | CSS variable |
| Other | Custom guidelines file | Q7 | Design System Flag | Follow file's own convention |

When an authority file is present: the generated prompt instructs the AI to read it (or
bind to it, in the Figma Variables case) before generating. Token values come from the
file. The prompt references token names only — no raw values. Authority file values cannot
be overridden by prompt-level specifications. For Claude Code + Figma MCP, Component
Library and Variables fire independently — one, both, or neither can be active, and each
covers a different category (components vs. styling).

---

## Intentional differences between SKILL.md and Gem

Do not "fix" these differences — they are correct.

| Feature | SKILL.md | Gem |
|---|---|---|
| Memory Consultation | Absent | Present (Gemini requires explicit instructions) |
| Version history (full table) | Absent | Present (Gem is versioned for human editors) |
| Version marker (single-line) | YAML comment + italic byline under H1 | Implicit (the version table fills this role) |
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
- Adding/changing a question (covers top-level Q-numbers and letter-suffixed conditional
  follow-ups like Q1a/Q1b/Q1c)
- Adding/changing a Phase 2 flag
- Adding/changing a token translation table
- Adding/changing output template content
- Adding/changing an accessibility requirement
- Adding/changing a CTA hierarchy rule
- Adding/changing an L10n / I18n requirement
- Adding/changing a guidelines file type
- Adding/changing the Authority File Gate
- Adding/changing the Claude Code + Figma MCP scenario
- Adding/changing a principle
- Tone or wording changes
- Bumping the version (Gem table row + Gem "Current version" line + SKILL.md YAML
  comment + SKILL.md byline — all four locations must move in lockstep)

---

## Current file versions

| File | Version | Last updated |
|---|---|---|
| SKILL.md | 2.18.0 (YAML comment + italic byline under H1) | 2026-06-01 |
| vibe-prompt-architect-gem.md | 2.18.0 | 2026-06-01 |
| SYNC-MANIFEST.md | No version (living document) | 2026-06-01 |
| README.md | No version | 2026-06-01 |
| vibe-prompt-architect-reconstruction-brief.md | No version (this file) | 2026-06-01 |

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
- 2.11: File Creation Rule (line-break / unwrapped-prose discipline)
- 2.12: Authority File Gate introduced — Q7/Q8/Q8b moved before Q9; Q9 branches on
  authority state; "Authority file gates primitive intake" principle added
- 2.13: Platform-implied authority — Q8 and Q8b removed; Figma Make / Google Stitch
  establish Make Kit / DESIGN.md authority via Q1 platform choice; readiness warnings
  fire after Q1 and appear in output template sections
- 2.14: Output Delivery Rule — generated prompt is raw Markdown delivered in-line in
  the chat (fenced code block), never as a file/canvas/document/Drive artifact/rendered
  preview; new "Output is raw Markdown, in-line in the chat" principle
- 2.15: Claude Code + Figma MCP scenario — third platform-implied authority alongside
  Figma Make and Google Stitch; Q1a/Q1b/Q1c follow-up questions (target file URL,
  Component Library URL, Variables); Authority File Gate expanded to 6 rows / 7 preambles;
  new Figma MCP Flag in Phase 2; new conditional `## Figma MCP` output template section
  with explicit `use_figma` instruction; new "Figma MCP routes to Figma Design" principle
- 2.16.0: Human-readable version marker in SKILL.md — YAML front-matter comment plus
  italic byline directly under the H1 (matches the Gem's "Current version" line)
- 2.16.1: Quality-audit cleanup — Figma Variable syntax corrected to literal Figma
  `{group/variable-name}` form with Collection captured separately at Q1c; Phase 2
  dependency-chain principle updated to name Figma MCP in the flag order; Figma-MCP-
  conditional AC items added to output template + Phase 2 AC Flags; authority-state
  label drift fixed; Translate-don't-discard principle extended to Figma Variables;
  SYNC-MANIFEST touchpoints corrected
- 2.17.0: Q1c expanded into a structured four-part ask (still one turn): Collection(s)/
  Group(s) + location; optional exclusions; modes per Collection + default mode for this
  screen; optional tier preference (semantic default). Embedded best-practice guidance:
  for theme-switching designs, bindings must be mode-aware (reference the Collection,
  never hardcode a mode); semantic-tier Variables preserve intent and adapt across modes.
  Q1 readiness notice gains a fourth pre-flight item — library-published Variables must
  be enabled in the target file. Phase 1 closing summary gains three conditional rows
  (modes, tier preference, exclusions). Output template Figma MCP block gains three new
  metadata lines and three new instruction bullets. Phase 2 Figma MCP Flag + AC Flags +
  output template AC block all extended with the new captures. "Figma MCP routes to
  Figma Design" principle extended with semantic-tier-preferred and mode-aware defaults.
  Gem Memory Consultation table extended with Variable modes, Variable tier preference,
  and Variable exclusions rows
- 2.18.0: Four built-in capabilities from the TODO backlog. **Casing & Capitalization** —
  standing rule + Q16 (sentence-case default; ALL CAPS via `text-transform` only;
  lowercase/Title Case as brand choices), Casing Flag (after CTA), always-present Casing
  Constraints row, mandatory casing AC item, new principle. **Guided generation review** —
  Phase 3 Generation Mode offers full-prompt vs section-by-section approve/revise, then
  assembles one raw Markdown block; preference recalled; new principle. **Elevation tokens** —
  Raw Value Translation maps `box-shadow`/depth to `--shadow-*` / `elevation.*` (none–xl);
  always-present Elevation Constraints row. **Motion tokens** — Motion & Animation standing
  rule (`--duration-fast/base/slow`, `--easing-standard/decelerate/accelerate`); raw
  durations/easings translated; reduced-motion (`prefers-reduced-motion`, WCAG 2.3.3)
  required when motion present, with conditional Motion Constraints row, Behavior guidance,
  Accessibility-flag check, conditional motion AC item; new principle. Phase 2 flag count
  4→ (Casing inserted after CTA). Gem Memory Consultation gains Casing convention and
  Generation mode rows

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

5. **Apply all 24 design principles.** Every section of every file should be consistent
   with the principles listed in this brief. Both SKILL.md and the Gem must carry all 24
   in the same order.

6. **Verify the Phase 2 flag order.** Both files must have the 13 flags in the exact
   dependency order listed in this brief — including the Figma MCP Flag between DESIGN.md
   Flag and Accessibility Flag.

7. **Verify the token tables.** All four token categories (Color, Typography, Spacing,
   Radius) must show CSS variable, DESIGN.md dot-path, and Figma Variable convention
   side-by-side in the output template's Constraints rows. Figma Variable form is the
   literal `{group/variable-name}` with the Collection named separately.

8. **Verify the Authority File Gate.** Both files must carry the 6-row status table and
   7 preamble variants (Make Kit / DESIGN.md / Figma Component Library and Variables both
   active / only Figma Component Library / only Figma Variables / named system / none).
   The gate evaluates Q1 platform, Q1b/Q1c Figma library/Variables, and Q7 design system.

9. **Verify the output template.** The template must include: all five header metadata
   fields; the mandatory Elements hints; the mandatory Behavior focus ring line; all
   token rows in Constraints (with three-convention examples); the full WCAG block with
   numeric values; the CTA hierarchy block; the pre-populated AC mandatory items (CTA +
   AA); the four conditional sections (`## Localisation & I18n`, `## Design Guidelines`,
   `## Make Kit (Figma Make only)`, `## Figma MCP (Claude Code + Figma MCP only)`); and
   the conditional commented AC items for L10n and Claude Code + Figma MCP scenarios.

10. **Verify the version markers.** SKILL.md carries a YAML front-matter comment line
    plus an italic byline directly under the H1, both matching the Gem's "Current
    version" line and the latest row in the Gem's Version History table.

11. **Set the Gem version.** The Gem version should be set to the latest version in the
    version history summary, unless a specific version is requested. Mirror that version
    to the SKILL.md YAML comment and byline.

12. **Update the SYNC-MANIFEST.md sync health log.** Add an entry recording the date
    and reason for reconstruction.
