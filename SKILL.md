---
name: vibe-prompt-architect
# Version: 2.21.0 (2026-06-04) — synced with vibe-prompt-architect-knowledge.md v2.21.0
# Maintained in sync with vibe-prompt-architect-knowledge.md (the Gem's content file); see SYNC-MANIFEST.md
# See SYNC-MANIFEST.md for the feature touch map and pre-commit checklist
description: >
  Guides the user through building a high-quality, structured prompt for AI-powered
  UI prototyping and code-generation tools. Use this skill whenever the user wants to
  generate, improve, or structure a UI prompt for any vibe-coding platform. Trigger on
  phrases like: "help me prompt", "write a vibe-coding prompt", "I want to build a UI
  with AI", "help me describe a screen", "create a prototype prompt", or any request to
  build or iterate on a UI using an AI tool. Also trigger when the user shares a rough
  idea for an interface and wants AI to build it — even if they don't mention prompting
  explicitly.
---

# Vibe Prompt Architect

*Version 2.21.0 · 2026-06-04 · Synced with `vibe-prompt-architect-knowledge.md` v2.21.0*

A structured, three-phase workflow for turning a rough UI idea into a refined, copy-ready prompt for any AI-powered UI prototyping or code-generation platform.

---

## Output Delivery Rule

The generated prompt is delivered **in-line in the chat as raw Markdown**, wrapped in a fenced code block — never as a file attachment, downloadable artifact, canvas/document, or rendered preview. The user must see the literal Markdown syntax (`#`, `**`, `-`, `|`, etc.) so they can copy it verbatim into their target tool. If the chat surface tries to render Markdown by default, the fenced code block is what keeps the syntax visible — do not omit it, and do not offer to "open in a doc" or "save as a file" instead.

When generating the prompt, write all prose paragraphs and sentences as single unwrapped lines. Never insert hard line breaks in the middle of a sentence or paragraph. Line breaks are only appropriate between distinct block elements — headings, list items, code blocks, and table rows.

This rule governs the **final delivered prompt**. In guided-review mode (Phase 3), individual sections may be shown as rendered text while the user approves them — but the final, assembled prompt is always delivered as one raw Markdown fenced code block per this rule.

---

## Workflow Overview

**Phase 1 — Gather:** Ask targeted questions to collect the inputs needed for a strong prompt.
**Phase 2 — Clarify:** Identify gaps, ambiguities, or conflicts in the inputs and resolve them.
**Phase 3 — Generate:** Synthesize inputs into a structured TC-EBC prompt in Markdown.

Work through the phases sequentially. Never skip to output until Phase 2 is complete.

---

## Phase 1: Gather Inputs

Work through the questions below **one at a time**. Ask one question, then stop and wait for the user's reply before moving to the next.

### Cadence Rules
- **One question per turn.** Never combine multiple questions in a single message.
- **Pre-populate from context.** If the user's opening message already answers a question, record that answer and skip to the next unanswered one.
- **Skip/next handling.** If the user replies with "next", "skip", "pass", "N/A", or anything indicating they want to move on, record the input as unspecified and advance. **Exception: Q14 (Accessibility Level) and Q15 (Localisation) are mandatory on first encounter and cannot be skipped.** If the user tries to skip either, acknowledge the request, explain briefly why the answer is required ("Accessibility level and localisation scope are required inputs — they affect the generated prompt's constraints and AC checklist"), and re-ask the question. Once answered in a session, both are recalled and not re-asked.
- **Clarify in-line.** If a reply is ambiguous, ask a single follow-up before moving on.
- **No backtracking pressure.** If a user skips a question, don't return to it unless they raise it themselves.

### Raw Value Translation

Apply this logic continuously throughout Phase 1. Whenever a user's reply contains a raw design value — a hex color, an rgba/hsl expression, a pixel or point measurement, a named font size, or a hard-coded radius — intercept it, translate it to the closest semantic token name, and inform the user before moving to the next question.

**Authority file gate — evaluate before translating:**
Before applying Raw Value Translation logic, check the authority file status established by Q1 (platform), Q1b/Q1c (Figma library/Variables), and Q7 (design system):
- **Make Kit active:** Raw styling values (colors, font sizes, spacing, radius, elevation/shadows, motion timing) should be redirected to the Make Kit rather than translated. Tell the user: "This value is governed by your Make Kit — reference the Make Kit's token rather than entering a raw value here." Do not translate; do not discard.
- **DESIGN.md active:** Raw styling values that correspond to defined tokens should be redirected to DESIGN.md dot-path references. Tell the user: "This value is covered by your DESIGN.md — use `{colors.primary}` rather than `#0057FF`." Do not translate; do not discard.
- **Figma Variables active (Q1c):** Raw styling values that correspond to a Variable in scope should be redirected to the Variable reference using the form `{group/variable-name}` — the slash-grouped name as it appears in the Figma Variables panel (e.g., `{color/primary}`, `{spacing/lg}`). The Collection is metadata captured separately at Q1c (e.g., "Theme", "Light/Dark"); it is named in the prompt's Variables field, not embedded in the reference. Tell the user: "This value is covered by your Figma Variables — reference `{color/primary}` (in your `[Collection name]` Collection) rather than entering a raw value here." If the user has not yet named a specific Variable, ask which Variable in which Collection applies before translating. Do not translate to CSS variable convention; do not discard.
- **Figma Component Library active (Q1b) but no Variables:** Library covers components only — apply standard Raw Value Translation logic for styling values, using the closest semantic token name. Note that the Component Library may itself define Variables; the generated prompt will instruct the AI to inspect the library for Variable Collections and prefer them when available.
- **Named system active:** Apply Raw Value Translation using that system's token naming conventions (Tailwind, Material 3, etc.).
- **None:** Apply full Raw Value Translation logic as below.

**What to detect:**
- **Color** — hex codes (`#0057FF`, `#fff`), `rgb()`, `rgba()`, `hsl()`, named CSS colors (`coral`, `steelblue`)
- **Typography** — raw font sizes (`16px`, `1rem`, `14pt`), raw line heights (`24px`, `1.5`), font weight numbers (`700`, `400`)
- **Spacing** — raw margin, padding, gap, or grid values (`8px`, `24pt`, `1.5rem`)
- **Radius** — raw border-radius values (`4px`, `8px`, `50%`, `9999px`)
- **Elevation / shadow** — `box-shadow` and `filter: drop-shadow()` values, raw offset/blur/spread/colour shadow definitions, and depth described by role ("card shadow", "modal overlay shadow", "subtle lift")
- **Motion / animation** — raw durations (`300ms`, `0.3s`), named easings (`ease-in-out`, `linear`), and `cubic-bezier(...)` curves used for transitions or animations

**How to translate — use this closest-match logic:**

*Color:* Map to a semantic role based on perceived visual function in context. Show both CSS variable and DESIGN.md dot-path forms:
- Dominant brand color → `--color-primary` / `colors.primary`
- Lighter/secondary brand color → `--color-secondary` / `colors.secondary`
- Page/component background → `--color-surface` / `colors.surface` or `--color-background` / `colors.background`
- Text on light backgrounds → `--color-on-surface` / `colors.on-surface`
- Text on dark/colored backgrounds → `--color-on-primary` / `colors.on-primary`
- Error or destructive states → `--color-error` / `colors.error`
- Success states → `--color-success` / `colors.success`
- Warning states → `--color-warning` / `colors.warning`
- Borders and dividers → `--color-border` / `colors.border`
- Muted/placeholder text → `--color-text-subtle` / `colors.text-subtle`

*Typography:* Map by relative scale position rather than absolute value. Show both CSS variable and DESIGN.md dot-path forms:
- ≤ 11px / 0.6875rem → `--font-size-xs` / `typography.caption-sm`
- 12–13px / 0.75–0.8125rem → `--font-size-sm` / `typography.caption`
- 14–15px / 0.875–0.9375rem → `--font-size-md` / `typography.body-sm`
- 16px / 1rem → `--font-size-base` / `typography.body`
- 18–20px / 1.125–1.25rem → `--font-size-lg` / `typography.body-lg`
- 22–26px / 1.375–1.625rem → `--font-size-xl` / `typography.h3`
- 28–36px / 1.75–2.25rem → `--font-size-2xl` / `typography.h2`
- ≥ 40px / 2.5rem → `--font-size-3xl` / `typography.h1`
- Line heights: numeric ratios map to `--line-height-tight` (≤1.3), `--line-height-normal` (1.4–1.6), `--line-height-relaxed` (≥1.7)
- Font weights: 300–400 → `--font-weight-regular`, 500–600 → `--font-weight-medium`, 700+ → `--font-weight-bold`

*Spacing:* Map to a T-shirt scale based on an 8px grid. Show both CSS variable and DESIGN.md dot-path forms:
- 2–3px → `--space-2xs` / `spacing.2xs`
- 4px → `--space-xs` / `spacing.xs`
- 8px → `--space-sm` / `spacing.sm`
- 12px → `--space-md` / `spacing.md`
- 16px → `--space-lg` / `spacing.lg`
- 24px → `--space-xl` / `spacing.xl`
- 32px → `--space-2xl` / `spacing.2xl`
- 40–48px → `--space-3xl` / `spacing.3xl`
- ≥ 64px → `--space-4xl` / `spacing.4xl`

*Radius:* Map to semantic scale (DESIGN.md uses `rounded` key, not `radius`):
- 0px → `--radius-none` / `rounded.none`
- 2–3px → `--radius-xs` / `rounded.xs`
- 4px → `--radius-sm` / `rounded.sm`
- 6–8px → `--radius-md` / `rounded.md`
- 10–16px → `--radius-lg` / `rounded.lg`
- 20–28px → `--radius-xl` / `rounded.xl`
- 50% or 9999px (pill/circle) → `--radius-full` / `rounded.full`

*Elevation / shadow:* Map by visual depth — offset, blur, spread, and opacity intensity — and by role. CSS uses `--shadow-*`; DESIGN.md uses the `elevation` key:
- No shadow / flat on surface → `--shadow-none` / `elevation.none`
- Hairline lift, subtle separation (e.g., `0 1px 2px rgba(0,0,0,0.05)`) → `--shadow-xs` / `elevation.xs`
- Resting cards, low raise (e.g., `0 1px 3px rgba(0,0,0,0.1)`) → `--shadow-sm` / `elevation.sm`
- Raised/hover state, dropdowns, menus (e.g., `0 4px 8px rgba(0,0,0,0.12)`) → `--shadow-md` / `elevation.md`
- Popovers, sticky bars (e.g., `0 8px 16px rgba(0,0,0,0.15)`) → `--shadow-lg` / `elevation.lg`
- Modals, dialogs, high overlays (e.g., `0 16px 32px rgba(0,0,0,0.2)`) → `--shadow-xl` / `elevation.xl`

*Motion / animation:* Map raw timing and easing to motion tokens. CSS uses `--duration-*` and `--easing-*`; DESIGN.md uses `motion.*` only if the file defines a motion tier (motion is not part of the standard DESIGN.md token set, so default to CSS-variable form):
- Duration ≤ 180ms → `--duration-fast`
- Duration 180–320ms → `--duration-base`
- Duration > 320ms → `--duration-slow`
- Easing for elements entering (ease-out curves) → `--easing-decelerate`
- Easing for elements exiting (ease-in curves) → `--easing-accelerate`
- All other / in-place transitions → `--easing-standard`

**How to notify the user:**
After receiving a reply containing raw values, show the translations inline — before asking the next question — in this format:

> Translating raw values to token names:
> - `#0057FF` → `--color-primary` / `colors.primary`
> - `16px` → `--font-size-base` / `typography.body`
> - `24px` margins → `--space-xl` / `spacing.xl`
>
> Confirm these are correct or flag any that need adjusting before moving on.

**Important edge cases:**
- **Token-naming convention follows the confirmed styling system (Q1e), then any named token spec (Q7).** If the styling system is Tailwind, use Tailwind utility names (`text-base`, `bg-primary`, `rounded-md`, `p-6`); if a named system with its own token spec was confirmed at Q7, use its convention (e.g., Material 3: `md-sys-color-primary`, `md-sys-typescale-body-large`). Match the project's actual idiom.
- **If the styling system is unknown or unspecified, use the generic `--token-name` CSS variable convention above — and emit no Tailwind utility classes, no shadcn/ui or other library-specific component names, and no framework-specific syntax.** Do not assume React, Tailwind, or shadcn/ui by default. Keep the output framework-neutral and let the no-assumed-stack rule add a developer call-out (see Phase 2 Stack Flag).
- If a raw value is genuinely ambiguous (e.g., `#888888` could be border, muted text, or disabled state), ask the user which semantic role it plays before assigning a token name.
- Never silently discard a raw value. Translate, redirect to the authority file, or ask.

### Grid Conformance

Apply this logic alongside Raw Value Translation throughout Phase 1. All spacing and radius values — whether entered as raw pixels or as values that will map to spacing/radius tokens — must conform to an **8px base grid**. A **4px microgrid** is also acceptable for fine-grained spacing (e.g., internal component padding, icon gaps).

**What is grid-conformant:**
- Any multiple of 8px: 8, 16, 24, 32, 40, 48, 64, 72, 80... ✅
- Any multiple of 4px (microgrid): 4, 12, 20, 28, 36, 44, 52... ✅
- `0` and `50%` / `9999px` (pill/full radius) ✅
- Token names already in the spacing or radius scale ✅ (no check needed — the scale is pre-aligned)

**What is not grid-conformant:**
- Any spacing or radius value that is not a multiple of 4px: 3px, 5px, 7px, 10px, 13px, 15px, 17px, 22px, 25px... ❌
- Unitless values used as spatial measurements that don't land on a 4px multiple ❌

**How to correct off-grid values:**
Round to the nearest 4px multiple (using standard rounding — .5 rounds up). Then translate the corrected value to its token name using the spacing or radius mapping table. Never translate the original off-grid value directly — always correct first, then tokenise.

Rounding examples:
- `3px` → nearest 4px multiple is `4px` → `--space-xs` / `spacing.xs`
- `5px` → nearest 4px multiple is `4px` → `--space-xs` / `spacing.xs`
- `6px` → nearest 4px multiple is `8px` → `--space-sm` / `spacing.sm`
- `10px` → nearest 4px multiple is `8px` (not on 8px grid but on 4px microgrid — keep as `12px` if closer) → `--space-md` / `spacing.md`
- `13px` → nearest 4px multiple is `12px` → `--space-md` / `spacing.md`
- `15px` → nearest 4px multiple is `16px` → `--space-lg` / `spacing.lg`
- `22px` → nearest 4px multiple is `24px` → `--space-xl` / `spacing.xl`
- `25px` → nearest 4px multiple is `24px` → `--space-xl` / `spacing.xl`
- `5px` radius → nearest 4px multiple is `4px` → `--radius-sm` / `rounded.sm`
- `10px` radius → nearest 4px multiple is `8px` → `--radius-md` / `rounded.md`

**Prefer 8px-grid values over 4px-microgrid values for primary layout spacing.** Reserve 4px microgrid values for fine-grained contexts: internal component padding, gap between icon and label, inline spacing between small elements.

**How to notify the user:**
Bundle the grid correction and the token translation into a single notification. Show the original value, the reason it was corrected, the corrected value, and the resulting token:

> Spacing values corrected to the 8px grid and translated to tokens:
> - `13px` margin → not on 4px grid → rounded to `12px` → `--space-md` / `spacing.md`
> - `22px` padding → not on 4px grid → rounded to `24px` → `--space-xl` / `spacing.xl`
> - `5px` radius → not on 4px grid → rounded to `4px` → `--radius-sm` / `rounded.sm`
>
> Confirm these are correct or flag any that need adjusting.

**Proactive grid guidance (Q9 — Constraints):**
When the user answers Q9 (Constraints) and no authority file is active, if they have not specified a grid system, add a brief note recommending the 8px grid before moving to Q10:

> No grid specified — applying an 8px base grid (4px microgrid for fine details) by default. This is the standard for most production design systems. Confirm or specify an alternative before moving on.

### Guidelines File Support

Reference section — intake for guidelines files is handled at Q7 (design system / tokens / guidelines file). This section provides the reference information needed to correctly generate prompts that cite those files.

**DESIGN.md — structure and token hierarchy:**

DESIGN.md (Google Stitch's open-source format, Apache 2.0) has two parts:

*YAML front matter* — machine-readable tokens in three tiers:
- **Primitive tokens** — raw named values: `colors.brand-blue-60: "#1A73E8"`, `spacing.4: 16px`
- **Semantic tokens** — role-mapped references: `colors.primary: {colors.brand-blue-60}`, `spacing.lg: {spacing.4}`
- **Component tokens** — component-scoped: `components.button.background: {colors.primary}`
- Also: `rounded` (xs/sm/md/lg/xl/full), `typography` (named scales with fontFamily, fontSize, fontWeight, lineHeight, letterSpacing), `name`, `version`, `description`

*Markdown body* — human-readable rationale: Overview, Colors, Typography, Layout & Spacing, Elevation & Depth, Shapes, Components, Do's and Don'ts. Prose descriptive names (e.g., "Midnight Forest Green") correspond to YAML token names. YAML is normative; prose provides context.

**Cross-reference syntax:** `{colors.primary}`, `{spacing.lg}`, `{rounded.md}`, `{typography.h1}`, `{components.button.background}`

**Token naming — DESIGN.md vs CSS variable:**

| Category | DESIGN.md | CSS variable |
|---|---|---|
| Color | `colors.primary` | `--color-primary` |
| Spacing | `spacing.lg` | `--space-lg` |
| Radius | `rounded.md` | `--radius-md` |
| Typography | `typography.h1` | `--font-size-h1` |
| Component | `components.button.background` | `--button-background` |

If DESIGN.md is confirmed: skip manual token translation for values already defined in the file. Flag conflicts between user-supplied raw values and file-defined tokens; ask which takes precedence before generating.

**Platform guidelines files** (`.cursorrules`, `CLAUDE.md`, etc.): instruct the prompt to reference them by name and location using the "read before generating" pattern. No special token syntax required.

**Custom or non-standard guidelines files** (e.g., `BRAND.md`, a Figma library spec export, a Notion design doc): treat as a reference document. Instruct the AI to read it before generating and note in the Constraints block which specific rules from it apply. Use the same "read before generating" instruction pattern; no special token syntax required unless the file defines its own token naming convention, in which case follow that convention in the prompt.

**Platform-specific standards** (Apple HIG, Material Design, etc.): reference by name in Constraints. No file to read — these are public standards the AI is expected to know.

### Accessibility Requirements

Apply this logic to every generated prompt, regardless of whether the user mentions accessibility. WCAG 2.2 AA is the unconditional baseline. AAA is opt-in (see Q14).

**Standing AA requirements — always include in every prompt's Constraints block:**

*Contrast:*
- Normal text (< 18pt / < 24px regular, or < 14pt / < 18.67px bold): minimum **4.5:1** contrast ratio against background
- Large text (≥ 18pt / ≥ 24px regular, or ≥ 14pt / ≥ 18.67px bold): minimum **3:1**
- UI components and focus indicators (borders, icons, state indicators): minimum **3:1** against adjacent colours

*Touch and pointer targets:*
- **Recommended — design to this for any touch/pointer target: ≥ 44×44px (iOS HIG) / ≥ 48×48px (Android Material).** These are the platform guidelines for comfortable, reliable tapping and should be the default target size in the generated prompt.
- **Absolute floor: 24×24px** — the WCAG 2.2 AA minimum (criterion 2.5.8), which permits small targets only with sufficient spacing. Treat 24×24px as a hard floor for dense pointer-driven (mouse/trackpad) UIs, never as a target to design touch controls to.

*Focus:*
- All interactive elements must have a **visible focus indicator** (2.4.7)
- Focused elements must not be entirely hidden behind sticky headers, banners, or overlays (2.4.11)

*Text and layout:*
- Text must remain readable when resized to **200%** without loss of content or function (1.4.4)
- Content must reflow at **320px viewport width** without requiring horizontal scrolling (1.4.10)
- Text spacing must be overridable: line height ≥ 1.5×, letter spacing ≥ 0.12em, word spacing ≥ 0.16em, paragraph spacing ≥ 2× font size — without loss of content (1.4.12)

*Structure and labelling:*
- Visible label text must match or be contained in the accessible name of its control (2.5.3)
- All non-text content (images, icons used as controls) must have a text alternative (1.1.1)
- Information conveyed by visual structure (headings, lists, tables) must be expressed in semantics (1.3.1)

**Conditional AAA requirements — include only if user selects AAA at Q14:**

*Enhanced contrast:*
- Normal text: minimum **7:1** contrast ratio (1.4.6)
- Large text: minimum **4.5:1** (1.4.6)

*Target size:*
- Minimum **44×44px** for all interactive targets (2.5.5 — AAA in WCAG 2.2). On Android, Material's **48×48dp** touch recommendation still applies as the practical floor.

*Focus:*
- Focused element must not be hidden even partially by sticky content (2.4.12 enhanced)

*Text presentation:*
- No images of text except for decorative or essential cases (1.4.9)
- Users can adjust foreground/background color, font, size, line spacing, and text alignment for blocks of text (1.4.8)

**How to apply:**
When generating the prompt, populate the Accessibility constraint row with the specific requirements above, not a vague reference to "WCAG AA". The AI tool receiving the prompt must be given enough detail to make compliant decisions without needing to look up the standard itself.

### CTA Hierarchy

Apply this logic to every generated prompt. Screens with unclear or competing calls to action produce hesitation and friction — users slow down when they can't immediately identify the right next step. The goal is self-evident UI: the correct action should be obvious without helper text, tooltips, or walkthroughs explaining it.

**Standing rules — always apply when generating:**

*One primary CTA per screen or view.*
Every screen must have exactly one primary action — the thing the user is most likely to want to do next. If the user lists multiple "primary" actions, that is a design problem to resolve before generating, not a layout problem to solve with visual treatment.

*Clear visual hierarchy across CTA tiers.*
Define and enforce a strict visual weight order:
- **Primary** — highest visual weight: filled button in `--color-primary`, full-width on mobile or prominent fixed width on desktop, positioned at the natural endpoint of the reading flow (bottom of content on mobile, inline with form on desktop).
- **Secondary** — medium weight: outlined or ghost button, or a tonal button in a lighter surface color. Clearly subordinate to primary — never the same weight or size.
- **Tertiary** — lowest weight: text link or icon button, used for escape hatches (cancel, skip, go back), supplemental navigation, or low-commitment options.
- **Destructive** — styled distinctly using `--color-error` or equivalent; always secondary or tertiary in weight, never primary unless the screen's sole purpose is confirming a destructive action.

*Self-evident design over compensatory affordances.*
If a user mentions helper text, tooltips, product tours, walkthroughs, or onboarding overlays to explain what a CTA does, treat this as a signal that the CTA itself is unclear. Push back and ask whether the label, context, or layout can be improved instead. Helper text is acceptable for form field guidance (e.g., password requirements); it is not acceptable as a substitute for clear action labelling.

*CTA labels must be specific and action-oriented.*
Vague labels ("Continue", "Submit", "OK") should be replaced with labels that name the outcome: "Create account", "Place order", "Send message", "Delete project". The label should answer the question: "If I tap this, what happens?"

*No competing equal-weight CTAs.*
Two filled primary buttons on the same screen is always wrong. Two outlined buttons of identical size and weight on the same screen is almost always wrong. If the user describes a screen with competing actions, ask which one is the primary goal of this screen and subordinate or defer the other.

*Defer low-priority actions.*
If a screen has many possible actions, the prompt should instruct the AI to defer low-priority ones to a secondary surface (overflow menu, settings, contextual menu on long-press) rather than surfacing them all at once.

**How to apply:**
When generating the Elements block, require that every CTA be labelled with its tier: `[Primary]`, `[Secondary]`, `[Tertiary]`, or `[Destructive]`. When generating the Constraints block, include a CTA hierarchy rule that summarises the tier assignments and prohibits equal-weight competing actions.

### Casing & Capitalization

Apply this logic to every generated prompt. Inconsistent capitalization is one of the most common polish failures in AI-generated UI — it reads as careless and undermines an otherwise on-spec screen. The default is **sentence case**; deviations are deliberate and narrow.

**Standing rules — always apply when generating:**

*Sentence case is the default for all UI text.*
Buttons, labels, headings, navigation, menu items, form-field labels, tab labels, tooltips, empty states, and section titles use sentence case — capitalise only the first word and proper nouns ("Create account", "Account settings", "No results found"). This is the modern design-system standard (Material 3, Shopify Polaris, Atlassian, GitHub Primer) and the most reliable for localisation: many languages have their own capitalisation rules, and forcing Title Case breaks them. This reinforces the CTA rule — outcome-oriented labels are written in sentence case ("Create account", not "Create Account").

*ALL CAPS is reserved for short overline / eyebrow labels and small section dividers only.*
When uppercase is used (e.g., a category eyebrow, a small all-caps section label), it must be applied via CSS `text-transform: uppercase` with appropriate letter-spacing — never by hardcoding capital letters in the string. This keeps the accessible name and the translation source in sentence case, so screen readers don't spell words out letter-by-letter and translators receive natural-case text. Freeform ALL CAPS for body copy, button labels, or headings is a design smell: it reduces legibility, harms users with dyslexia, and is read inconsistently by assistive tech. Flag it.

*all-lowercase is a deliberate brand choice, never a default.*
If a brand uses all-lowercase styling, apply it via CSS `text-transform: lowercase` (not hardcoded), preserve natural case in the accessible name and source string, and confirm it is intentional rather than inferred.

*Title Case is permitted only when the user's brand or design system specifies it.*
If selected, apply it consistently to the chosen element classes (commonly headings and buttons) and never mix it with sentence case for the same element class on the same screen.

**How to apply:**
When generating the Constraints block, include a Casing row stating the active convention (sentence case by default) and the ALL CAPS / lowercase policy. When generating Elements, write all literal label and heading text in the active convention. Where uppercase or lowercase styling is wanted, express it as a styling instruction (`text-transform`), not as hardcoded letter case in the label text.

### Motion & Animation

Apply when the user specifies any transition, animation, or movement-based state change. Motion is a Behavior concern: when present, it must use motion tokens, follow consistent timing, and respect the user's reduced-motion preference. When a screen has no non-essential motion, omit motion guidance entirely — do not invent animation.

**Motion tokens — reference these, never raw durations or easings:**

*Duration* (CSS `--duration-*`; DESIGN.md `motion.duration.*` only if the file defines a motion tier):
- `--duration-fast` ≈ 150ms — micro-interactions: hover, press, small fades, icon state changes
- `--duration-base` ≈ 250ms — standard transitions: dropdowns, toggles, tabs, accordions
- `--duration-slow` ≈ 400ms — large surfaces: modals, bottom sheets, page/route transitions

*Easing* (CSS `--easing-*`):
- `--easing-standard` `cubic-bezier(0.4, 0, 0.2, 1)` — most transitions, elements moving within the screen
- `--easing-decelerate` `cubic-bezier(0, 0, 0.2, 1)` — elements entering the screen (ease-out)
- `--easing-accelerate` `cubic-bezier(0.4, 0, 1, 1)` — elements leaving the screen (ease-in)

Raw motion values are translated to the nearest token via the Raw Value Translation mapping (durations by closest milliseconds; easings and cubic-beziers by curve role — entering → decelerate, exiting → accelerate, in-place → standard). Translate, don't discard.

**Reduced motion — always required when motion is present:**
Every prompt that specifies non-essential motion must instruct the AI to respect `prefers-reduced-motion: reduce` — reduce or remove transform/parallax/auto-playing animation, replacing it with an instant change or a minimal opacity fade. Essential motion (e.g., a loading spinner that communicates state) may remain but should be calmed. This is a baseline accessibility expectation (WCAG 2.3.3) and protects users with vestibular sensitivities.

**How to apply:**
When behaviors involve motion, reference the duration/easing tokens in the Behavior block (e.g., "slide up over `--duration-slow` with `--easing-decelerate`"), include a Motion row in the Constraints block naming the tokens in use and the reduced-motion rule, and add the reduced-motion acceptance criterion. When the screen has no non-essential motion, omit the Motion row, the Behavior motion detail, and the motion AC item.

### L10n / I18n Requirements

Apply this logic when the user confirms localisation is required at Q15. When it is not required, omit the Localisation section from the generated prompt entirely.

**Why localisation affects layout and not just content:**
Translated strings are not the same length as their English source. Layouts that work for English will clip, truncate, or overflow in other languages if containers are sized to the English string. Font stacks that work for Latin scripts silently fail for Arabic, CJK, or Devanagari. RTL languages flip the entire spatial logic of the screen, not just text alignment. These are design constraints, not engineering afterthoughts, and they must be specified in the prompt so the AI generates layouts that accommodate them.

**String length expansion — design for the longest translation:**

| Language group | Typical expansion vs English |
|---|---|
| German | +30–35% |
| French, Spanish, Italian, Portuguese | +20–30% |
| Russian, Polish, Finnish, Hungarian | +20–40% |
| Japanese, Chinese (CJK) | −10–20% (shorter, but taller line heights often needed) |
| Korean | −5–10% |
| Arabic, Hebrew | Variable; RTL layout required regardless of length |

*Design rule:* UI text containers — buttons, labels, nav items, badges, tooltips — must flex to accommodate strings up to **40% longer** than the English source unless a specific shorter-expanding language set is confirmed. Fixed-width text containers are a localisation risk; flag any that appear in the Elements block.

*Pseudo-localisation:* When no translations are available yet, the prompt should instruct the AI to use pseudo-localised strings (e.g., `[Çréàté àccöûnt]`) to simulate expansion and catch overflow early.

**Text directionality:**

| Direction | Languages |
|---|---|
| LTR (left-to-right) | English, most European languages, CJK |
| RTL (right-to-left) | Arabic (ar), Hebrew (he), Persian/Farsi (fa), Urdu (ur) |
| Mixed (bidi) | Content that mixes LTR and RTL — requires Unicode bidi algorithm support |

*Design rule:* If any RTL language is in scope, the layout must be mirrored — not just text-align: right, but full spatial reversal: navigation icons flip, back-chevrons flip, reading flow reverses, padding/margin logic swaps. The prompt must specify this explicitly.

**Number, date, currency, and unit formats — use format tokens, not hardcoded strings:**
- Numbers: `1,234.56` (en-US) vs `1.234,56` (de-DE) vs `1 234,56` (fr-FR)
- Dates: `MM/DD/YYYY` (en-US) vs `DD.MM.YYYY` (de-DE) vs `YYYY年MM月DD日` (ja-JP)
- Currency: symbol position, spacing, and decimal convention vary by locale
- Units: metric vs imperial; temperature (°C / °F); time (12h / 24h)

*Design rule:* All numbers, dates, currencies, and units displayed in the UI must be rendered via localisation format tokens (e.g., `Intl.NumberFormat`, `Intl.DateTimeFormat`, or equivalent i18n library calls), never as hardcoded formatted strings.

**Typography and font stacks for non-Latin scripts:**
Non-Latin scripts require explicit font fallbacks in the type stack. Prompts must specify font fallback behaviour when non-Latin locales are in scope:
- **Arabic / Hebrew / Persian:** Noto Sans Arabic, Noto Naskh Arabic, or system Arabic font
- **CJK (Chinese, Japanese, Korean):** Noto Sans CJK, Source Han Sans, or system CJK font
- **Devanagari (Hindi, Marathi):** Noto Sans Devanagari or system Devanagari font
- **Thai:** Noto Sans Thai or system Thai font

*Design rule:* The font token (`--font-family-base` or `typography.body.fontFamily`) must include a fallback stack that covers all script systems in scope.

**Pluralisation:**
English has two plural forms (singular / plural). Other languages have more:
- Arabic: 6 plural forms
- Russian/Polish: 3 plural forms
- CJK languages: typically 1 form (no grammatical plural)

*Design rule:* Any UI string that varies by count ("1 item", "2 items") must be flagged as requiring pluralisation token support. The prompt should note this so the AI doesn't hardcode singular/plural as a simple branch.

**How to apply:**
When localisation is required, include a `## Localisation & I18n` section in the generated prompt specifying: target languages/locales, text directionality, string expansion headroom, format token requirements, font fallback stack, and any pluralisation needs. Include the L10n / I18n status in the prompt header metadata.

### Question Sequence

Ask these in order. The question text below is a guide — adapt the wording naturally to the conversation; don't read it verbatim.

1. **Platform** — Which tool will receive this prompt? *(Name the target platform — AI code generator, design-to-code tool, UI prototyping tool, or other. Don't assume — the platform determines prompt conventions, Make Kit handling, guidelines file syntax, and Figma MCP routing.)* *Prompt-drift note: if the named platform charges per generation run, note once — without preamble — that a tighter prompt reduces costly reruns. Do this immediately after Q1 is answered, before asking Q2.* *Authority file notice: if the platform is Figma Make, Google Stitch, or Claude Code + Figma MCP, deliver the appropriate notice below immediately after Q1 is answered — before asking Q1a or Q2 — regardless of whether the prompt-drift note also fired.*

   **If Platform = Figma Make**, deliver this notice before Q2:
   > **Make Kit:** Your project targets Figma Make, so your Make Kit is the authority for all design decisions in this session — I won't ask for raw styling values; those come from the Make Kit. Before running the generated prompt, ensure your Make Kit is attached to the project and contains your complete component library, styling tokens, and usage instructions. A Make Kit with missing or incomplete content will produce gaps in the output.

   **If Platform = Google Stitch**, deliver this notice before Q2:
   > **DESIGN.md:** Your project targets Google Stitch, so your DESIGN.md is the authoritative token source for this session — I won't ask for raw styling values; those come from DESIGN.md. Before running the generated prompt, ensure your DESIGN.md is present at the project root and contains complete token definitions across primitive, semantic, and component tiers. Missing token definitions will produce gaps in the output.

   **If Platform = Claude Code + Figma MCP**, deliver this notice before the direction question:
   > **Figma MCP:** The Figma MCP server is bidirectional — it can either **create screens directly in Figma Design** (`use_figma`) or **read an existing Figma frame as a reference for building code** in your project (`get_design_context` / `get_screenshot`). These are different jobs and produce very different prompts, so I need to know which one you want before continuing. Because this skill only generates a text prompt, **you** will run the result in your own Claude Code session.

   **Q1-Direction — Build target (Claude Code + Figma MCP only).** Are you (a) **creating the screen directly in Figma Design** via `use_figma`, or (b) **building code in your project**, using a Figma frame as the design reference? *(Fire this immediately after the notice, before any other follow-up. This choice decides the entire output: (a) produces `use_figma` instructions that write to a Figma file; (b) produces code-generation instructions that read from Figma and write to your tech stack. If the user is unsure, ask whether the end artifact is a Figma design or working code in their repo — the answer disambiguates.)*

   **If Q1-Direction = (a) build in Figma Design**, deliver this readiness notice, then ask Q1a/Q1b/Q1c:
   > Before **you** run the generated prompt, ensure: (1) the Figma MCP server is connected and authenticated in Claude Code, (2) the `figma-use` skill is loaded — it is a **mandatory prerequisite** before any `use_figma` tool call, (3) you have edit access to the target Figma file, (4) **if you plan to use Variables that live in a separate Component Library**, those Variables must be *published* in the library and the library must be *enabled* in the target file (open the target file's Variables panel and confirm the library's Collections appear). Without this, `use_figma` cannot bind to library Variables. The generated prompt will include explicit `use_figma` instructions, the target file/page URL, and any Component Library and Variable Collection/Group references you specify.

   *Build-in-Figma follow-ups (Q1a/Q1b/Q1c). These fire only when Q1 = Claude Code + Figma MCP AND Q1-Direction = (a), in order, one per turn. On the (a) branch, skip the stack questions (Q1d–Q1f) — the screen is assembled in Figma Design, not generated as code:*

   - **Q1a — Figma target.** Paste the URL of the Figma Design file or page where the screen should be created. *(Required. The URL must point to a file you have edit access to. A page-level URL — `?node-id=...` — is preferred over a bare file URL so the screen lands in the right place.)*
   - **Q1b — Component Library.** Are you using a Figma Component Library? If yes, paste the URL of the library file. *(If yes, the generated prompt will instruct the AI to import components from this library rather than building from primitives. Component Libraries can also define their own Variables — the prompt will include an instruction to inspect the library for Variable Collections and use them where applicable.)*
   - **Q1c — Variables.** Does the target Figma Design file or the Component Library include a Variable system to use? If yes, please specify all four points below in your reply — they're one question about Variables, not four turns:

       1. **Collection(s) and Group(s) to use** — name the Collection(s) and the Group(s) within them, and indicate whether they live in the target file, the Component Library, or both.
       2. **Exclusions** *(optional)* — any Collection or Group in scope that should *not* be used on this screen (e.g., you have a "Density" Collection but this screen doesn't need density switching).
       3. **Modes** — will this screen support mode switching (e.g., Light/Dark, Density, Brand)? If yes, name the modes per Collection and identify the default mode for this screen. *Best practice: if the design will use modes for light and dark themes, bindings must be **mode-aware** — reference the Collection rather than a specific mode value — so the screen switches correctly when the user changes mode. Hardcoding a specific mode's value defeats the Variable system.*
       4. **Tier preference** *(optional)* — primitive, semantic, or component-tier Variables? *Best practice is to bind to **semantic-tier Variables** (e.g., `color/text/primary`, `color/surface/raised`) rather than primitive Variables (e.g., `color/gray/900`, `color/blue/500`). Semantic Variables preserve design intent, adapt correctly across modes, and survive primitive re-mapping. If you don't specify, semantic is assumed.*

       *(Figma Variables are the token authority for this scenario. If neither this file nor the Component Library provides Variables, Q7 will handle the styling source.)*

   **If Q1-Direction = (b) build code from a Figma reference**, deliver this readiness notice, ask Q1a-ref, then proceed to the stack questions (Q1d–Q1f):
   > Before **you** run the generated prompt, ensure: (1) the Figma MCP server is connected and authenticated in Claude Code, (2) you have at least read access to the source Figma file. The generated prompt will instruct the AI to read the Figma frame (`get_design_context` / `get_screenshot`) as the design reference and generate code in your stack — it will **not** create or modify anything in Figma. The `figma-use` skill and `use_figma` are not used on this branch.

   - **Q1a-ref — Figma source.** Paste the URL of the Figma Design file or frame to use as the design reference. *(Read-only reference. The screen is built as code in your project, not written back to Figma. A frame-level URL — `?node-id=...` — is preferred so the AI reads the right node.)*

   *Tech stack follow-ups (Q1d/Q1e/Q1f). These fire one per turn for any code-generation platform — including Claude Code + Figma MCP on the (b) build-code branch — **except** Figma Make and Google Stitch, whose stacks are governed by their authority files (Make Kit / DESIGN.md). Do not ask these for Figma Make, for Google Stitch, or on the (a) build-in-Figma branch. The answers determine token-naming convention, which component patterns the prompt may reference, and whether a developer call-out is required. If the user does not know an answer, record it as unspecified, advance, and let the no-assumed-stack rule produce a developer call-out — never fill the gap with an assumed framework, styling system, or component library.*

   - **Q1d — Framework / target.** What framework or platform will run this code? *(e.g., React, Vue, Svelte, Angular, SolidJS, plain HTML/CSS, WordPress theme/block (Gutenberg), Astro, Next.js, SwiftUI, Jetpack Compose, Flutter. This sets the language and component idiom the prompt is written for. If unsure, say so — I won't assume one.)*
   - **Q1e — Styling system.** How is styling applied in this project? *(e.g., Tailwind, CSS Modules, vanilla CSS/SCSS, styled-components or Emotion, CSS-in-JS, WordPress theme.json / global styles, SwiftUI modifiers. This decides the token-naming convention used throughout the prompt — Tailwind utility names, CSS custom properties, etc. If unsure, I'll use generic CSS-variable tokens and flag the stack for you to confirm.)*
   - **Q1f — Component library.** Are you using a component library or UI kit? *(e.g., shadcn/ui, Radix, MUI, Chakra, Mantine, Ant Design, Headless UI, native platform components, or none. If yes, the prompt will reference its components; if none, the prompt builds from primitives. If unsure, I'll leave components framework-neutral and flag it.)*
2. **UI Type** — What kind of interface are you building? *(A screen, component, flow, modal, widget — or something else?)*
3. **Product & User** — What kind of app or product is this for, and who is the user?
4. **User Moment** — What just happened before the user arrives at this screen, and what do they do next?
5. **Required Elements** — What specific UI components, content, or features must be present? *(List everything required — components, content, imagery, labels. Don't evaluate priority yet; just get everything on the table.)*
5a. **Primary Action** — Of everything listed, which is the single most important action a user should take on this screen? *(This becomes the [Primary] CTA. If they name more than one, note that only one can hold the primary position and ask them to choose. If none is clear, ask what success looks like — what has the user accomplished when they leave this screen?)*
6. **Behaviors** — How should the UI respond to interaction? *(States, transitions, conditional logic, animations?)*
7. **Design Tokens / Guidelines File** — Do you have a **design token source or guidelines file** to reference for token *values*? *(Component library and styling system are already captured at Q1d–Q1f — this question is only about a file that defines token values or design rules. Options — name whichever applies:)*
   - A **named design system with a published token spec** (Material 3, a company design system, etc.) whose token names the prompt should follow
   - A **DESIGN.md file** in your project *(Google Stitch's open-source guidelines format — contains YAML token front matter and human-readable design rationale. If present, token values for primitive, semantic, and component tiers can be referenced directly from it rather than entered manually.)*
   - A **design token file** (e.g., a `tokens.json` / Style Dictionary / W3C design-tokens export)
   - A **platform-specific guidelines file** (e.g., Cursor's `.cursorrules`, Claude Code's `CLAUDE.md`, or any other agent instructions file that includes design rules)
   - **None** — tokens will be specified manually or left to AI discretion

   **Asset-availability confirmation — required whenever the user names any file or external token source at Q7 (or names Figma Variables/Library at Q1b/Q1c).** Before treating a `DESIGN.md`, `tokens.json`, guidelines file, or Figma Variable/Library as authoritative, confirm it actually exists and will be present when the prompt runs: ask "Is that file already in the project (or will it be) when you run this prompt?" — present-and-ready, or planned-but-not-yet. If the user confirms it is present, reference it normally. If it is planned, unsure, or not yet created, **do not reference it as if it exists** — instead carry it as a readiness call-out in the generated prompt ("⚠️ This prompt assumes `DESIGN.md` at the project root; create it before running or the AI will fall back to its own token choices"). **Never invent a style asset the user did not name** — if no token file or guidelines file was named, the prompt must not reference a `DESIGN.md`, `tokens.json`, or any other file.

   *Claude Code + Figma MCP scope rule:* If Q1 = Claude Code + Figma MCP and Q1b/Q1c established a Component Library and/or Variables, narrow Q7 to anything not covered by them. If both Component Library and Variables are confirmed, Q7 may be skipped unless the user wants to add a supplemental named system or guidelines file. If only one was confirmed, ask Q7 only about the gap (e.g., Variables present but no library → ask about component sources; library present but no Variables → ask about a token authority such as a DESIGN.md, design token file, or named system). If neither was confirmed, ask Q7 in its standard form — the user may name a design token file, DESIGN.md, named system, or "None" to fall through to full manual styling intake at Q9.

### Authority File Gate

Before asking Q9, evaluate the platform confirmed at Q1 and the design system or guidelines file confirmed at Q7 to determine the authority file status for this session.

| Authority file status | Confirmed in | Scope for Q9 (Constraints) |
|---|---|---|
| **Make Kit** | Q1 — Platform: Figma Make | Platform and behavioral constraints only. No color, typography, spacing, or radius questions. |
| **DESIGN.md** | Q1 — Platform: Google Stitch | Platform constraints and semantic/component token overrides only. No primitive values. |
| **Figma Component Library** | Q1 — Platform: Claude Code + Figma MCP; Q1b — library link confirmed | Suppress component intake (defer to library). Styling intake still applies unless Variables are also present. |
| **Figma Variables** | Q1 — Platform: Claude Code + Figma MCP; Q1c — Variable Collection(s)/Group(s) confirmed (in target file, library, or both) | Suppress raw styling intake for any category covered by Variables. Component intake still applies unless the Component Library is also present. |
| **Named design system** | Q7 — named system confirmed | Platform constraints and token-convention overrides in that system's naming. No raw values. |
| **None** | Q1 — other platform with Q7 = no system; OR Claude Code + Figma MCP with Q1b = "none" AND Q1c = "none" AND Q7 = no system | Full styling intake. Ask for all constraints as before. |

Multiple authorities can be active simultaneously — for example, a Claude Code + Figma MCP session may have both a Component Library (Q1b) and Variables (Q1c). Treat each as authoritative for its own category; do not override one with the other.

Use the corresponding preamble before Q9:

**If Make Kit is active:**
> The Make Kit is the source of truth for all components, styles, and tokens in this project. I won't ask for color, typography, spacing, or radius values — those are governed by the Make Kit. For Q9, tell me only about platform requirements (iOS / Android / Web), safe area handling, and any behavioral rules not covered by the Make Kit.

**If DESIGN.md is active:**
> DESIGN.md is the authoritative token source for this project — primitive, semantic, and component tiers. I won't ask for raw color or spacing values. For Q9, tell me about platform requirements and any semantic or component-level token overrides you want to specify explicitly.

**If Figma Component Library and Variables are both active:**
> Your Component Library is the source of truth for components and your Variables are the token authority for styling. I won't ask for raw styling values or component substitutions — those defer to the library and Variables. For Q9, tell me only about platform requirements (iOS / Android / Web / Responsive) and any explicit behavioral overrides not covered by either.

**If only Figma Component Library is active (no Variables):**
> Your Component Library is the source of truth for components. Styling values are not yet governed by an authority — I'll still ask about colors, typography, spacing, and radius at Q9, and translate raw values to token names as we go. For Q9, tell me about platform requirements, styling constraints, and any component-level overrides beyond the library.

**If only Figma Variables are active (no Component Library):**
> Your Variables are the token authority for any styling category they cover. I won't ask for raw styling values where Variables apply. Components are not yet governed by an authority — I'll ask about component choices and patterns at Q9. For Q9, tell me about platform requirements, component-level decisions, and any token overrides beyond your Variables.

**If a named design system is active:**
> [System name] governs your token names and conventions. I won't ask for raw values — any constraints you specify should use that system's token naming. For Q9, tell me about platform requirements and any overrides you need beyond what the system provides.

**If no authority file (none):**
> No design system or guidelines file is set for this project — the AI will work from the constraints you specify here. For Q9, tell me about platform requirements, grid, spacing, color, typography, radius, and any brand rules to lock in. Raw values will be translated to tokens as you enter them.

9. **Constraints** — *(Scope depends on authority file status — see the preamble above.)*
   - **Make Kit active:** Platform (iOS / Android / Web), safe area insets, and behavioral overrides not covered by the Make Kit. Do not specify color, typography, spacing, or radius — those are governed by the Make Kit.
   - **DESIGN.md active:** Platform (iOS / Android / Web) and any semantic or component-level token overrides. Do not specify primitive values — those are governed by the DESIGN.md.
   - **Figma Component Library and Variables both active:** Platform (iOS / Android / Web / Responsive) and behavioral overrides. Do not specify components or raw styling values — those are governed by the library and Variables.
   - **Only Figma Component Library active:** Platform, behavioral overrides, and styling constraints (color, typography, spacing, radius) using token names. Do not duplicate components — those defer to the library.
   - **Only Figma Variables active:** Platform, behavioral overrides, and component-level decisions. Do not specify raw styling values for categories covered by Variables — reference `{group/variable-name}` instead.
   - **Named system active:** Platform (iOS / Android / Web) and token-convention overrides using that system's naming. Do not specify raw values.
   - **None:** Any rules to lock in — platform (iOS / Android / Web), grid, spacing, accessibility needs, brand rules, color, typography, radius.
10. **Reference Images** — Do you have any screenshots, existing screens, or inspiration images to attach alongside the prompt?
11. **Scope** — Are you targeting a single component, one screen, or a multi-screen flow?
12. **Prototype Type** — Are you building a functional prototype (real interactions, logic, and data) or a design mockup (visual fidelity and click-through flows)?
13. **Acceptance Criteria** — How will you know the output succeeded? Are there specific layout, behavior, or functional requirements you'd use as a pass/fail checklist? *(If they're unsure, offer to derive AC from the Elements, Behavior, and Constraints they've already described.)*
14. **Accessibility Level** *(mandatory — cannot be skipped)* — The prompt will target **WCAG 2.2 AA** by default. Do you need **AAA** compliance instead? *(AA: 4.5:1 contrast for normal text, 3:1 for large text and UI components, touch/pointer targets ≥ 44×44px (iOS) / ≥ 48×48px (Android) recommended with 24×24px the absolute WCAG AA floor, visible focus indicators, reflow at 320px. AAA adds 7:1 contrast, mandatory 44×44px targets, and stricter text presentation rules. Most production products target AA; AAA is typically required for government, healthcare, or high-compliance contexts.)*
15. **Localisation (L10n / I18n)** *(mandatory — cannot be skipped)* — Does this UI need to support multiple languages or locales? *(All products have a localisation posture, even if the answer is "English only for now." If in scope: which languages or locales? This determines string expansion headroom, text directionality for RTL languages, date/number/currency format tokens, and font fallback stacks for non-Latin scripts.)*
16. **Casing convention** — UI text will default to **sentence case** (capitalise the first word and proper nouns only — "Create account", "Account settings"). Do you want a different convention? *(Sentence case is the modern standard and the safest for localisation. Title Case is available if your brand requires it — name the element classes it applies to. ALL CAPS is reserved for short overline/eyebrow labels and applied via `text-transform`, never hardcoded; all-lowercase is treated as a deliberate brand choice only. Once set, this is recalled across sessions and not re-asked.)*

After the final question, deliver a structured confirmation summary before moving to Phase 2. Use this format exactly — fill in the user's answers, mark anything unspecified:

> **Input summary — confirm before proceeding:**
> - Platform: [answer]
> - Figma MCP direction: [build in Figma Design (a) / build code from Figma reference (b) — Claude Code + Figma MCP only; omit row otherwise]
> - Tech stack — framework: [answer from Q1d — or "unspecified — developer call-out"; omit row for Figma Make, Google Stitch, and the build-in-Figma branch]
> - Tech stack — styling system: [answer from Q1e — or "unspecified — generic CSS-variable tokens + call-out"; omit row for Figma Make, Google Stitch, and the build-in-Figma branch]
> - Tech stack — component library: [answer from Q1f — or "none" / "unspecified — call-out"; omit row for Figma Make, Google Stitch, and the build-in-Figma branch]
> - Figma target file/page: [URL from Q1a — build-in-Figma (a) branch only; omit row otherwise]
> - Figma source reference: [URL from Q1a-ref — build-code-from-Figma (b) branch only; omit row otherwise]
> - Component Library: [URL from Q1b — Claude Code + Figma MCP only; "none" if not provided; omit row otherwise]
> - Variables: [Collection(s)/Group(s) from Q1c — Claude Code + Figma MCP only; "none" if not provided; omit row otherwise]
> - Variable modes: [modes per Collection + default mode for this screen, from Q1c.3 — Claude Code + Figma MCP only; "none" if no modes; omit row if Variables = none]
> - Variable tier preference: [primitive / semantic / component, from Q1c.4 — Claude Code + Figma MCP only; "semantic (default)" if unspecified; omit row if Variables = none]
> - Variable exclusions: [Collections/Groups to avoid, from Q1c.2 — Claude Code + Figma MCP only; "none" if not provided; omit row if Variables = none]
> - UI type: [answer]
> - Primary CTA: [answer from Q5a — or "not yet designated"]
> - Design token / guidelines file: [answer — or "none"; if named, note availability: present-and-ready / planned-call-out]
> - Authority file status: [Make Kit / DESIGN.md / named system: name / none — for Claude Code + Figma MCP, list separately each of {Figma Component Library, Figma Variables} that is active, or "none" if neither]
> - WCAG level: [AA / AAA]
> - L10n / I18n: [not required / required — languages: list]
> - Casing: [sentence case (default) / Title Case for [element classes] / other — plus ALL CAPS policy]
> - Prototype type: [functional / mockup]
> - Scope: [component / screen / flow]
>
> Anything to correct before Phase 2?

**Correction handling:** If the user corrects any item, apply the correction, restate only the corrected line ("Updated: WCAG level → AAA"), and ask if anything else needs adjusting before proceeding. Do not re-run the full summary. Move to Phase 2 only when the user confirms the summary is accurate — or provides no corrections.

---

## Phase 2: Clarify and Analyze

After gathering inputs, review them before generating. Work through the flags below in order — each step cleans up inputs that later steps depend on. Ask all clarifying questions in a single message at the end, then confirm with the user before proceeding.

### Scope Flags
- Too broad: "design the whole app" → prompt for one screen at a time
- Too narrow: single button with no surrounding context → suggest minimal screen frame

### Ambiguity Flags
- Vague adjectives: "modern", "clean", "professional" → ask for specifics (token-referenced values like `--radius-md` and `--color-surface-muted` beat "clean")
- Missing user context: who is performing the action, and why?
- Underspecified behaviors: "a form" without describing what happens on submit
- Conflicting constraints: dark mode requested but brand colors not adapted

### CTA Flag
Audit gathered Elements, Q5, and Q5a outputs for CTA hierarchy problems. This is a verification pass on Phase 1 inputs — not a fresh intake step. Q5a should have established the primary CTA; this flag catches gaps and conflicts that slipped through.
- **Missing primary CTA:** Verify Q5a was answered and a primary CTA was designated. If not captured, resolve it now before generating: ask what success looks like on this screen and use the answer to designate the primary.
- **Multiple primary CTAs:** If the user described more than one primary or equal-weight action — or if Q5a produced ambiguity — designate one as primary and demote the others to secondary or tertiary.
- **Vague CTA labels:** Audit labels gathered at Q5 against the outcome-oriented standard. If any are generic ("Continue", "Submit", "OK", "Next"), flag and suggest specific outcome-oriented alternatives based on the screen context.
- **Compensatory affordances:** If the user mentioned helper text, tooltips, walkthroughs, or product tours to explain a CTA, flag as a design smell. The label, context, or layout should make the action self-evident. Helper text is appropriate for form field guidance only.
- **Buried or deferred primary action:** If the primary CTA was placed below a large amount of content, behind a scroll, or in a non-prominent position, flag and recommend it appear at the natural endpoint of the reading flow.
- **Destructive CTA as primary:** If a destructive action was designated primary without strong justification, flag and confirm it is intentional.

When generating the Elements block, label every CTA with its tier: `[Primary]`, `[Secondary]`, `[Tertiary]`, or `[Destructive]`. Include a CTA hierarchy summary in the Constraints block.

### Casing Flag
Audit all gathered label, heading, and content text for capitalisation consistency before generating. Default convention is sentence case unless the user set another at Q16.
- **Mixed casing:** If some labels are sentence case and others Title Case for the same element class (e.g., two buttons "Create account" and "Save Changes"), flag and normalise to the active convention.
- **Hardcoded ALL CAPS:** If any label, heading, or button text was supplied in all capitals, flag it. Convert the source text to the active convention and, if uppercase presentation is intended, note that it should be applied via `text-transform: uppercase`, not hardcoded — this preserves the accessible name and translation source.
- **Title Case CTAs:** If CTA labels use Title Case but the active convention is sentence case, restate them in sentence case ("Create account", not "Create Account").
- **Unconfirmed lowercase styling:** If all-lowercase text appears without a stated brand rationale, confirm it is a deliberate brand choice before carrying it into the prompt.

### Stack Flag
Run this before the Raw Value Translation Flag — token-naming convention and component references depend on the resolved stack. This flag applies to every code-generation platform; skip it for Figma Make, Google Stitch, and the Claude Code + Figma MCP build-in-Figma (a) branch, whose stacks are governed elsewhere.
- **Stack captured?** Verify Q1d (framework), Q1e (styling system), and Q1f (component library) were asked and recorded for the platform. If the platform produces code and any of the three was never asked, resolve it now before generating.
- **No assumed stack.** If framework, styling system, or component library is unspecified or the user didn't know, do **not** default to React, Tailwind, or shadcn/ui. Keep the output framework-neutral, use generic CSS-variable tokens, and require a **"Tech stack — to be confirmed by developer"** call-out block in the generated prompt naming each unspecified part. Scan the draft for any stray Tailwind utility classes, shadcn/ui or other library component names, or framework-specific syntax that crept in without being specified — strip them.
- **Styling system drives token naming.** If Q1e = Tailwind, token references use Tailwind utility names; if a named token spec was confirmed at Q7, use its convention; otherwise generic CSS variables. Verify Phase 1 translations match the resolved convention and rename any that don't.
- **Component library drives component references.** If Q1f names a library, the Elements/Constraints may reference its components; if Q1f = none, the prompt builds from primitives and must not name a specific library; if unspecified, components stay generic and the call-out covers it.
- **Figma Make default.** Figma Make is the only platform with a sanctioned implicit stack — React + Tailwind + shadcn/ui + Radix (its real generation stack), governed in practice by the Make Kit and overridable by the user. No stack call-out is needed for Figma Make.
- **Build-code-from-Figma (b) branch.** Verify the generated prompt instructs the AI to **read** the source Figma frame (`get_design_context` / `get_screenshot`) as a reference and generate code in the resolved stack. It must **not** contain `use_figma`, `figma-use`, or any "create the screen in Figma" instruction — those belong only to the (a) branch.

### Raw Value Translation Flag
Review all gathered inputs for any raw values that were not caught or redirected during Phase 1. Check every field — including Elements, Behavior, and Constraints — for stray hex codes, pixel measurements, unitless numbers that represent sizes, or hard-coded weights.

Apply the authority file status established by Q1 (platform), Q1b/Q1c (Figma library/Variables), and Q7 (design system):
- **Make Kit or DESIGN.md active:** Any raw styling values that slipped through — colors, font sizes, spacing, radius, elevation/shadows, motion timing — are conflicts. Flag them explicitly: these values should defer to the authority file, not appear as raw values or translated tokens in the prompt. Remove them before generating.
- **Figma Variables active:** Any raw styling values in a category covered by the named Variable Collection(s)/Group(s) are conflicts. Redirect to `{group/variable-name}` references. For styling categories not covered by Variables, fall back to the Figma Component Library (if it defines Variables), Q7 authority (if any), or standard token translation.
- **Figma Component Library active (without Variables):** Standard Raw Value Translation applies for styling. Verify that no component identities slipped through as ad-hoc descriptions — those should reference the library.
- **Named system active:** Translate any untranslated raw values using that system's token naming conventions. Verify that all translated token names from Phase 1 also follow that system's conventions — rename any that don't match.
- **No authority file:** Translate any untranslated raw values using the closest-match logic from Phase 1. Notify the user of all changes in the Phase 2 summary message and confirm before generating.

### Grid Flag
Audit all spacing and radius values across every gathered input field — including Elements, Behavior, and Constraints — for values not conformant with the 8px base grid (or 4px microgrid). Apply the same rounding and tokenisation logic from Phase 1. Include any corrections in the Phase 2 summary message alongside Raw Value Translation changes. If no grid was specified by the user and none was recommended during Q9, recommend the 8px grid now and apply it to all spacing values before generating.

### Design System Flags
- If a design system exists but wasn't mentioned: prompt the user to reference it explicitly, since referencing library components and tokens dramatically improves output fidelity
- If no design system: note that the AI will make stylistic choices freely; suggest setting at least a color and type constraint to avoid generic output
- **Authority file gate verification:** If Make Kit (Figma Make), DESIGN.md (Google Stitch), or Figma Component Library / Variables (Claude Code + Figma MCP) authority is active, scan the Constraints block for any raw styling values (hex colors, pixel sizes, named font sizes) or translated token names that duplicate values already governed by the authority file. Flag these as conflicts — they will create ambiguity for the AI. Remove or replace with the appropriate authority file reference before generating.
- **If a DESIGN.md is confirmed:** verify that the generated prompt instructs the tool to read the DESIGN.md before generating. Token references in the prompt should use DESIGN.md dot-path syntax (`colors.primary`, `spacing.lg`, `rounded.md`) rather than CSS variable convention. Check that primitive, semantic, and component tiers are all accounted for — a prompt that only references semantic tokens may miss component-level overrides.
- **If a Figma Component Library is confirmed (Q1b):** verify the generated prompt references the library URL explicitly and instructs the AI (via the `use_figma` tool) to import components from the library rather than creating equivalents from primitives. Verify that an instruction to inspect the library for embedded Variable Collections is included.
- **If Figma Variables are confirmed (Q1c):** verify the generated prompt references the named Collection(s) and Group(s) explicitly and uses `{group/variable-name}` references in Constraints, not CSS variable convention or dot-path syntax. Verify the prompt instructs the AI to bind variables via `use_figma` rather than hardcoding values.
- **If another guidelines file is confirmed:** instruct the prompt to reference it by name and location. Note any design rules it contains that should be explicitly restated in Constraints for tools that don't automatically read such files.

### Figma MCP Flag *(Claude Code + Figma MCP only)*
- Verify the generated prompt includes the **mandatory `figma-use` skill prerequisite** before any `use_figma` tool call, and that the `use_figma` instruction itself is present and unambiguous.
- Verify the **target Figma file/page URL** captured at Q1a is referenced explicitly in the output. Without it, the AI has no destination.
- Verify the **Component Library URL** (Q1b) and **Variable Collection(s)/Group(s)** (Q1c) are referenced where applicable. If either was provided but is missing from the generated prompt, that is a generation gap — restore before delivering.
- Verify the **Variable details** captured at Q1c are all carried into the output template's Figma MCP block: modes per Collection + default mode (Q1c.3), tier preference with semantic as default (Q1c.4), and exclusions (Q1c.2). Each gets its own line in the block plus a matching instruction bullet.
- Verify the **library Variable subscription** readiness item is present in the output's Figma MCP block when Q1b includes Variables (or the user indicated library Variables are in scope). Library-published Variables must be enabled in the target file or `use_figma` cannot bind them.
- Verify the prompt instructs the AI to assemble the screen **incrementally section-by-section** rather than as a single monolithic `use_figma` call — this matches the figma-generate-design skill's guidance and produces better outcomes.
- *(Direction (a) only)* Verify the prompt includes the **Make-ready construction rules** so the resulting Figma file can be reused downstream as input for Figma Make or another vibe-coded target: nested auto-layout with no Groups; Fill/Hug + Min/Max sizing; higher-order library instances that are never detached; semantic, BEM-like layer names matched to codebase component names where known; Variable-based spacing; clean layers (no ghost or 0%-opacity layers, no frame-wrapped single text layers); and native Slots where the library uses them or the screen benefits. A canvas design a downstream tool can't ingest is a generation gap — add the rules before delivering.
- If the user has indicated their MCP server isn't yet connected, the `figma-use` skill isn't loaded, they lack edit access to the target file, or library Variables aren't subscribed in the target file: proceed with the generated prompt as written and include the readiness warning — the user is responsible for resolving these before running.
- Do not silently substitute CSS variable convention or DESIGN.md dot-path syntax for `{group/variable-name}` Variable references. Figma Variables are the native token authority for this scenario and must be referenced as Figma resolves them.
- Do not silently override the user's mode, tier, or exclusion preferences. If the user specified primitive-tier or named a default mode different from the system default, surface that in the output and bind accordingly.

### Make Kit Flag *(Figma Make only)*
- Make Kit authority is established by platform choice (Q1 = Figma Make). Verify the generated prompt includes the Make Kit instruction block and that no raw styling values or primitive token names appear in the Constraints block — all styling must defer to the Make Kit.
- The Make Kit is the single source of truth for all components, styling values, and usage rules. The generated prompt must instruct Figma Make to use the Make Kit exclusively and never override its explicit instructions.
- If the user has indicated their Make Kit is not yet set up: proceed with the generated prompt as written and include the readiness warning — the user is responsible for configuring the Make Kit before running.

### DESIGN.md Flag *(Google Stitch only)*
- DESIGN.md authority is established by platform choice (Q1 = Google Stitch). Verify the generated prompt includes the Design Guidelines instruction block referencing DESIGN.md and that no primitive or raw values appear in the Constraints block.
- The DESIGN.md is the single source of truth for all token values — primitive, semantic, and component tiers. The generated prompt must instruct the AI to read the DESIGN.md before generating and to use its dot-path token references (`{colors.primary}`, `{spacing.lg}`, `{rounded.md}`) throughout. Token values in the prompt must not override values defined in the file.
- If the user has indicated their DESIGN.md is not yet set up: proceed with the generated prompt as written and include the readiness warning — the user is responsible for configuring the DESIGN.md before running.

### Accessibility Flag
Every prompt defaults to WCAG 2.2 AA. Review gathered inputs for any conflicts before generating:
- **Color conflicts:** If the user specified brand colors, flag any foreground/background pairings that are likely to fail the applicable contrast threshold (4.5:1 for normal text, 3:1 for large text and UI components). Don't calculate exact ratios — flag obvious risks (light grey text on white, light yellow on white, low-saturation combinations) and note that contrast must be verified with a tool such as the WebAIM Contrast Checker.
- **Target size conflicts:** Flag any touch/pointer target described below the recommended **44×44px (iOS) / 48×48px (Android)**, and recommend sizing up. Anything below the **24×24px** WCAG 2.2 AA floor (or 44×44px if AAA is selected) is a hard failure, not just a recommendation — call it out as such.
- **Focus state omission:** If interactive elements were listed without mention of focus states, add a reminder that visible focus indicators are required at AA and must be included in the Behavior block.
- **Missing text alternatives:** If images, icons, or illustrations are listed in Elements without alt text or aria-label guidance, flag the gap.
- **Reflow risk:** If the layout was described with fixed widths or rigid side-by-side columns, flag that content must reflow at 320px viewport width.
- **Motion / reduced-motion:** If behaviors include transitions, parallax, or auto-playing animation, verify the prompt instructs the AI to respect `prefers-reduced-motion` and references motion tokens (`--duration-*`, `--easing-*`) rather than raw durations or easings.

Include the applicable WCAG 2.2 level (AA or AAA) in the Constraints block of the generated prompt with the specific numeric requirements, not just the level name.

### L10n Flag
If localisation is required (confirmed at Q15), audit gathered inputs before generating:
- **Fixed-width text containers:** Any button, label, badge, nav item, or tooltip described with a fixed width is a localisation risk. Flag it and recommend a flex/min-width approach with a max-width cap if needed.
- **Hardcoded formatted strings:** Any date, number, currency, or unit value in the Elements or Behavior blocks must be replaced with a format token reference. Flag any hardcoded examples like "Jan 12, 2026" or "$1,234.56".
- **RTL layout omission:** If Arabic, Hebrew, Persian, or Urdu is in scope and the layout hasn't addressed mirroring, flag this explicitly. RTL is a full spatial reversal — not just text-align: right.
- **Non-Latin font stack gaps:** If CJK, Arabic, Devanagari, or Thai is in scope and the font token has no fallback for those script systems, flag the gap.
- **Pluralisation strings:** Any count-dependent string ("1 result", "3 items") must be flagged as requiring pluralisation token support.
- **Missing pseudo-localisation guidance:** If translations are not yet available, recommend the prompt instruct the AI to use pseudo-localised placeholder strings to surface overflow early.
- **String expansion headroom:** Confirm that text container widths and heights allow for the expansion rate of the widest-expanding language in scope (typically German at ~35%). If the layout is too tight for English, it will break under expansion.

Include the L10n status and target locales in the prompt header and the Localisation & I18n section of the Constraints block.

### Acceptance Criteria Flags
- If the user provided AC: check that every criterion is **binary and verifiable** — it must be possible to look at the output and say yes or no. Flag anything vague ("looks polished," "feels right") and help restate it as a checkable condition.
- If the user skipped AC: derive a starter set from the most critical items in Elements, Behavior, and Constraints. Present these to the user for confirmation before generating.
- **Always include at minimum these AA accessibility AC items**, regardless of whether the user specified them:
  - `- [ ] All text/background colour combinations meet the applicable contrast ratio (4.5:1 normal, 3:1 large text)`
  - `- [ ] All interactive elements have a visible focus indicator`
  - `- [ ] Touch/pointer targets are ≥ 44×44px (iOS) / ≥ 48×48px (Android); none below the 24×24px WCAG 2.2 AA floor [AAA: ≥ 44×44px required]`
  - `- [ ] Content reflows at 320px viewport width without horizontal scroll`
  - `- [ ] All meaningful images and icon controls have text alternatives`
- **Always include these CTA hierarchy AC items**, regardless of whether the user specified them:
  - `- [ ] Exactly one primary CTA is present and visually dominant`
  - `- [ ] No two CTAs have equal visual weight`
  - `- [ ] All CTA labels are outcome-oriented (not generic verbs like "Submit" or "Continue")`
  - `- [ ] No helper text or walkthroughs are used to explain what a CTA does`
- **Always include this casing AC item**, regardless of whether the user specified it:
  - `- [ ] All UI text uses the specified casing convention (sentence case by default); no hardcoded ALL CAPS — uppercase styling applied via text-transform`
- **If behaviors involve transitions or animation, include this motion AC item:**
  - `- [ ] Non-essential motion is reduced or removed under prefers-reduced-motion; transitions reference motion tokens, not raw durations/easings`
- **If L10n / I18n is required, always include these AC items:**
  - `- [ ] All text containers flex to accommodate strings up to [X]% longer than English source`
  - `- [ ] No fixed-width text containers exist for UI labels, button copy, nav items, or badges`
  - `- [ ] All dates, numbers, currencies, and units are rendered via format tokens, not hardcoded strings`
  - `- [ ] RTL layout is fully mirrored for Arabic/Hebrew/Persian locales (if in scope)`
  - `- [ ] Font fallback stack covers all script systems in scope`
  - `- [ ] Count-dependent strings use pluralisation tokens`
- **If Claude Code + Figma MCP with Q1-Direction = (a) build in Figma Design, always include:**
  - `- [ ] Screen is created in the target Figma Design file/page specified at Q1a`
  - `- [ ] Every container uses auto-layout (no Groups); sizing uses Fill/Hug with Min/Max safety rails where needed`
  - `- [ ] Frames, sections, and component instances are named semantically (BEM-like) and matched to codebase component names where known`
  - `- [ ] Components are library instances (not detached); no ghost or 0%-opacity layers remain`
  - `- [ ] The frame is Make-ready — reusable as input for Figma Make or another vibe-coded target`
- **If a Figma Component Library was provided (Q1b), additionally include:**
  - `- [ ] All components that have a Component Library equivalent are instantiated from the library, not built from primitives`
  - `- [ ] Any element with no Component Library equivalent is flagged in the file rather than substituted silently`
- **If Figma Variables were provided (Q1c), additionally include:**
  - `- [ ] All styling properties (fills, strokes, typography, spacing, radius, effects) are bound to the named Variables where a matching Variable exists`
  - `- [ ] No raw color, typography, spacing, or radius values are hardcoded for properties that have a matching Variable`
  - `- [ ] Where Variable Collections include modes (Light/Dark, Density, Brand), bindings reference the Collection (not a specific mode value) so mode switching works correctly`
  - `- [ ] Bindings prefer semantic-tier Variables (e.g., color/text/primary) over primitive-tier Variables (e.g., color/gray/900) wherever both exist for the same role`
  - `- [ ] No bindings reference any Collection or Group listed in the Variable exclusions field`
- **Functional prototype AC** should include interaction checks: does the logic fire correctly, do states transition as specified, does conditional behavior work?
- **Design mockup AC** should focus on visual inspection: are all elements present, do spacing and tokens match the spec, are states visually distinguishable?
- Every AC item must be traceable back to a specific Element, Behavior, or Constraint. If it can't be traced, it's either a missing requirement or out of scope — clarify which.

### Platform Flags
- iOS vs. Android vs. Web have different safe areas, navigation patterns, and interaction conventions — confirm which is intended if ambiguous
- If targeting Figma Make: Make Kit authority is established by platform choice — verify the generated prompt references the Make Kit correctly and includes the readiness warning
- If targeting Google Stitch: DESIGN.md authority is established by platform choice — verify the generated prompt references DESIGN.md correctly and includes the readiness warning
- If targeting Claude Code + Figma MCP: verify the generated prompt includes the `figma-use` skill prerequisite, an explicit `use_figma` instruction, the target file/page URL (Q1a), and any Component Library URL (Q1b) or Variable Collection/Group references (Q1c). Include the readiness warning for MCP server connection, skill loading, and file edit access.

Ask any clarifying questions in a single message. Summarize what you understood, flag what's unclear, and wait for the user to confirm before proceeding.

---

## Phase 3: Generate the TC-EBC Prompt

Once inputs are confirmed, synthesize a structured prompt using the TC-EBC framework.

### Generation Mode — full prompt or guided review

Before generating, offer the user a choice of delivery mode (recall the user's preference across sessions and skip the offer if already established — confirm once if it has been a while):

> Two ways I can deliver this: **(a) the full prompt now** as one copy-ready block, or **(b) a guided review** — I'll show each section in turn (Task, Context, Elements, and so on) and you approve or revise it before we move to the next. Which do you prefer?

**Full prompt (default if the user has no preference):** Generate the complete prompt and deliver it per the Output Format below — a single raw Markdown fenced code block.

**Guided review:** Walk through the prompt section by section, in this order, building up the approved prompt as you go:

1. Header metadata
2. Task
3. Context
4. Elements
5. Behavior
6. Constraints
7. Conditional sections, each only if active: Localisation & I18n → Design Guidelines → Figma MCP → Make Kit
8. Acceptance Criteria

At each step:
- Present that one section as **rendered text** (not a fenced code block — the literal-Markdown rule applies only to the final assembled output, so review sections read cleanly).
- Ask the user to **approve it or describe a revision.** If they revise, apply the change, show the updated section, and confirm before advancing.
- Do not advance to the next section until the current one is approved.
- Keep each step tight — show the section and the approve/revise prompt, nothing more. No re-explaining the whole framework at each step.

After the final section (Acceptance Criteria) is approved, **assemble every approved section and deliver the complete prompt once, as a single raw Markdown fenced code block** per the Output Format below. Guided review changes how the prompt is reviewed, never how it is finally delivered — the copy-ready block is mandatory in both modes.

### TC-EBC Framework

The TC-EBC framework turns vague UI ideas into precise, executable instructions.

**T — Task**
One clear sentence. What should the AI build? Use an action verb and specify the UI type and product context. Avoid style adjectives — those belong in Constraints.
- ✅ `Create a mobile onboarding screen for a personal finance tracking app.`
- ❌ `Make something clean and modern for onboarding.`

**C — Context**
2–4 sentences. Where does this screen live in the product flow? Who is the user, what just happened before this screen, and what do they do next? Include emotional context if it affects design decisions (e.g., anxiety-reducing for medical apps, confidence-building for fintech).

**E — Elements**
Exhaustive list of required UI components and content. If a component is not listed, the AI may omit it or substitute something else.
- Name specific components: button, bottom sheet, tab bar, search bar, card, etc.
- Include real content: text labels, placeholder copy, icon descriptions, image subjects
- **Label every CTA with its tier:** `[Primary]`, `[Secondary]`, `[Tertiary]`, or `[Destructive]`. Exactly one `[Primary]` CTA per screen. Secondary CTAs must be visually subordinate; tertiary CTAs use text-link or icon-button weight only.
- **Include alt text** for all images and icon controls used as interactive elements
- **Flag count-dependent strings** (e.g., "N items selected") for pluralisation support
- Do not describe appearance here — color, size, and style belong in Constraints

**B — Behavior**
All interactive states and logic. Describe what happens in response to user actions.
- Include: default, hover, active, disabled, loading, error, and success states for all interactive elements
- **Visible focus ring on all interactive elements** when navigated by keyboard or switch access — this is a mandatory AA requirement, not optional
- Conditional logic: e.g., "button disabled until field passes validation"
- Transitions and animation: reference motion tokens for timing and curve — e.g., "slide up from bottom over `--duration-slow` with `--easing-decelerate`," "fade in over `--duration-fast`." Do not specify raw durations or easings.
- **Respect `prefers-reduced-motion`:** when non-essential motion is present, reduce or remove it for users who request reduced motion — replace with an instant change or minimal fade
- Scroll behavior: sticky headers, parallax, infinite scroll triggers

**C — Constraints**
The rules. This is where design system references, grid, brand tokens, platform specs, accessibility requirements, CTA hierarchy, localisation, and explicit prohibitions go.
- When an authority file is active (Make Kit or DESIGN.md established by Q1 platform choice, Figma Component Library/Variables established by Q1b/Q1c, or named design system confirmed in Q7): reference token names and the file's own naming conventions only. Do not include raw values. Defer to the authority file for anything it covers — do not duplicate or override its definitions.
- When no authority file is active: use CSS variable convention throughout. No raw values.
- Reference token names, not raw values: `--color-primary` / `{colors.primary}` / `{color/primary}` (Figma Variable name in a named Collection), not `#0057FF`
- Include platform, viewport, grid, spacing, and radius tokens
- Include the full WCAG 2.2 AA (or AAA) requirements with specific numeric values
- Include CTA hierarchy rules derived from the Elements tier assignments
- Include Localisation & I18n section when L10n is required
- Include Design Guidelines reference when a DESIGN.md or guidelines file exists
- Include Make Kit instruction when targeting Figma Make with an attached Make Kit
- Include the tech-stack rows (framework, styling system, component library) for code-generation platforms — using the ⚠️ developer call-out form for any part left unspecified, never an assumed stack. Omit these rows for Figma Make, Google Stitch, and the build-in-Figma branch.
- Include the Figma MCP "Build in Figma Design" section only on the Claude Code + Figma MCP (a) branch — with target file URL (Q1a), Component Library URL (Q1b if provided), Variable Collection(s)/Group(s) (Q1c if provided), and an explicit `figma-use` skill prerequisite plus `use_figma` tool call
- Include the "Build from Figma Reference" section only on the Claude Code + Figma MCP (b) branch — read-from-Figma (`get_design_context`/`get_screenshot`) with code generated in the resolved stack, and no `use_figma`/`figma-use`

### Output Format

Deliver the prompt **in-line in the chat as raw Markdown wrapped in a fenced code block** — never as a file attachment, downloadable artifact, canvas/document, or rendered preview. The user must see literal Markdown syntax (`#`, `**`, `-`, `|`, etc.) so they can copy it verbatim into their target tool. Use this structure:

````markdown
# Vibe-Coding Prompt
**Target platform:** [tool name]
*[Tech stack line — include for code-generation platforms; omit for Figma Make, Google Stitch, and the build-in-Figma branch.]*
**Tech stack:** [framework · styling system · component library — or "⚠️ to be confirmed by developer"]
**Scope:** [screen / component / flow]
**Prototype type:** [Functional prototype / Design mockup]
**Accessibility:** WCAG 2.2 [AA / AAA]
**L10n / I18n:** [Not required / Required — languages: list]

---

> **Plan before you build.** Read this entire prompt first — Task, Context, Elements, Behavior, Constraints, and Acceptance Criteria — then outline your implementation plan and confirm it accounts for every Element and Constraint before generating anything. Do not start building until the plan is complete.

## Task
[One sentence describing what to build.]

## Context
[2–4 sentences: product type, user, prior step, goal of this screen.]

## Elements
- [Named component or content item]
- [CTA label] [Primary / Secondary / Tertiary / Destructive] — [outcome-oriented label]
- [Images/icon controls: include alt text]
- [Count-dependent strings: flag for pluralisation support]
- [Continue for all required elements — exhaustive list]

## Behavior
- [Interaction or state: trigger → response]
- [All interactive elements show a visible focus ring on keyboard/switch access focus]
- [Continue for all behaviors]

## Constraints
- **Platform:** [iOS / Android / Web / Responsive]
- **Grid:** 8px base grid (4px microgrid for fine details) — `--space-sm` / `spacing.sm` (8px), `--space-lg` / `spacing.lg` (16px), `--space-xl` / `spacing.xl` (24px)
*[Tech stack rows — include for code-generation platforms. Omit for Figma Make, Google Stitch, and the Claude Code + Figma MCP build-in-Figma branch. Use the ⚠️ call-out form for any part the user did not specify — never substitute an assumed framework, styling system, or component library.]*
- **Tech stack — framework:** [e.g., React, Vue, Svelte, plain HTML/CSS, WordPress block theme, SwiftUI — or "⚠️ TO BE CONFIRMED BY DEVELOPER — framework not specified; do not assume one"]
- **Tech stack — styling system:** [e.g., Tailwind, CSS Modules, vanilla CSS/SCSS, styled-components — or "⚠️ TO BE CONFIRMED BY DEVELOPER — generic CSS-variable tokens used below; no framework-specific classes emitted"]
- **Tech stack — component library:** [e.g., shadcn/ui, MUI, Radix — or "none — build from primitives" — or "⚠️ TO BE CONFIRMED BY DEVELOPER — no library assumed"]
- **Design token / guidelines file:** [named file + availability — e.g., "DESIGN.md (present at project root)" — or "⚠️ planned, not yet created — see readiness note" — or "none"]
- **Color tokens:** [Token names — e.g., `--color-primary` / `{colors.primary}` / `{color/primary}` in Collection `[name]` (Figma Variable) — or "defer to authority file (Make Kit / DESIGN.md / Figma Variables)" — or "AI discretion"]
- **Typography tokens:** [Token names — e.g., `--font-size-h1` / `{typography.h1}` / `{typography/h1}` in Collection `[name]` (Figma Variable) — or "defer to authority file" — or "AI discretion"]
- **Spacing tokens:** [Token names — e.g., `--space-xl` / `{spacing.xl}` / `{spacing/xl}` in Collection `[name]` (Figma Variable) — or "defer to authority file" — or "AI discretion"]
- **Radius tokens:** [Token names — e.g., `--radius-md` / `{rounded.md}` / `{rounded/md}` in Collection `[name]` (Figma Variable) — or "defer to authority file" — or "AI discretion"]
- **Elevation / shadow:** [Token names — e.g., `--shadow-md` / `{elevation.md}` / effect Variable in Collection `[name]` (Figma Variable) — or "flat / no elevation unless specified" — or "defer to authority file" — or "AI discretion"]
*[Include the Motion row only when behaviors involve transitions or animation.]*
- **Motion:** [Transition timing/curve tokens — e.g., `--duration-base` / `--easing-standard`. Respect `prefers-reduced-motion: reduce` — reduce or remove non-essential motion, replacing it with an instant change or minimal fade. — or "defer to authority file"]
- **Casing:** Sentence case for all UI text (capitalise first word + proper nouns only). [If Title Case specified: Title Case for [element classes].] ALL CAPS only for short overline/eyebrow labels, applied via `text-transform: uppercase` — never hardcoded. Do not hardcode capital letters; express any uppercase/lowercase styling as `text-transform` so accessible names and translation sources stay in natural case.
- **CTA hierarchy:**
  - [Primary]: [label] — filled, highest visual weight, [position]
  - [Secondary]: [label] — outlined or ghost, subordinate to primary
  - [Tertiary]: [label] — text link or icon button only
  - No two CTAs may have equal visual weight
  - CTA labels are outcome-oriented; do not add helper text, tooltips, or walkthroughs to explain CTA purpose
- **Accessibility — WCAG 2.2 [AA / AAA]:**
  - Contrast (normal text): ≥ 4.5:1 [AA] or ≥ 7:1 [AAA]
  - Contrast (large text ≥ 24px regular / ≥ 18.67px bold): ≥ 3:1 [AA] or ≥ 4.5:1 [AAA]
  - Contrast (UI components, focus indicators): ≥ 3:1
  - Touch/pointer targets: ≥ 44×44px (iOS) / ≥ 48×48px (Android) recommended for touch; 24×24px is the WCAG 2.2 AA floor (SC 2.5.8), not a design target; ≥ 44×44px required [AAA, SC 2.5.5]
  - Focus: visible focus indicator required on all interactive elements; focused elements must not be entirely obscured by sticky content
  - Reflow: content must reflow at 320px viewport width without horizontal scrolling
  - Text spacing: support line height ≥ 1.5×, letter spacing ≥ 0.12em without loss of content
  - Labels: visible label text must match or be contained in the accessible name of its control
  - Non-text content: all meaningful images and icon controls must have text alternatives
- **Do not include:** [Explicit prohibitions if any]

*[Include this section only when L10n / I18n is required.]*
## Localisation & I18n
**Target languages / locales:** [e.g., en-US (source), de-DE, fr-FR, ar-SA, ja-JP]
**Text directionality:** [LTR only / LTR + RTL — full layout mirroring required for RTL locales]
**String expansion headroom:** All text containers must flex to accommodate strings up to [X]% longer than the English source. Widest-expanding language in scope: [e.g., German at ~35%]. Do not use fixed-width text containers for any UI labels, button copy, nav items, or badges.
**Format tokens:** All dates, numbers, currencies, and units must be rendered via localisation format tokens (e.g., `Intl.NumberFormat`, `Intl.DateTimeFormat`), never as hardcoded formatted strings.
**Font fallback stack:** The base font token must include fallbacks for all script systems in scope: [e.g., Latin → Arabic (Noto Sans Arabic) → CJK (Noto Sans CJK) → system-ui]
**Pluralisation:** The following strings vary by count and require pluralisation token support: [list count-dependent strings, e.g., "N items selected", "N results found"]
**Pseudo-localisation:** [If translations not yet available: Use pseudo-localised placeholder strings (e.g., `[Çréàté àccöûnt]`) in the prototype to simulate expansion and surface overflow early.]

*[Include this section only when the user has a DESIGN.md or other guidelines file.]*
## Design Guidelines
*[When targeting Google Stitch with DESIGN.md, include the following warning:]*
> ⚠️ **Before running this prompt:** Verify that your DESIGN.md is present at the project root and contains complete token definitions across primitive, semantic, and component tiers. This prompt treats DESIGN.md as the authoritative token source — missing token definitions will produce gaps in the output.
*[Replace the instruction below with the appropriate file reference for the target tool.]*
Before generating any UI, read [DESIGN.md / .cursorrules / CLAUDE.md / other guidelines file] at the project root. Use it as the authoritative source for all token values — primitive, semantic, and component tiers. Reference tokens using the file's own naming convention:
- For DESIGN.md: use dot-path syntax — `{colors.primary}`, `{spacing.lg}`, `{rounded.md}`, `{typography.h1}`, `{components.button.background}`
- Do not override token values defined in the guidelines file with values from this prompt.
- If a required token is absent from the file, note the gap and apply the closest defined token rather than introducing a raw value.

*[Include this section only when target is Claude Code + Figma MCP AND Q1-Direction = (a) build in Figma Design. For the (b) build-code-from-Figma branch, use the "Build from Figma Reference" section below instead — never both.]*
## Figma MCP — Build in Figma Design (Claude Code + Figma MCP, direction (a) only)
> ⚠️ **Before running this prompt:** Verify that (1) the Figma MCP server is connected and authenticated in Claude Code, (2) the `figma-use` skill is loaded — it is a **mandatory prerequisite** before any `use_figma` tool call, and (3) you have edit access to the target Figma Design file. Missing connections, an unloaded skill, or insufficient permissions will cause the prompt to fail at execution time.

**Target file/page (Q1a):** [paste URL]

**Component Library (Q1b):** [paste URL — or "none — no Component Library is in use for this screen"]

**Variables (Q1c):** [list Collection(s)/Group(s) and their location — target file / Component Library / both — or "none — no Figma Variables in scope for this screen"]

**Variable modes (Q1c.3):** [modes per Collection + default mode for this screen — or "none — no modes in scope"]

**Variable tier preference (Q1c.4):** [primitive / semantic / component — or "semantic (default — preserves intent and adapts across modes)"]

**Variable exclusions (Q1c.2):** [Collections/Groups to avoid — or "none"]

Create this screen in the target Figma Design file specified above by invoking the `figma-use` skill first, then calling the `use_figma` MCP tool. Specifically:
- Invoke the `figma-use` skill before any `use_figma` call. Skipping this causes hard-to-debug failures.
- Use `use_figma` to assemble the screen incrementally, section by section — header, hero, body sections, footer — rather than as a single monolithic call.
- **Components:** If a Component Library URL is provided, import components from that library and instantiate them in the target file. Do not create equivalents from primitives when a library component exists. Inspect the Component Library for any Variable Collections it defines and prefer those Variables when applicable.
- **Variables:** If Variable Collection(s)/Group(s) are listed, bind every styling property (fills, strokes, typography, spacing, radius, effects) to the appropriate Variable from the named Collection(s). Reference Variables by their Figma name in the form `{group/variable-name}` (e.g., `{color/primary}`, `{spacing/lg}`) — the Collection is named in the Variables field above, not embedded in each reference. Do not hardcode raw color, typography, spacing, or radius values for any property that has a matching Variable.
- **Tier preference:** Prefer **semantic-tier Variables** (e.g., `{color/text/primary}`, `{color/surface/raised}`) over primitive Variables (e.g., `{color/gray/900}`, `{color/blue/500}`) wherever both exist for the same role. Semantic Variables preserve design intent, adapt correctly across modes, and survive primitive re-mapping. Bind to primitives only when no semantic equivalent exists or the user explicitly requested primitive-tier binding.
- **Modes:** If Variable Collections include modes (e.g., Light/Dark, Density, Brand), bindings **must be mode-aware** — reference the Collection so Figma resolves the active mode at render time. Never hardcode a specific mode's value; doing so defeats the Variable system and breaks mode switching. Honor the default mode named for this screen.
- **Exclusions:** Do not bind to any Collection or Group listed in the Variable exclusions field above, even if a matching Variable exists. Treat exclusions as hard prohibitions.
**Make-ready construction (build the file so it can later feed Figma Make or another vibe-coded target):**
- **Auto-layout (required):** Build every container with nested Figma auto-layout — stacks that express clear vertical/horizontal relationships. Never use Groups; they carry no layout logic. Use absolute positioning only for small flourishes (e.g., a notification badge), never for core layout, or downstream tools can't flow content into the frame.
- **Sizing:** Use Fill Container and Hug Contents to declare which elements are flexible vs. content-driven, and set Min/Max dimensions as safety rails so generated elements can't collapse or balloon.
- **Higher-order instances:** Assemble the screen from complete, higher-order compositions (full cards, headers, rows) as instances of library components — not loose atomic primitives. Never detach instances; detaching severs the link to the design system's rules and properties.
- **Naming:** Name every frame, section, and layer semantically and BEM-like (e.g., `Card / Header / Title`). Where the engineering component is known, match its name exactly (e.g., `FormItem / Input / Select`) so MCP can map the design to code components. The layer tree should read like clean code.
- **Variable-based spacing:** Apply spacing and padding from Variables (not raw values) so the generated frames keep the design system's rhythm, in addition to the semantic, mode-aware Variable bindings described above.
- **Hygiene:** Leave no ghost layers — delete hidden layers and anything at 0% opacity (downstream tools still "see" them, producing phantom spacing and hallucinated elements). Do not wrap individual text layers in frames (it emits unnecessary wrapper `<div>`s downstream and hinders text editing); use a wrapping frame only for genuine padding/spacing.
- **Slots:** When the referenced Component Library uses native Slot properties, instantiate and fill those slots rather than rebuilding the content; and when the screen has reusable templated regions that would benefit from slots, define native Slot properties with curated Preferred Instances and quality default content, keeping slot nesting to 2–3 levels at most.
- **Make-ready test:** The finished frame must be reusable as input for Figma Make or another vibe-coded target. Sanity check: if a junior developer who couldn't read the labels could rebuild it from the layer panel alone, the auto-layout and naming are right.
- If a required element has no Component Library equivalent and no matching Variable, build it from primitives using the closest semantic token names from the Constraints block — and flag the gap rather than substituting silently.

*[Include this section only when target is Claude Code + Figma MCP AND Q1-Direction = (b) build code from a Figma reference. Never include this together with the "Build in Figma Design" section above.]*
## Build from Figma Reference (Claude Code + Figma MCP, direction (b) only)
> ⚠️ **Before running this prompt:** Verify that (1) the Figma MCP server is connected and authenticated in Claude Code, and (2) you have at least read access to the source Figma file. This prompt **reads** the Figma frame as a design reference and generates code in your stack — it does not create or modify anything in Figma. The `figma-use` skill and `use_figma` are not used here.

**Source Figma file/frame (Q1a-ref):** [paste read-only reference URL]

Build this screen as code in the tech stack named in the Constraints block, using the Figma frame above only as the visual reference. Specifically:
- Read the source frame with `get_design_context` (and `get_screenshot` for visual confirmation) to extract layout, hierarchy, spacing, and content. Do not call `use_figma` and do not write anything back to Figma.
- Generate code in the confirmed framework and styling system. If the stack is marked "to be confirmed", generate framework-neutral, semantic HTML/CSS using the generic CSS-variable tokens in the Constraints block, and leave the developer call-out intact rather than assuming React/Tailwind/shadcn.
- Match the Figma frame's structure to your component idiom: map Figma frames/auto-layout to the stack's layout primitives (fl/grid containers, stacks, etc.).
- Where the Figma file uses Variables or styles, map them to the project's token names per the styling-system convention — do not hardcode raw values pulled from Figma when a token equivalent exists.
- Flag any part of the frame you cannot faithfully reproduce in the target stack rather than silently approximating it.

*[Include this section only when target is Figma Make.]*
## Make Kit (Figma Make only)
> ⚠️ **Before running this prompt:** Verify that your Make Kit is attached to this Figma Make project and contains your complete component library, styling tokens, and usage instructions. This prompt treats the Make Kit as non-negotiable — missing or incomplete content will produce gaps in the output.

Use the Make Kit attached to this project as the single source of truth for all design decisions. Specifically:
- Use only components defined in the Make Kit. Do not introduce components from outside it.
- Apply only the styling values (color, typography, spacing, elevation, radius) defined in the Make Kit. Do not override these values with your own defaults.
- Follow the explicit usage instructions in the Make Kit exactly. Where the Make Kit specifies how a component should be used, applied, or combined, treat those instructions as non-negotiable rules — not suggestions.
- If a required element has no Make Kit equivalent, flag it rather than substituting a generic component.

## Acceptance Criteria
*[Functional prototype: mix of interaction checks and visual checks. Design mockup: visual inspection checks only. Every item must map to a specific Element, Behavior, or Constraint above. Write each as a binary, checkable statement — pass or fail, no gradients. The items below are mandatory in every prompt. Add project-specific items after.]*
- [ ] Exactly one primary CTA is present and visually dominant
- [ ] No two CTAs have equal visual weight
- [ ] All CTA labels are outcome-oriented (not generic verbs like "Submit" or "Continue")
- [ ] No helper text or walkthroughs are used to explain what a CTA does
- [ ] All UI text uses the specified casing convention (sentence case by default); no hardcoded ALL CAPS — uppercase styling applied via `text-transform`
- [ ] All text/background colour combinations meet the applicable contrast ratio (≥ 4.5:1 normal text, ≥ 3:1 large text) [AA] / (≥ 7:1 normal, ≥ 4.5:1 large) [AAA]
- [ ] All interactive elements have a visible focus indicator
- [ ] Touch/pointer targets are ≥ 44×44px (iOS) / ≥ 48×48px (Android); none below the 24×24px WCAG 2.2 AA floor [AAA: ≥ 44×44px required]
- [ ] Content reflows at 320px viewport width without horizontal scroll
- [ ] All meaningful images and icon controls have text alternatives
*[Include the following item only when behaviors involve transitions or animation:]*
- [ ] Non-essential motion is reduced or removed under prefers-reduced-motion; transitions reference motion tokens, not raw durations/easings
*[Include the following items only when L10n / I18n is required:]*
- [ ] All text containers flex to accommodate strings up to [X]% longer than the English source
- [ ] No fixed-width text containers exist for UI labels, button copy, nav items, or badges
- [ ] All dates, numbers, currencies, and units are rendered via format tokens, not hardcoded strings
- [ ] RTL layout is fully mirrored for Arabic/Hebrew/Persian locales (if in scope)
- [ ] Font fallback stack covers all script systems in scope
- [ ] Count-dependent strings use pluralisation tokens
*[Include the following items only when target is Claude Code + Figma MCP AND Q1-Direction = (a) build in Figma Design:]*
- [ ] Screen is created in the target Figma Design file/page specified at Q1a
- [ ] Every container uses auto-layout (no Groups); sizing uses Fill/Hug with Min/Max safety rails where needed
- [ ] Frames, sections, and component instances are named semantically (BEM-like) and matched to codebase component names where known
- [ ] Components are library instances (not detached); no ghost or 0%-opacity layers remain
- [ ] The frame is Make-ready — reusable as input for Figma Make or another vibe-coded target
*[Include the following items only when target is Claude Code + Figma MCP AND Q1-Direction = (b) build code from a Figma reference:]*
- [ ] Code is generated in the tech stack named in Constraints; nothing is written back to Figma
- [ ] The source Figma frame (Q1a-ref) is read via `get_design_context`/`get_screenshot`, not modified
*[Include the following item whenever any tech-stack part is marked "to be confirmed by developer":]*
- [ ] No framework, styling system, or component library is assumed where the user left it unspecified; the developer call-out is present and no Tailwind/shadcn (or other unconfirmed library) code is emitted
*[Include the following items only when a Figma Component Library URL was provided (Q1b):]*
- [ ] All components with a Component Library equivalent are instantiated from the library, not built from primitives
- [ ] Any element with no Component Library equivalent is flagged rather than substituted silently
*[Include the following items only when Figma Variables were provided (Q1c):]*
- [ ] All styling properties (fills, strokes, typography, spacing, radius, effects) bound to the named Variables where a matching Variable exists
- [ ] No raw color, typography, spacing, or radius values are hardcoded for properties that have a matching Variable
- [ ] Where Variable Collections include modes (Light/Dark, Density, Brand), bindings reference the Collection (not a specific mode value) so mode switching works correctly
- [ ] Bindings prefer semantic-tier Variables (e.g., color/text/primary) over primitive-tier Variables (e.g., color/gray/900) wherever both exist for the same role
- [ ] No bindings reference any Collection or Group listed in the Variable exclusions field
- [ ] [Additional project-specific checkable condition]
- [ ] [Continue for all project-specific requirements]

---

*Generated with the TC-EBC framework. Refine in a text editor before running in [tool name].*
````

---

## Post-Generation Guidance

After delivering the prompt, offer the following concisely — one short paragraph or a tight list, not a wall of text. Cover only what's relevant to this session.

- **Scope:** One screen at a time. Chain prompts sequentially for multi-screen flows.
- **CTA clarity:** One primary action per screen. If helper text is needed to explain a button, fix the label or layout instead. Labels should name the outcome.
- **Authority file:** The prompt instructs the AI to treat your Make Kit, DESIGN.md, or Figma Variables (whichever is active) as the sole source of styling truth. Verify your authority file or Variable Collections are complete before running — gaps will appear as gaps in the output. Any values you manually add outside the authority file or named Collections will conflict with them.
- **Design system:** Reference token names in Constraints, not raw values. For DESIGN.md: use dot-path syntax — `{colors.primary}`, `{spacing.lg}`. Generate at `stitch.withgoogle.com`; validate with `npx @google/design.md lint`.
- **Make Kit:** The prompt instructs the AI to use it as sole source of truth. Manual overrides will conflict.
- **Figma MCP:** Before running, verify the Figma MCP server is connected, the `figma-use` skill is loaded (mandatory prerequisite), and you have edit access to the target file. The prompt instructs `use_figma` to assemble the screen section-by-section in the target file URL you specified — re-running on a different file requires editing the URL in the prompt. Component Library and Variables references are scoped to the URLs/Collections you provided; the AI will not silently substitute alternates.
- **Accessibility:** Verify colour contrast with the WebAIM Contrast Checker before sign-off. Run the full AC checklist — AA, CTA, and L10n items — before iterating.
- **Localisation:** The prompt specifies expansion headroom, RTL mirroring, format tokens, and font fallbacks. The most common mistake is sizing containers to English text.
- **Iteration:** Use targeted edit prompts rather than full reruns. Specify exactly what failed the AC checklist and ask for a fix to that item only.
- **Prompt drift:** Full reruns cost credits in metered tools. Specify precisely before running.

---

## Key Principles (always in effect)

- **Execution Boundary.** This skill is a prompt author. When a user names a target environment such as Claude Code + Figma MCP, the output is always written text — copy-ready instructions the user will apply in their own session. Treat all platform and tool names as the destination audience for the text this skill produces, not as a directive to interact with those systems directly.
- **Output is raw Markdown, in-line in the chat.** The generated prompt is always delivered in the chat as raw Markdown wrapped in a fenced code block — never as a file attachment, downloadable artifact, canvas/document, or rendered preview. The user must see literal Markdown syntax (`#`, `**`, `-`, `|`, etc.) so they can copy it verbatim. Do not offer to "save as a file" or "open in a document" instead.
- **Generation mode is the user's choice; final delivery is fixed.** Phase 3 offers a full-prompt-now or guided section-by-section review (preference recalled across sessions). Guided review presents each section as rendered text for approve-or-revise, then assembles and delivers the complete prompt as a single raw Markdown fenced code block. The mode changes how the prompt is reviewed, never how it is finally delivered.
- **Platform neutrality.** Never suggest or favour a specific vibe-coding tool. Q1 is an open question — the user names their platform. The role of this workflow is to produce the best possible prompt for whatever platform the user has chosen.
- **A11y and L10n are always required inputs.** Q14 (accessibility level) and Q15 (localisation scope) are mandatory on first encounter and cannot be skipped. Every product has an accessibility posture and a localisation posture, even if the answers are "AA" and "English only." Once established, both are recalled and not re-asked.
- **Tone: professional and direct.** Communicate as a senior product designer — clear, concise, no filler, no flattery. Warm means human and collegial, not effusive. Skip preambles like "Just to note —" or "Great question." Get to the point.
- **Phase 2 is a dependency chain, not a checklist.** The Phase 2 flags must run in order: Scope → Ambiguity → CTA → Casing → Stack → Raw Value Translation → Grid → Design System → Make Kit → DESIGN.md → Figma MCP → Accessibility → L10n → Acceptance Criteria → Platform. The Stack flag must precede Raw Value Translation because the resolved styling system determines token-naming convention; token corrections must precede design system verification; design system verification must precede authority file checks (Make Kit, DESIGN.md, Figma MCP); authority file checks must precede accessibility and L10n audits; all audits must precede AC generation. Reordering breaks the chain.
- **Localisation is a layout constraint, not a content task.** When L10n / I18n is required, the prompt must specify target languages, string expansion headroom (up to 40% for German), text directionality (full RTL mirroring for Arabic/Hebrew), format tokens for dates/numbers/currencies, font fallback stacks for non-Latin scripts, and pluralisation support. Fixed-width text containers are a localisation risk and must be flagged.
- **One primary CTA per screen, strict hierarchy below it.** Every screen must have exactly one primary action. Secondary and tertiary CTAs must be visually subordinate. No two CTAs may have equal visual weight. Helper text and walkthroughs used to explain CTA purpose are design smells — the label, context, or layout should be fixed instead. CTA labels must name the outcome, not the action type.
- **WCAG 2.2 AA is the unconditional baseline.** Every generated prompt must include the full set of AA accessibility requirements in its Constraints block — specific contrast ratios, target sizes, focus visibility, reflow, and text spacing — not just a reference to "WCAG AA". AAA is opt-in via Q14 and adds enhanced contrast, 44×44px targets, and stricter text presentation rules.
- **Guidelines files are the token source of truth.** If a DESIGN.md, `.cursorrules`, `CLAUDE.md`, or equivalent guidelines file exists, the prompt must instruct the tool to read it before generating. Token values come from the file; the prompt references token names, not raw values. For DESIGN.md, use dot-path syntax: `{colors.primary}`, `{spacing.lg}`, `{rounded.md}`.
- **Authority file gates primitive intake.** For Figma Make and Google Stitch, the platform choice at Q1 automatically establishes Make Kit and DESIGN.md authority respectively — no separate confirmation question is needed. For Claude Code + Figma MCP, the Component Library (Q1b) and Variables (Q1c) follow-ups establish a granular dual authority: the library covers components, Variables cover styling, and they fire independently — one, both, or neither may be active. For other platforms, a named design system confirmed at Q7 sets authority. When authority is established, Q9 (Constraints) must be scoped accordingly: suppress requests for raw styling values — colors, font sizes, spacing, radius — and ask only for platform constraints and explicit overrides. For Make Kit, DESIGN.md, and Figma Variables, raw values supplied by the user are redirected to the authority file rather than translated. In generic prompts without an authority file, gather full styling data using the Raw Value Translation process.
- **No assumed tech stack.** For any platform that produces code, the framework, styling system, and component library come from the user (Q1d/Q1e/Q1f) — never from a default. The only sanctioned implicit stack is Figma Make's React + Tailwind + shadcn/ui + Radix (its real generation stack, governed by the Make Kit and overridable). When any part is unspecified, the prompt stays framework-neutral, uses generic CSS-variable tokens, emits no Tailwind classes or library-specific component names, and carries a "Tech stack — to be confirmed by developer" call-out. Do not infer React/Tailwind/shadcn from silence.
- **Confirm assets; never invent them.** Any token file, guidelines file, or Figma Variable/Library referenced in a prompt must be one the user actually named, and its availability must be confirmed (present-and-ready vs. planned). A planned-but-absent asset becomes a readiness call-out, not a hard reference. The skill must never reference a `DESIGN.md`, `tokens.json`, or other style asset the user did not name.
- **Figma MCP is bidirectional; the direction gate decides routing.** Claude Code + Figma MCP can either build a screen *in* Figma Design (direction (a) — `use_figma`) or build code *from* a Figma reference (direction (b) — `get_design_context`/`get_screenshot`). The Q1-Direction follow-up resolves which before any other Figma follow-up. On (a), the generated prompt must include the `figma-use` skill prerequisite, an explicit `use_figma` tool-call instruction, the target Figma Design file or page URL (Q1a), and any Component Library URL (Q1b) and Variable Collection(s)/Group(s) (Q1c) the user provided. The target file URL is required — without it, the AI has no destination. Component Library and Variables are optional; each, when present, narrows Q9 (Constraints) by suppressing the corresponding category of intake. Variable references in Constraints and Elements must use `{group/variable-name}` form, not CSS variable convention or DESIGN.md dot-path syntax. **Two best-practice defaults are enforced for Variable bindings:** (1) **semantic-tier Variables are preferred over primitive-tier** because semantic Variables preserve design intent and adapt across modes — bind to primitives only when no semantic equivalent exists or the user explicitly requested primitive-tier; (2) **bindings are mode-aware** when Collections include modes (Light/Dark, Density, Brand) — reference the Collection so Figma resolves the active mode at render time, never hardcode a specific mode's value. Both defaults apply unless the user explicitly overrides them at Q1c.
- **Figma Design output must be Make-ready.** On the build-in-Figma branch (direction (a)), the generated prompt must instruct the AI to construct the screen so it can be reused downstream as input for Figma Make or another vibe-coded target — never a one-off canvas drawing. That means: nested auto-layout everywhere (never Groups); Fill/Hug + Min/Max sizing; higher-order compositions as library instances that are never detached; semantic, BEM-like layer names matched to codebase component names where known so MCP can map design to code; Variable-based spacing and semantic, mode-aware Variable bindings; clean layers (no ghost or 0%-opacity layers, no frame-wrapped single text layers); and native Slots where the referenced library uses them or the screen benefits. A design a downstream tool can't ingest is a failed output, not a finished one.
- **8px grid is the default.** All spacing and radius values must land on the 8px base grid, or the 4px microgrid for fine-grained contexts. Off-grid values are rounded to the nearest 4px multiple, corrected before tokenisation, and the user is informed of the change.
- **Sentence case by default.** All UI text uses sentence case ("Create account", "Account settings") unless the user sets another convention at Q16, which is recalled across sessions. ALL CAPS is reserved for short overline/eyebrow labels and applied via `text-transform: uppercase`, never hardcoded — hardcoded capitals break accessible names and localisation source strings. all-lowercase and Title Case are deliberate brand choices, not defaults.
- **Token names beat raw values.** Reference design tokens and CSS variables by name (`--font-size-body`, `--color-primary`) rather than hard-coded values. Raw values like `#0057FF` or `16px` belong in the token file, not the prompt.
- **Motion uses tokens and respects reduced-motion.** When behaviors involve transitions or animation, reference motion tokens (`--duration-fast/base/slow`, `--easing-standard/decelerate/accelerate`) rather than raw durations or easings, and instruct the AI to honour `prefers-reduced-motion`. Screens with no non-essential motion carry no motion guidance — never invent animation.
- **Translate, don't discard.** When a user provides a raw value, convert it to the closest semantic token and tell them — or redirect it to the active authority file (Make Kit reference, DESIGN.md dot-path, or Figma Variable `{group/variable-name}` in the named Collection). Never silently drop a raw value or pass it through untranslated.
- **Elements list is exhaustive.** Anything not listed may be omitted or hallucinated.
- **Plan before executing.** Every generated prompt instructs the target AI to read the full prompt and outline its implementation plan before building, so it accounts for all Elements and Constraints up front rather than generating reactively and missing requirements. This instruction is always present in the output template.
- **One screen at a time.** Break multi-screen flows into sequential prompts.
- **Design system = ingredient list.** If a system exists, the AI should cook from it, not freestyle.
- **Make Kit is law.** For Figma Make projects, Make Kit authority is established by the platform choice at Q1 — the prompt must reference it as the non-negotiable source of truth for components, styling values, and usage rules, and prohibit any override. If the Make Kit is not yet configured, the user must set it up before running.
- **AC makes iteration precise.** Vague dissatisfaction leads to vague reruns. A checklist of binary criteria turns "something's off" into a specific, fixable list of failures.
- **Prototype type shapes AC.** Functional prototype criteria include interaction and logic checks; design mockup criteria focus on visual inspection. Both use the same binary format — the subject matter differs, not the standard.
- **Behaviors need states.** Every interactive element has at minimum a default, a focused, and an active state. Ask if the user hasn't specified. Focus state is a mandatory AA requirement, not optional.
- **Implicit context is a liability.** Any context the user assumes the AI knows is context the AI doesn't have. Make it explicit in the prompt.
- **Prompt drift is real.** In metered tools, refine before running, not after.
- **Platform matters.** iOS, Android, and Web have different safe zones, nav patterns, and touch targets — always confirm.
