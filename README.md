# Vibe Prompt Architect

A structured intake and prompt-generation workflow for AI-powered UI prototyping and
code-generation tools. Available as a **Claude Skill** and a **Gemini Gem** — two
implementations of the same workflow for two different AI platforms.

> 🚀 **New here?** Read the [Cheat Sheet](CHEATSHEET.md) — a ~5-minute reference on what to prepare before a session and how to get the best result.

---

## What it does

Vibe Prompt Architect guides you through a structured requirements-gathering conversation,
then generates a copy-ready Markdown prompt you can paste directly into any vibe-coding
tool. It replaces the common pattern of typing a rough description into a tool, getting a
mediocre result, and iterating blindly — with a deliberate, front-loaded process that
produces better output on the first run.

The workflow has three phases:

1. **Gather** — A sequential question-by-question intake covering the 15+ inputs that
   determine prompt quality: platform, UI type, product context, required elements, primary
   CTA, behaviors, constraints, design system, accessibility level, localisation scope, and
   casing convention.
   Platform-specific follow-ups fire for Figma Make, Google Stitch, and Claude Code +
   Figma MCP — including the target Figma file, any Component Library, and any Variable
   Collections in use. For code-generation platforms, the intake also asks your **tech
   stack** (framework, styling system, component library) so the prompt matches your project
   instead of assuming React + Tailwind + shadcn/ui — when you don't know a detail, the
   prompt stays framework-neutral and leaves a developer call-out rather than guessing. For
   Claude Code + Figma MCP it first asks **which direction** you want: build the screen
   directly in Figma Design, or build code in your project using a Figma frame as reference.
2. **Clarify** — An automated audit of your inputs that catches ambiguity, off-grid spacing
   values, raw color/type values that need tokenising, missing CTA hierarchy, accessibility
   risks, and localisation gaps — before the prompt is generated.
3. **Generate** — A structured TC-EBC prompt (Task, Context, Elements, Behavior,
   Constraints) in **raw Markdown delivered in-line in the chat** (wrapped in a fenced
   code block) so you can copy it verbatim into your tool of choice. The output is never
   delivered as a file, canvas, document, or rendered preview — keeping the Markdown
   syntax visible and the copy/paste flow trivial. You can take the full prompt at once,
   or opt into a **guided review** that walks each section (Task, Context, Elements, and
   so on) for approve-or-revise before assembling the final block.

---

## Who it's for

**Product designers and UX practitioners** who use AI tools to prototype UI screens,
components, or flows and want more consistent, on-spec results with fewer reruns.

**Design technologists and front-end developers** who generate UI code from prompts and
need prompts that respect a design system, token conventions, grid, and accessibility
requirements.

**Design leads and teams** building a shared prompting practice — the structured format
produces prompts that are reviewable, repeatable, and improvable over time.

It is **not** a general-purpose UI generator. It generates prompts for your tool of
choice — it doesn't generate UI directly. You still run the prompt in Figma Make, Lovable,
Claude Code, Google Stitch, Cursor, or whichever platform you use.

---

## Supported platforms

The generated prompt is platform-neutral and works with any AI UI tool. The workflow has
specific support for:

| Platform | Special handling |
|---|---|
| **Figma Make** | Detects and enforces Make Kit as the sole source of truth for components and styling when one is attached |
| **Google Stitch** | Detects and enforces DESIGN.md as the authoritative token source; uses dot-path token references (`{colors.primary}`, `{spacing.lg}`, `{rounded.md}`) |
| **Claude Code + Figma MCP** | First asks the **build direction**: (a) create the screen directly in Figma Design via `use_figma`, or (b) build code in your project using a Figma frame as a read-only reference (`get_design_context` / `get_screenshot`). Branch (a) captures the target Figma file/page URL, an optional Component Library URL, and any Variable Collections/Groups (with modes, default mode for the screen, tier preference, and exclusions), and includes the mandatory `figma-use` skill prerequisite plus an explicit `use_figma` instruction block; Variable references use literal Figma form `{group/variable-name}`. Branch (a) also enforces **Make-ready construction** — nested auto-layout (no Groups), Fill/Hug + Min/Max sizing, higher-order library instances (never detached), semantic codebase-synced layer names, clean layers, and native Slots where useful — so the screen it creates on the Figma canvas can be reused as input for Figma Make or other vibe-coded projects. Branch (b) captures the source Figma URL plus your tech stack and produces read-from-Figma/write-to-code instructions with no `use_figma` |
| **Cursor, Claude Code (generic), WordPress, and other code targets** | Asks your tech stack (framework, styling system, component library) and writes the prompt to match it — no assumed framework or library; references platform guidelines files (`.cursorrules`, `CLAUDE.md`) using the "read before generating" pattern when present |
| **All other tools** | Platform-neutral token and constraint format using CSS variable convention (`--color-primary`, `--space-lg`); no framework or component library assumed unless you name one |

---

## What the generated prompt contains

Every prompt produced by this workflow includes:

**Header metadata**
- Target platform
- Scope (component / screen / flow)
- Prototype type (functional prototype / design mockup)
- Accessibility level (WCAG 2.2 AA or AAA)
- Localisation status and target languages

**Plan-first instruction** — a standing line right after the header tells the target AI to read the whole prompt, outline an implementation plan, confirm it covers every Element and Constraint, and not start building until the plan is complete.

**TC-EBC prompt body**
- **Task** — one sentence, action verb, UI type, product context
- **Context** — 2–4 sentences covering user type, prior step, next step, emotional context
- **Elements** — exhaustive component list with CTA tier labels (`[Primary]`, `[Secondary]`, `[Tertiary]`, `[Destructive]`), alt text for images, and pluralisation flags for count-dependent strings
- **Behavior** — all interactive states, conditional logic, transitions (referencing motion tokens), a mandatory focus ring entry for keyboard/switch access, and a `prefers-reduced-motion` rule when motion is present
- **Constraints** — platform, 8px grid with token names, design system reference, colour/typography/spacing/radius/elevation token rows (both CSS variable and DESIGN.md dot-path), a casing convention row, a motion row when applicable, full WCAG 2.2 AA/AAA requirements with numeric values, CTA hierarchy rules, and explicit prohibitions

**Conditional sections** (included only when relevant)
- `## Localisation & I18n` — target locales, directionality, string expansion headroom, format tokens, font fallback stacks, pluralisation requirements, pseudo-localisation guidance
- `## Design Guidelines` — instruction for the AI to read DESIGN.md or another guidelines file before generating
- `## Make Kit (Figma Make only)` — instruction for Figma Make to use the attached Make Kit as its sole source of truth
- `## Figma MCP (Claude Code + Figma MCP only)` — readiness warning (MCP server, `figma-use` skill, edit access, library Variable subscription), target Figma file/page URL, Component Library URL, Variables (Collections/Groups), Variable modes + default mode, Variable tier preference, Variable exclusions, plus an explicit `use_figma` instruction block covering components, Variables, tier preference, modes, exclusions, auto-layout, and naming

**Acceptance Criteria** — a pre-populated checklist of binary pass/fail items covering CTA hierarchy (4 items), casing (1 item), WCAG 2.2 AA (5 items), a conditional reduced-motion item (when motion is in scope), conditional L10n items (6 items), and Claude Code + Figma MCP items (3 baseline + 2 when a Component Library is provided + 5 when Variables are provided — all commented out and uncommented as applicable), plus project-specific slots

---

## Built-in quality requirements

These requirements are applied to every prompt by default — you don't need to ask for them:

### Accessibility — WCAG 2.2 AA
- Text contrast ≥ 4.5:1 (normal), ≥ 3:1 (large text and UI components)
- Touch/pointer targets ≥ 44×44px (iOS) / ≥ 48×48px (Android) recommended for touch; 24×24px is the bare WCAG 2.2 AA floor (SC 2.5.8), not a design target
- Visible focus indicator on all interactive elements
- Content reflows at 320px viewport width
- Text spacing overridable (line height, letter spacing, word spacing)
- Visible labels match accessible names of their controls
- All meaningful images and icon controls have text alternatives

AAA compliance (7:1 contrast, 44×44px mandatory targets, enhanced focus, strict text presentation) is opt-in via a dedicated intake question.

### CTA hierarchy
- Exactly one primary CTA per screen
- Secondary and tertiary CTAs visually subordinate — no equal-weight competing actions
- CTA labels are outcome-oriented ("Create account", not "Submit")
- Helper text used to explain a CTA is flagged as a design smell — the label or layout is fixed instead

### 8px grid
- All spacing and radius values corrected to the 8px base grid (4px microgrid for fine-grained internal spacing)
- Off-grid values detected, rounded to the nearest 4px multiple, translated to token names, and the correction is shown to you before proceeding

### Token-first values
- Raw hex colors, pixel measurements, and hard-coded type sizes are automatically translated to the nearest semantic token name
- DESIGN.md projects use dot-path syntax; all others use CSS variable convention
- Ambiguous values are flagged for your confirmation rather than silently assigned

### Elevation tokens
- Raw `box-shadow` and drop-shadow values are translated to a semantic elevation scale (`--shadow-none` through `--shadow-xl`, or `elevation.*` in DESIGN.md), mapped by depth and role (resting card, dropdown, modal)
- Every prompt carries an Elevation row in Constraints, defaulting to "flat / no elevation unless specified"

### Casing
- Sentence case is the default for all UI text — buttons, labels, headings, nav ("Create account", not "Create Account") — the modern design-system standard and the safest for localisation
- ALL CAPS is reserved for short overline/eyebrow labels and applied via `text-transform`, never hardcoded — this keeps accessible names and translation sources in natural case
- all-lowercase and Title Case are deliberate brand choices you can opt into; the convention is recalled across sessions

### Motion
- When behaviors include transitions or animation, timing and curve reference motion tokens (`--duration-fast/base/slow`, `--easing-standard/decelerate/accelerate`) instead of raw milliseconds or easings
- Non-essential motion respects `prefers-reduced-motion` (WCAG 2.3.3) — reduced or removed for users who request it
- Screens with no motion carry no motion guidance — the workflow never invents animation

---

## Localisation support

When you confirm localisation is required, the workflow generates a `## Localisation & I18n`
section covering:

- **String expansion headroom** — containers sized for the widest-expanding language in your locale set (German ~35%, Finnish up to 40%)
- **RTL layout** — full spatial mirroring for Arabic, Hebrew, Persian, and Urdu (not just text alignment)
- **Format tokens** — dates, numbers, currencies, and units via `Intl.NumberFormat` / `Intl.DateTimeFormat`, never hardcoded
- **Font fallback stacks** — non-Latin script coverage for Arabic, CJK, Devanagari, and Thai
- **Pluralisation** — count-dependent strings flagged for pluralisation token support (Arabic has 6 plural forms; English has 2)
- **Pseudo-localisation** — artificial expansion strings to surface overflow before real translations exist

Localisation scope is a mandatory intake question — every project has a localisation posture, even if the answer is "English only."

---

## Figma Variables support (Claude Code + Figma MCP)

When the platform is **Claude Code + Figma MCP**, the workflow treats your Figma
Component Library and Figma Variables as a granular dual authority — Component Library
covers components, Variables cover styling, and the two fire independently. The intake
asks you to provide:

- **Target Figma file/page URL** — where the screen should be created. Required.
- **Component Library URL** *(optional)* — if you have one, the prompt instructs the AI
  to import components from it rather than building from primitives, and to inspect the
  library for any embedded Variable Collections
- **Variable Collection(s) and Group(s)** *(optional)* — which ones to use, where they
  live (target file / Component Library / both), modes per Collection and the default
  mode for the screen, optional tier preference, and any exclusions
- **Library Variable subscription** is checked as a pre-flight item — Variables published
  in a Component Library must be enabled in the target file or `use_figma` cannot bind
  to them

Two best-practice defaults are enforced for Variable bindings unless you override them
at intake:

- **Semantic-tier preferred.** Bindings reference semantic-tier Variables (e.g.,
  `{color/text/primary}`, `{color/surface/raised}`) over primitive-tier Variables (e.g.,
  `{color/gray/900}`, `{color/blue/500}`). Semantic Variables preserve design intent,
  adapt correctly across modes, and survive primitive re-mapping.
- **Mode-aware.** When a Collection includes modes (Light/Dark, Density, Brand),
  bindings reference the Collection so Figma resolves the active mode at render time.
  A specific mode's value is never hardcoded — that would defeat the Variable system
  and break mode switching.

Variable references in the generated prompt use literal Figma form `{group/variable-name}`,
matching how Variables appear in Figma's Variables panel. The Collection is captured
separately at intake and named in the prompt's Variables field, not embedded in each
reference.

---

## Installation and use

### Claude Skill (SKILL.md)

1. Place `SKILL.md` in your Claude Skills directory (typically `/mnt/skills/user/vibe-prompt-architect/`)
2. Claude will trigger the skill automatically when you use phrases like "help me prompt", "write a vibe-coding prompt", "I want to build a UI with AI", or describe a screen you want to build
3. Answer the intake questions one at a time — Claude will stop after each question and wait for your reply
4. Review the Phase 2 summary and confirm or correct it
5. Copy the generated Markdown prompt into your vibe-coding tool

**Session behaviour:** Claude recalls stable project-level inputs (platform, design system, WCAG level, localisation scope) across conversations. You won't be asked to repeat established decisions when building subsequent screens in the same project.

### Gemini Gem (vibe-prompt-architect-gem.md)

1. Go to [gemini.google.com](https://gemini.google.com) and create a new Gem
2. Paste the full contents of `vibe-prompt-architect-gem.md` into the Gem instructions field
3. Save the Gem and start a conversation
4. Answer the intake questions one at a time
5. Review the Phase 2 confirmation summary — items recalled from previous sessions are annotated *(recalled)* so you can spot any that are out of date
6. Copy the generated Markdown prompt into your vibe-coding tool

**Session behaviour:** The Gem scans prior conversation history for stable project inputs before asking questions. Recalled inputs are shown in the confirmation summary with a *(recalled / new)* annotation so you can verify them before proceeding.

---

## The TC-EBC framework

The prompt format is built on TC-EBC — a five-section structure designed to give AI tools
enough context to make good decisions without over-constraining them:

| Section | Purpose |
|---|---|
| **T — Task** | One sentence. What to build. Action verb + UI type + product context. No style adjectives. |
| **C — Context** | 2–4 sentences. User type, prior step, next step, emotional context if design-relevant. |
| **E — Elements** | Exhaustive component list. Everything not listed may be omitted or replaced. CTA tiers, alt text, pluralisation flags. |
| **B — Behavior** | All interactive states and logic. Focus ring is mandatory. |
| **C — Constraints** | All rules: design system, grid, tokens, platform, CTA hierarchy, accessibility, localisation, prohibitions. |

The framework is designed around a key insight: **the AI tool can only act on what you
write**. Vague inputs produce generic outputs. The intake process exists to surface the
specifics that most prompts leave out.

---

## Design token support

The workflow supports four token naming conventions simultaneously:

| Convention | When used | Example |
|---|---|---|
| CSS variable | Default for all platforms | `--color-primary`, `--space-xl`, `--radius-md` |
| DESIGN.md dot-path | When a DESIGN.md is confirmed | `{colors.primary}`, `{spacing.xl}`, `{rounded.md}` |
| Figma Variable | When Claude Code + Figma MCP with Variables in scope | `{color/primary}`, `{spacing/xl}`, `{rounded/md}` — Collection captured separately |
| Tailwind / Material 3 | When that design system is named | `bg-primary`, `p-6`, `rounded-md` / `md-sys-color-primary` |

All four value categories have translation tables:
- **Color** — mapped to semantic roles (primary, surface, error, etc.) by visual function
- **Typography** — mapped to scale position (xs through 3xl), line height ratios, and weight tiers
- **Spacing** — mapped to an 8px-aligned T-shirt scale (2xs through 4xl)
- **Radius** — mapped to semantic scale (none through full), with DESIGN.md `rounded` key convention

---

## Files in this package

| File | Purpose |
|---|---|
| `SKILL.md` | Claude Skill — drop into your Skills directory. Carries a version marker (YAML comment + italic byline under H1) matching the knowledge file |
| `vibe-prompt-architect-gem.md` | Gemini Gem **router** — the minimal instruction text pasted into a new Gem's instruction box; points at the knowledge files |
| `vibe-prompt-architect-knowledge.md` | Gemini Gem **knowledge file** — the full operating procedures, uploaded as a Gem attachment. This is the content sync partner with `SKILL.md` |
| `vibe-prompt-architect-tc-ebc-primer.md` | Gemini Gem **knowledge file** — the TC-EBC framework origin and rationale, uploaded as a second Gem attachment |
| `Figma Design to Figma Make Best Practices (Mid-2026 Update).md` | Reference doc — source of record for the Make-ready Figma construction rules used by the Claude Code + Figma MCP build-in-Figma branch. Not attached to the Gem |
| `CHANGELOG.md` | Authoritative version history for both the Skill and the Gem |
| `CHEATSHEET.md` | User cheat sheet — a ~5-minute reference covering what the Skill/Gem does, what to prepare, the intake at a glance, built-in quality, and tips for a better session |
| `SYNC-MANIFEST.md` | Maintenance document — section map, feature touch map, pre-commit checklist, and intentional differences list for keeping the two implementation files in sync |
| `vibe-prompt-architect-reconstruction-brief.md` | Reconstruction brief — design authority for rebuilding the file set from scratch in a new Claude session, or for onboarding a collaborator. Explains the *why* alongside the content files' *what* |
| `README.md` | This file |

---

## Maintenance

The Skill and Gem implement the same workflow in two different AI platforms. They are kept
in sync using `SYNC-MANIFEST.md`. If you extend or modify the workflow:

1. Open `SYNC-MANIFEST.md` first and identify which Feature Touch Map entries are affected
2. Upload all three files (`SKILL.md`, `vibe-prompt-architect-gem.md`, `SYNC-MANIFEST.md`) to Claude
3. Describe your change — Claude will apply it consistently to both files
4. Bump the Gem version number and add a changelog entry — and update SKILL.md's matching version marker (YAML comment + italic byline under H1) in lockstep
5. Update `SYNC-MANIFEST.md` if the change introduces a new feature or section

The Gem uses semantic versioning. The current version is **2.18.1**. Major versions (X.0.0)
indicate breaking changes to the output format or workflow structure. Minor versions (X.Y.0)
indicate new capabilities. Patch versions (X.Y.Z) indicate fixes and clarifications.

For a full rebuild from scratch (e.g., recovering from file loss or onboarding a new
collaborator), use `vibe-prompt-architect-reconstruction-brief.md` as the design authority
alongside any surviving content files.

---

## Known limitations

- **The workflow generates prompts, not UI.** You still need a vibe-coding platform to execute the prompt and generate actual screens or code.
- **Acceptance criteria require manual verification.** The AC checklist tells you what to check; it doesn't run automatically against the generated output.
- **Color contrast is flagged, not calculated.** The workflow identifies likely contrast risks (light grey on white, low-saturation combinations) but doesn't compute exact ratios. Use the [WebAIM Contrast Checker](https://webaim.org/resources/contrastchecker/) to verify.
- **DESIGN.md token names are referenced, not validated.** If a token name in your DESIGN.md has changed, the workflow won't detect the mismatch — it references the name you provided.
- **Claude Code + Figma MCP requires pre-flight setup that the workflow can't verify automatically.** Before running a Figma-MCP-targeted prompt, you must independently confirm: (1) the Figma MCP server is connected and authenticated in Claude Code; (2) the `figma-use` skill is loaded (it is a mandatory prerequisite before any `use_figma` call); (3) you have edit access to the target Figma file; (4) any Variables published in a separate Component Library are *enabled* (subscribed) in the target file. The generated prompt includes a readiness warning that lists these, but the workflow itself cannot check them on your behalf.
- **Figma Variable names are referenced, not validated.** As with DESIGN.md, if a Variable name or Collection name has changed in Figma since you captured it, the workflow won't detect the mismatch.
- **The Gem has no persistent memory between browser sessions** unless Gemini Gems memory is enabled in your account. The Memory Consultation section of the Gem scans the current conversation thread for prior context, but cannot access previous sessions unless that session's history is available.
