---
product:
feature:
version:
---

# TC-EBC Requirements

<!--
HOW TO USE THIS TEMPLATE
Fill in the sections below, then paste the whole file into the Vibe Prompt Architect Skill or Gem — or attach it as a source file. The workflow reads what you've already written, skips questions you've answered, and asks only for what's missing. Leaving a section blank is fine — the workflow will ask. TC-EBC = Task · Context · Elements · Behavior · Constraints.
-->

## Setup (Optional)

<!--
SETUP — quick context the workflow needs before the TC-EBC body. Fill in what you know.
- Platform: the tool that will run the prompt (Figma Make, Google Stitch, Claude Code + Figma MCP, Cursor, v0, Lovable, WordPress, …).
- Tech stack (code targets only): framework · styling system · component library (e.g., "React · Tailwind · shadcn/ui"). Write "not sure" and nothing will be assumed.
- Scope: a single component, one screen, or a multi-screen flow.
- Prototype type: functional prototype (real logic/data) or design mockup (visual fidelity).
- Accessibility: WCAG 2.2 AA (default) or AAA.
- Localization: "English only", or the languages/locales in scope.
-->

- **Platform:**
- **Tech stack:**
- **Scope:**
- **Prototype type:**
- **Accessibility:** AA
- **Localization:** English only

---

## Task

<!--
TASK — what to build, in ONE sentence. Action verb + UI type + product context. No style adjectives (those belong in Constraints).
- ✅ "Create a mobile onboarding screen for a personal-finance tracking app."
- ❌ "Make something clean and modern for onboarding."
-->

Task description…

## Context

<!--
CONTEXT — where this screen lives and who it's for. 2–4 sentences. Who is the user? What just happened before this screen, and what do they do next? Include emotional context if it shapes the design (reassuring for medical, confident for fintech).
-->

Context description…

## Elements

<!--
ELEMENTS — an EXHAUSTIVE list of what must appear. Anything not listed may be omitted or substituted.
- Name specific components (button, tab bar, search field, card, bottom sheet, …).
- Include real content: labels, placeholder copy, icon/image subjects.
- Label every call-to-action with its tier: [Primary] [Secondary] [Tertiary] [Destructive]. Prefer one dominant [Primary]; co-equal primaries are fine if the screen genuinely needs them (e.g., "Sign in" / "Create account").
- Add alt text for images and icon controls. Flag count-dependent strings ("3 items") for pluralization.
- Don't describe appearance here — color, size, and style go in Constraints.
-->

* First component…
* Second component…
* Etc.

## Behavior

<!--
BEHAVIOR — how the UI responds to interaction.
- States for each interactive element: default, hover, active, disabled, loading, error, success (a visible focus state is required).
- Empty / loading / error states for anything backed by data.
- Conditional logic ("submit disabled until the form is valid").
- Navigation: where the primary action leads; how to exit / go back / dismiss.
- Transitions / animation: describe the intent — motion tokens are applied for you.
-->

* Behavior description…

## Constraints

<!--
CONSTRAINTS — the rules. Style, limits, and prohibitions live here.
- Platform specifics (iOS / Android / Web / Responsive), grid, spacing.
- Design system / tokens / guidelines file (a named system, DESIGN.md, tokens.json), or brand colors and type — you can write raw values (#0057FF, 16px) and they're translated to tokens for you.
- Casing, motion rules, localization needs, and any explicit prohibitions ("no modals", "no carousels").
You don't need to be exhaustive: the workflow adds 8px-grid, token, WCAG AA, CTA-hierarchy, and casing defaults automatically — and shows you anything it changes so you can keep your own value.
-->

* Constraint description…
* Additional constraint…

---

## Acceptance Criteria (Optional)

<!--
ACCEPTANCE CRITERIA (optional) — how you'll know the output succeeded. Binary, checkable pass/fail items. Leave this blank to have the workflow derive a starter set from your Elements, Behavior, and Constraints.
-->

- [ ] Criteria one…
- [ ] Criteria two…

## Reference Images (Optional)

<!--
REFERENCE IMAGES (optional) — list any screenshots or inspiration you'll attach, and for each note its role:
- "exact target" — the build should match it
- "inspiration only" — informs style/direction but doesn't dictate the build
The workflow reads each image and flags anything that disagrees with what you wrote above.
-->

* Attachment one…
* 
