# Vibe Prompt Architect — Cheat Sheet

Quick reference for getting the best results from **Vibe Prompt Architect**, available as a **Claude Skill** and a **Gemini Gem**. Read time: ~5 minutes.

> **In one line:** It interviews you about the screen you want, audits your answers, and hands you a copy-ready Markdown prompt to paste into your vibe-coding tool — so you get a good result on the first run instead of iterating blindly.

---

## How to start a session

| | Claude Skill | Gemini Gem |
|---|---|---|
| **Setup** | Place `SKILL.md` in your Skills directory | Paste `vibe-prompt-architect-gem.md` into a new Gem |
| **Trigger** | Say "help me write a vibe-coding prompt", "I want to build a UI with AI", or just describe the screen | Start a conversation in the Gem |
| **Answer style** | One question at a time — reply, wait for the next | Same |

---

## What it does (3 phases)

1. **Gather** — asks a focused set of questions, one at a time, about your screen (fewer than it looks — low-stakes ones are inferred and just confirmed). Prefer to go fast? Paste everything you know in one message and it fills the gaps (express lane).
2. **Clarify** — audits your answers for ambiguity, off-grid spacing, raw values, and CTA / casing / accessibility / localisation gaps, then shows you the corrections. Also reconciles what you typed against anything you attached — reads reference images and flags where they disagree with your description, and confirms typed values/names match your DESIGN.md, token file, or Figma Variables.
3. **Generate** — outputs a structured **TC-EBC** prompt as raw Markdown in a fenced code block, ready to copy.

## What you get

- A **TC-EBC prompt** (Task · Context · Elements · Behavior · Constraints) plus a pre-filled **Acceptance Criteria** checklist.
- A **plan-first instruction** at the top telling your target tool to read the whole prompt and plan before it builds.
- Delivered as **raw Markdown in the chat** — copy it verbatim; never a file, canvas, or doc.
- **Built-in quality** applied automatically (see below) — you don't have to ask for it.

## When to use it — and when not

- ✅ Use it to produce a prompt for Figma Make, Google Stitch, Claude Code + Figma MCP, Cursor, Lovable, v0, or any AI UI tool.
- ✅ One screen, component, or flow at a time.
- ❌ It does **not** generate UI itself — you still run the prompt in your tool.
- ❌ Not for whole-app generation in one shot — chain screens sequentially.

---

## Before you start — prep checklist

> **Tip:** want to write your requirements up front? Fill in `tc-ebc-requirements-template.md` and paste it in (or attach it) — the workflow reads it, skips what you've answered, and asks only for the rest.

Have these ready for a smooth session:

- [ ] **Target platform/tool** the prompt will run in
- [ ] **Tech stack** *(code targets)* — framework, styling system, component library (or "not sure" — it won't be assumed)
- [ ] **Scope** — the one screen / component / flow you're building
- [ ] **Required elements** — every component, label, and image that must appear
- [ ] **Primary action** — the single most important thing the user should do here
- [ ] **Behaviors / states** — what happens on interaction
- [ ] **Design system / tokens** — a system name, or a guidelines file
- [ ] **Accessibility level** — AA (default) or AAA *(required question)*
- [ ] **Localisation scope** — languages/locales, or "English only" *(required question)*
- [ ] **Casing** — sentence case is the default; flag it only if your brand differs
- [ ] **Reference images** — screenshots or inspiration (optional)

### Per-platform — "have this ready"

| Platform | Bring this | Why |
|---|---|---|
| **Figma Make** | Your **Make Kit**, attached to the project | It's the sole source of truth for components and styling — the workflow won't ask you for raw values |
| **Google Stitch** | A complete **DESIGN.md** at the project root | Authoritative token source across primitive / semantic / component tiers |
| **Claude Code + Figma MCP** | First decide the **direction**: (a) build in Figma Design, or (b) build code from a Figma reference. (a) → Target **Figma file/page URL** (edit access), optional **Component Library URL**, any **Variable Collections**, **`figma-use` skill loaded**, MCP connected. (b) → Source **Figma URL** (read access) + your **tech stack** | (a) creates the screen directly in Figma using **Make-ready construction** (nested auto-layout, library instances, semantic names, clean layers) so it can later feed Figma Make or other vibe-coded projects — library Variables must be *published* and *enabled* in the target file; (b) reads the frame and writes code in your stack — nothing is written back to Figma |
| **Cursor / Claude Code (generic) / WordPress / other code targets** | Your **tech stack** (framework, styling system, component library) + any **`.cursorrules`** / **`CLAUDE.md`** | The prompt is written for your stack — no framework or library is assumed; it reads your guidelines file before generating when present |
| **Any other tool** | Token / design-system names (or nothing) | Falls back to the CSS-variable token convention; nothing framework-specific assumed |

---

## What it'll ask you (intake at a glance)

You'll get these one at a time, grouped roughly as:

- **Setup** — platform (with follow-ups: Figma Make / Google Stitch / Figma MCP build-direction; tech stack — framework, styling system, component library — for code targets)
- **Context** — UI type, product & user, the user's moment (what happened before / next)
- **Elements** — required elements, then the primary action(s)
- **Behavior** — states, transitions, conditional logic
- **Constraints** — design system / tokens / guidelines file, then constraints (scoped to your authority file)
- **Wrap-up** — reference images, acceptance criteria *(scope and prototype type are inferred from context and just confirmed in the summary)*
- **Posture** — accessibility level\*, localisation scope\*, casing convention

\* **Mandatory** — cannot be skipped on first encounter.

> **Recall:** Stable answers (platform, design system, WCAG level, localisation scope, casing) are remembered across sessions, so you won't re-answer them when building the next screen. The Gem marks recalled items *(recalled)* in its summary — **glance at those and correct any that are stale.**

---

## Quality you get for free

Applied to every prompt by default — no need to ask:

| Area | What's enforced |
|---|---|
| **Accessibility** | WCAG 2.2 **AA** baseline with numeric values (contrast; touch targets **≥ 44×44px (iOS) / ≥ 48×48px (Android)** — 24×24px is the bare AA floor, not a target; visible focus; reflow); AAA opt-in |
| **CTA hierarchy** | Prefer **one dominant primary CTA** (co-equal primaries OK where the screen needs them, e.g., sign in / create account); outcome-oriented labels ("Create account", not "Submit"); no *unintended* equal-weight rivals |
| **8px grid** | Spacing/radius snapped to the 8px grid (4px microgrid for fine detail); off-grid values corrected and shown to you |
| **Token-first values** | Raw hex / px / pt translated to semantic token names; ambiguous values flagged for confirmation |
| **Casing** | **Sentence case** default; ALL CAPS reserved for short overline labels via `text-transform`, never hardcoded |
| **Elevation** | Shadows mapped to a `--shadow-*` / `elevation.*` scale |
| **Motion** | `--duration-*` / `--easing-*` tokens; respects `prefers-reduced-motion` when motion is present |
| **Localisation** | Expansion headroom, RTL mirroring, format tokens, font fallbacks, pluralisation (when in scope) |
| **Completeness** | Empty / loading / error states for data & input surfaces; an exit/back/dismiss path on every screen & overlay; single-theme default (no invented dark mode); data source/volume + pagination for functional prototypes; breakpoint behaviour for Web/Responsive; named assets confirmed (not fabricated); a final conflict check for requirements that can't all hold |
| **Overridable defaults** | Defaults (grid, tokens, AA, casing, motion) are shown, not silent — if one would change something you specified (an exact value, a brand name like "Smart Reply", a chosen duration, a brand colour), you're asked rather than overridden; borders aren't grid-rounded; AA is never silently dropped |

---

## Tips & tricks

- **One screen at a time.** For a multi-screen flow, generate each screen as its own prompt and chain them.
- **Don't pre-format raw values.** Type `#0057FF` or `16px` if that's what you have — it translates to tokens and confirms with you.
- **Use guided review for high-stakes prompts.** At generation, choose the **section-by-section review** to approve or revise Task, Elements, Constraints, and the rest before the final block is assembled.
- **Take the full prompt for quick ones.** Choose "full prompt now" when you just want the output fast.
- **Verify recalled inputs.** If something was remembered from a past session, confirm it's still true before proceeding.
- **Refine before running on metered tools.** Tools that charge per run reward a tighter prompt — fix the prompt, not the output.
- **Copy the fenced block verbatim.** The literal Markdown is intentional; paste it as-is into your tool.
- **Run the Acceptance Criteria checklist before iterating.** It turns "something's off" into a specific pass/fail list.
- **Check contrast** with the [WebAIM Contrast Checker](https://webaim.org/resources/contrastchecker/) before sign-off.
- **Name your design system if you have one.** Referencing real components and tokens dramatically improves output fidelity.
- **Let it push back on CTA helper text.** If you need a tooltip to explain a button, the label or layout is the real fix.

---

## After you generate

- [ ] Copy the raw Markdown prompt verbatim into your tool
- [ ] Confirm your authority file (Make Kit / DESIGN.md / Variables) is complete — gaps there become gaps in the output
- [ ] For Figma MCP: MCP connected, `figma-use` loaded, edit access confirmed, library Variables enabled in the target file
- [ ] Run the prompt, then work through the **Acceptance Criteria** checklist
- [ ] Verify colour contrast; if something's off, fix the **prompt** (not the output) and re-run

---

## Glossary

- **TC-EBC** — the prompt structure: **T**ask · **C**ontext · **E**lements · **B**ehavior · **C**onstraints.
- **Authority file** — the single source of truth for styling: Make Kit (Figma Make), DESIGN.md (Google Stitch), or Figma Variables (Figma MCP). When one is active, the workflow defers raw styling to it instead of asking you for values.
- **Token** — a named design value (`--color-primary`, `{spacing.lg}`, `{color/primary}`) used instead of a raw hex/px.
- **Token tiers** — *primitive* (raw, e.g. `gray-900`), *semantic* (role, e.g. `text-primary`), *component* (scoped). Semantic is preferred — it preserves intent and adapts across modes.
- **CTA tiers** — `[Primary]` / `[Secondary]` / `[Tertiary]` / `[Destructive]` visual-weight labels on actions.
- **Guided review** — the Phase 3 mode that walks each prompt section for your approval before assembling the final output.

---

*Companion to `README.md` (full overview) and `SKILL.md` / `vibe-prompt-architect-gem.md` (the workflow itself).*
