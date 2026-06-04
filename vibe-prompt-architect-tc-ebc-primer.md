# The TC-EBC Framework — Origin, Concept, and Importance

## What TC-EBC Is

The TC-EBC Framework is a structured prompt architecture for AI-powered UI generation tools. Developed by designer Greg Huntoon and published on the Figma Blog, it provides a repeatable five-part structure that converts a designer's mental vision into a precise, executable AI instruction set.

**TC-EBC stands for:** Task · Context · Elements · Behavior · Constraints

The framework addresses a fundamental problem: AI generative tools are trained on what already exists, not on the unique vision in a designer's head. When designers describe what they want in natural, intuitive language, the gap between their high-resolution mental model and the lower-resolution result of a vague prompt produces inconsistent, unpredictable output. TC-EBC closes that gap by making the designer's specification explicit and complete before any generation happens.

---

## The Core Insight: Visual Literacy as a Design Discipline

Huntoon's key insight is that effective AI prompting requires **visual literacy** — the ability to decompose a design vision into its constituent parts. This is not an intuitive skill. It is a learnable, repeatable discipline analogous to writing a design spec or a component API contract.

The **cooking metaphor** makes this concrete: vague instructions ("make me a fancy breakfast") produce arbitrary results. Specific ingredients plus constraints ("sourdough, avocado, poached egg, no dairy, chili flakes on top") produce exactly what was intended. Prompting works the same way. The designer specifies the ingredients (components, tokens, data), the technique (how to assemble them), and the rules (platform, grid, brand constraints). The AI executes within that specification.

---

## The Five Components

### Task
One clear sentence stating exactly what the AI should build. No ambiguity, no style adjectives. The Task gives the AI a concrete, unambiguous goal.

*Why it matters:* Without a precise Task, the AI infers intent from context — and it will infer wrong. "Create a mobile onboarding screen for a fitness tracking app" is unambiguous. "Design something for onboarding" is not.

### Context
Two to four sentences placing the screen in its product flow. Who is the user? What happened before they arrived on this screen? What do they do next?

*Why it matters:* AI has no knowledge of your product's state, user journey, or design intent. Context is how you supply it. A checkout screen for a first-time guest has different design requirements than the same screen for a returning logged-in customer. Context is what tells the AI which one you mean.

### Elements
An exhaustive list of the specific UI components and content that must appear. Not described by appearance — described by name and purpose.

*Why it matters:* If a component is not listed, the AI may omit it or hallucinate a substitute. Elements is the ingredient list. Completeness here directly determines completeness of the output. Anything not listed may be dropped or replaced without warning.

### Behavior
All interactive logic: states, transitions, conditional rules, and animations. What happens when the user taps, swipes, submits, or encounters an error?

*Why it matters:* Behavior is where static mockups become functional prototypes. Without Behavior, the AI produces screens that look right but don't work right. This section is especially critical for tools that generate interactive prototypes (Figma Make, Lovable, v0, Claude Code, etc.). Most vague prompts describe appearance but omit logic — filling in Behavior is usually where the largest output quality gains are found.

### Constraints
The non-negotiable rules: platform, grid system, accessibility requirements, design tokens, and explicit prohibitions.

*Why it matters:* Constraints prevent the AI from freelancing on design decisions. An unconstrained AI makes its own choices about color, spacing, type scale, and hierarchy — choices that may not match your design system. Constraints enforce brand and accessibility standards by referencing your actual tokens rather than allowing the AI to invent substitutes.

---

## Why the Framework Produces Better AI Output

**It eliminates ambiguity at the input.** Each section addresses a distinct category of information the AI needs. A prompt that fills all five sections leaves little for the AI to guess.

**It creates accountable iteration.** When output is wrong, the designer knows exactly which section to fix. Wrong behavior → Behavior section was incomplete. Missing components → Elements section was incomplete. TC-EBC makes refinement systematic instead of trial-and-error.

**It separates concerns.** Appearance belongs in Constraints (as token names, not raw values). Structure belongs in Elements. Logic belongs in Behavior. Mixing these produces prompts that are hard to read and harder to diagnose.

**It scales with design systems.** The Constraints section is where component libraries, design tokens, and accessibility standards are cited. Referencing a design system forces the AI to use those ingredients rather than inventing its own. This is the framework's most powerful leverage point for brand consistency and output fidelity.

---

## The Author Intent Principle

Huntoon frames AI-assisted design as a shift from "maker to curator" — more precisely, to **author**. The designer's vision is the high-resolution source; the prompt is the code used to execute it.

This reframing is important: TC-EBC is not a shortcut. It is a more rigorous form of design specification. Writing a good TC-EBC prompt requires the same decomposition skill that writing a good design spec has always required. What changes is the audience — instead of communicating intent to a developer, you are communicating intent to a generation system.

The framework works because it respects the AI's actual strengths and limitations. AI executes well within a well-defined space. It infers poorly when intent is unstated. TC-EBC defines the space precisely, then lets the AI execute within it.

---

## Practical Principles Derived from the Framework

- **One screen at a time.** Complex requests degrade output quality. Prompt for one screen, verify the result, then chain to the next.
- **Prompt drift is real.** In metered tools like Figma Make, every rerun costs credits. Refine the prompt in a text editor before running it — not after.
- **Design systems as ingredients.** Reference your component library and token names in Constraints. This forces the AI to cook from your recipe, not invent its own.
- **Elements must be exhaustive.** The AI will not add what you forgot to list. Treat the Elements section as a complete manifest, not a highlights reel.
- **Behavior is usually the gap.** Most vague prompts describe what a screen looks like, not how it works. The Behavior section is where the most quality is left on the table by underspecified prompts.
- **Constraints are rules, not suggestions.** Stated constraints are treated as hard requirements. Unstated preferences are treated as AI discretion. If it matters, it must be in Constraints.
