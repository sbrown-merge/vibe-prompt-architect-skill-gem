# Vibe Prompt Architect

A structured intake and prompt-generation workflow for AI-powered UI prototyping and
code-generation tools. Available as a **Claude Skill** and a **Gemini Gem** — two
implementations of the same workflow for two different AI platforms.

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
   CTA, behaviors, constraints, design system, accessibility level, and localisation scope.
2. **Clarify** — An automated audit of your inputs that catches ambiguity, off-grid spacing
   values, raw color/type values that need tokenising, missing CTA hierarchy, accessibility
   risks, and localisation gaps — before the prompt is generated.
3. **Generate** — A structured TC-EBC prompt (Task, Context, Elements, Behavior,
   Constraints) in native Markdown, ready to copy into your tool of choice.

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
| **Cursor, Claude Code** | References platform guidelines files (`.cursorrules`, `CLAUDE.md`) using the "read before generating" pattern |
| **All other tools** | Platform-neutral token and constraint format using CSS variable convention (`--color-primary`, `--space-lg`) |

---

## What the generated prompt contains

Every prompt produced by this workflow includes:

**Header metadata**
- Target platform
- Scope (component / screen / flow)
- Prototype type (functional prototype / design mockup)
- Accessibility level (WCAG 2.2 AA or AAA)
- Localisation status and target languages

**TC-EBC prompt body**
- **Task** — one sentence, action verb, UI type, product context
- **Context** — 2–4 sentences covering user type, prior step, next step, emotional context
- **Elements** — exhaustive component list with CTA tier labels (`[Primary]`, `[Secondary]`, `[Tertiary]`, `[Destructive]`), alt text for images, and pluralisation flags for count-dependent strings
- **Behavior** — all interactive states, conditional logic, transitions, and a mandatory focus ring entry for keyboard/switch access
- **Constraints** — platform, 8px grid with token names, design system reference, colour/typography/spacing/radius token rows (both CSS variable and DESIGN.md dot-path), full WCAG 2.2 AA/AAA requirements with numeric values, CTA hierarchy rules, and explicit prohibitions

**Conditional sections** (included only when relevant)
- `## Localisation & I18n` — target locales, directionality, string expansion headroom, format tokens, font fallback stacks, pluralisation requirements, pseudo-localisation guidance
- `## Design Guidelines` — instruction for the AI to read DESIGN.md or another guidelines file before generating
- `## Make Kit` — instruction for Figma Make to use the attached Make Kit as its sole source of truth

**Acceptance Criteria** — a pre-populated checklist of binary pass/fail items covering CTA hierarchy (4 items), WCAG 2.2 AA (5 items), and conditional L10n items (6 items), plus project-specific slots

---

## Built-in quality requirements

These requirements are applied to every prompt by default — you don't need to ask for them:

### Accessibility — WCAG 2.2 AA
- Text contrast ≥ 4.5:1 (normal), ≥ 3:1 (large text and UI components)
- Touch/pointer targets ≥ 24×24px minimum (AA); 44×44px recommended
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

The workflow supports three token naming conventions simultaneously:

| Convention | When used | Example |
|---|---|---|
| CSS variable | Default for all platforms | `--color-primary`, `--space-xl`, `--radius-md` |
| DESIGN.md dot-path | When a DESIGN.md is confirmed | `{colors.primary}`, `{spacing.xl}`, `{rounded.md}` |
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
| `SKILL.md` | Claude Skill — drop into your Skills directory |
| `vibe-prompt-architect-gem.md` | Gemini Gem instructions — paste into a new Gem |
| `SYNC-MANIFEST.md` | Maintenance document — section map, feature touch map, pre-commit checklist, and intentional differences list for keeping the two implementation files in sync |
| `README.md` | This file |

---

## Maintenance

The Skill and Gem implement the same workflow in two different AI platforms. They are kept
in sync using `SYNC-MANIFEST.md`. If you extend or modify the workflow:

1. Open `SYNC-MANIFEST.md` first and identify which Feature Touch Map entries are affected
2. Upload all three files (`SKILL.md`, `vibe-prompt-architect-gem.md`, `SYNC-MANIFEST.md`) to Claude
3. Describe your change — Claude will apply it consistently to both files
4. Bump the Gem version number and add a changelog entry
5. Update `SYNC-MANIFEST.md` if the change introduces a new feature or section

The Gem uses semantic versioning. The current version is **2.10.0**. Major versions (X.0.0)
indicate breaking changes to the output format or workflow structure. Minor versions (X.Y.0)
indicate new capabilities. Patch versions (X.Y.Z) indicate fixes and clarifications.

---

## Known limitations

- **The workflow generates prompts, not UI.** You still need a vibe-coding platform to execute the prompt and generate actual screens or code.
- **Acceptance criteria require manual verification.** The AC checklist tells you what to check; it doesn't run automatically against the generated output.
- **Color contrast is flagged, not calculated.** The workflow identifies likely contrast risks (light grey on white, low-saturation combinations) but doesn't compute exact ratios. Use the [WebAIM Contrast Checker](https://webaim.org/resources/contrastchecker/) to verify.
- **DESIGN.md token names are referenced, not validated.** If a token name in your DESIGN.md has changed, the workflow won't detect the mismatch — it references the name you provided.
- **The Gem has no persistent memory between browser sessions** unless Gemini Gems memory is enabled in your account. The Memory Consultation section of the Gem scans the current conversation thread for prior context, but cannot access previous sessions unless that session's history is available.
