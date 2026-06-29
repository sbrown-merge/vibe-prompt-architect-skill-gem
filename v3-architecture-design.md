---
type: Design Document
title: "Vibe Prompt Architect v3.0 — Architecture & Integration Design"
description: Design for the modular sub-skill rearchitecture, the Gem compile model, EARS requirements integration, and OKF output compliance.
tags: [design, architecture, ears, okf, skills, gem]
timestamp: 2026-06-08
status: Draft for review
---

# Vibe Prompt Architect v3.0 — Architecture & Integration Design

This is a design document for review, not an implementation. It covers four coordinated initiatives agreed at kickoff: (1) rearchitect the Skill into a composable, modular package; (2) keep the Gemini Gem as a compiled/flattened artifact built from that source; (3) integrate the EARS requirements syntax for both input and optional output; and (4) make generated outputs compliant with the Google Open Knowledge Format (OKF) so they can contribute to a product knowledge base. Nothing in here changes the user-facing intake or the TC-EBC output *content* except where explicitly noted (EARS and OKF); the rearchitecture itself is behavior-preserving.

## 1. Goals and non-goals

**Goals.** Bring `SKILL.md` under the recommended size by splitting reference material into on-demand files (progressive disclosure); make a few capabilities standalone, reusable sub-skills callable by other skills and agents; preserve the Gemini Gem as a no-install consumer artifact built deterministically from the modular source; let the workflow accept EARS-formatted requirements and optionally emit Behavior and Acceptance Criteria in EARS; and emit every generated prompt as a conformant OKF concept document.

**Non-goals.** No change to the three-phase workflow, the question sequence, the Phase 2 flag set/order, or the TC-EBC section semantics. No move to Gemini CLI in this program (the Gem stays the Gemini target; CLI portability is a side benefit of the open format, noted but not pursued here). No generation of full multi-file OKF *bundles* — each output is a single conformant concept document.

## 2. Current state

`SKILL.md` is a ~1,150-line monolith — roughly twice the ~500-line body that current Claude Skills guidance recommends. The Gemini Gem is three files: `vibe-prompt-architect-gem.md` (a minimal router pasted into the instruction box), `vibe-prompt-architect-knowledge.md` (the full operating procedures, the content sync-partner of `SKILL.md`), and `vibe-prompt-architect-tc-ebc-primer.md` (framework rationale). `SYNC-MANIFEST.md` governs a hand-maintained mirror between `SKILL.md` and `vibe-prompt-architect-knowledge.md`. The workflow already gathers states, triggers, conditional logic, error states, and acceptance criteria, and the generated prompt already carries a human-readable header metadata block — both relevant to EARS and OKF below.

## 3. Target architecture

A three-tier hybrid that fits a linear procedure wrapped around heavy reference material.

```
vibe-prompt-architect/
  SKILL.md                       Orchestrator: persona, three-phase workflow, question
                                 sequence, Phase 2 flag order, principles, generation modes.
                                 Target < 500 lines. References the files below on demand.
  references/
    token-translation.md         Raw Value Translation tables, Grid Conformance, Elevation,
                                 Motion token scales, Guidelines File Support.
    accessibility.md             WCAG 2.2 AA/AAA numeric specs and the documented-exception rule.
    localization.md              L10n/I18n expansion, directionality, format tokens, font stacks.
    figma-mcp.md                 Direction gate, (a) Build-in-Figma + Make-ready construction,
                                 (b) Build-from-Figma-reference, Variables/Library handling.
    output-template.md           The TC-EBC output block + all conditional sections + AC block.
    ears.md                      EARS patterns, the input mapping, and the optional output style.
    okf.md                       OKF frontmatter schema and the field-mapping rules.
  sub-skills/
    design-token-translation/SKILL.md     Standalone: raw value -> semantic token, grid, dual convention.
    wcag-accessibility-constraints/SKILL.md  Standalone: AA/AAA numeric constraints + AC items.
    ears-requirements/SKILL.md            Standalone: read/write EARS; usable outside this workflow.
build/
  build-gem-knowledge.md         The documented flatten procedure (and/or a script) that compiles
                                 the orchestrator + references into the Gem knowledge file(s).
```

Tier 1, the **orchestrator**, keeps the procedural spine only: the persona, the three phases, the question sequence, the Phase 2 flag order, the principles, and the generation-mode logic. It names each reference file and says when to load it.

Tier 2, **reference files**, hold the heavy tables and the output template — content the orchestrator pulls in on demand. This is what gets the body under the size guidance without losing any material.

Tier 3, **sub-skills**, are standalone skills for capabilities worth reusing elsewhere. Initial set: design-token translation, WCAG accessibility constraints, and EARS. Each has its own `SKILL.md` with a discovery description, so other skills and agents (Claude Code, and portably Gemini CLI / Codex / Cursor via the shared format) can invoke them independently. The orchestrator delegates to them rather than duplicating their content.

**Module seams** map cleanly onto today's sections, so R1 is mostly relocation, not rewriting.

## 4. Gem compile model

The Gem cannot run a modular package — it is a static instruction blob plus up to ten knowledge files, with no progressive disclosure or skill activation. So the Gem becomes a **build artifact**, not a peer.

- **Source of truth:** the modular skill package (orchestrator + references + sub-skills).
- **Build step:** `build/build-gem-knowledge` concatenates the orchestrator body and the reference files in workflow order, inlines the content of the referenced sub-skills, strips Claude-only mechanics (e.g., progressive-disclosure "load this file" directions), and applies the Gem save-filter-safe formatting we established earlier (no four-backtick fences; conditional markers as `*[Include when…]*` rather than HTML comments; no execution-adjacent imperative phrasing in the router). Output: **one or two** flattened knowledge files, well under the ten-file cap.
- **Unchanged:** the router (`vibe-prompt-architect-gem.md`) stays a hand-maintained minimal file. The TC-EBC primer can remain a separate knowledge file or be folded into the flatten — open decision (see §10).
- **Determinism:** running the build twice on the same source yields the same artifact; the acceptance test for R1 is that the artifact is content-equivalent to today's `vibe-prompt-architect-knowledge.md` (differences cosmetic only).

This converts the SYNC-MANIFEST premise from "keep two files mirrored by hand" to "compile the Gem artifact from the source modules" — fewer opportunities for drift, at the cost of a build step (manual procedure first; optional script later).

## 5. EARS integration (input + optional output)

EARS and TC-EBC operate at different altitudes: EARS is a per-sentence syntax; TC-EBC is the document structure. EARS lives inside the behavioral and conditional parts of TC-EBC and does not touch Task, Context, or visual Constraints.

**The "system" noun.** EARS requires a system name in every requirement (`the <system> shall …`). For this workflow the system is the screen or component being built. The EARS module defines this once and uses it consistently.

**Pattern-to-section mapping.**

| EARS pattern | Syntax | Routed into |
|---|---|---|
| Ubiquitous | `The <screen> shall <response>` | Constraints (always-true rules) |
| State-driven | `While <state>, the <screen> shall <response>` | Behavior (states) |
| Event-driven | `When <trigger>, the <screen> shall <response>` | Behavior (interactions) |
| Unwanted | `If <trigger>, then the <screen> shall <response>` | Behavior (error/empty states — pairs with the States flag) |
| Optional feature | `Where <feature>, the <screen> shall <response>` | Elements (conditional presence) / Constraints |
| Complex | `While …, when …, the <screen> shall …` | Behavior |

**E1 — EARS as input.** The workflow recognizes EARS-syntax lines wherever requirements arrive (pasted, attached, or in the TC-EBC template), routes each into the correct TC-EBC section by keyword, and preserves the exact phrasing rather than paraphrasing it. The TC-EBC template gains an optional EARS affordance in the Behavior and Acceptance Criteria sections (a comment showing the five patterns). No change to how non-EARS input is handled.

**E2 — EARS as output (optional).** A new Phase 3 generation option (alongside full-prompt and guided-review) emits the Behavior section as EARS requirements and derives each Acceptance Criterion directly from a requirement (EARS requirements are testable by construction, so the mapping is ~1:1). It stays off for quick mockups and is offered, not forced — consistent with the workflow's other progressive features. Visual Constraints, Task, and Context remain prose.

**Acceptance Criteria synergy.** Because an EARS requirement names a trigger/state and a single observable response, each maps to one binary AC item. When EARS output is on, the AC block is generated from the Behavior requirements instead of being authored separately.

## 6. OKF output compliance

Goal: every generated prompt is a conformant OKF v0.1 concept document, so it can drop into the product knowledge catalog. Conformance is low-bar — parseable YAML frontmatter with a non-empty `type` — and aligns with the frontmatter already added to the TC-EBC input template.

**Frontmatter emitted on every generated prompt:**

```yaml
---
type: <e.g., "UI Screen Spec" — see §10 open decision>
title: <screen/component name, derived from Task>
description: <the one-sentence Task>
resource: <Figma file URL (Figma MCP) or repo/path when known; omit otherwise>
tags: [<platform>, <ui-type>, <scope>, wcag-<aa|aaa>, <locales…>]
timestamp: <ISO 8601; see §10 open decision on time>
okf_version: "0.1"
---
```

**Relationship to the existing header block.** Today's human-readable header (Target platform, Tech stack, Scope, Prototype type, Accessibility, L10n) overlaps with these tags. Proposal: the YAML frontmatter becomes the machine-readable source of record, and the human header is kept but trimmed to avoid duplication (or rendered from the same values). This is a design choice flagged in §10.

**Single concept, not a bundle.** Each output is one OKF concept document. We do not generate `index.md`/`log.md` or multi-file bundles — assembling concepts into a catalog bundle is the consuming system's job. The doc can note the OKF concept-ID convention (path minus `.md`) and absolute-link style so authors know how it slots in, without the workflow having to manage a bundle.

**Body.** The TC-EBC body is already structural markdown (headings, lists, tables), which is exactly what OKF prefers. EARS-phrased Behavior strengthens this. No structural change required beyond the frontmatter.

**Delivery rule interaction.** The Output Delivery Rule (raw Markdown in a fenced code block, copied verbatim) is unaffected — the frontmatter sits inside that block. Worth confirming the fenced-code presentation still reads cleanly with a leading `---` frontmatter.

**OKF as a module.** This is small enough to live as `references/okf.md` plus a few lines in the output template; it does not need a sub-skill.

## 7. Sync and build model

`SYNC-MANIFEST.md` is rewritten around source -> build:

- The **anatomy map** describes modules and which reference file owns each former section, rather than a SKILL.md <-> knowledge.md row-by-row mirror.
- The **feature touch map** points at the owning module instead of "both files."
- A **build section** documents how to regenerate the Gem knowledge file(s) from source and how to verify equivalence.
- The **pre-commit checklist** gains "rebuild the Gem artifact and confirm it reflects the module changes."
- Intentional-differences and health-log carry forward.

The reconstruction brief — the rebuild authority — gets the largest doc update, since the file set and the build pipeline both change.

## 8. Versioning and phases

This is a 3.x program; the rearchitecture is the 3.0.0 milestone (a structural/distribution change, even though R1 preserves behavior). Phases are checkpointed like the prior audit.

| Phase | Scope | Acceptance | Version |
|---|---|---|---|
| R1 | Refactor skeleton: orchestrator + reference files; move content verbatim; stand up sub-skill stubs | Compiled Gem artifact content-equivalent to today's `knowledge.md`; Skill drives identical intake/output | 3.0.0 |
| R2 | Compile pipeline: documented flatten (+ optional script); SYNC-MANIFEST rewrite | Build reproduces the Gem knowledge file deterministically | 3.0.0 |
| E1 | EARS input: recognize + route; template affordance | Pasted EARS lands in correct sections without paraphrase loss | 3.1.0 |
| O1 | OKF output: frontmatter on generated prompts; header dedupe; template frontmatter aligned to OKF | Output validates as OKF v0.1 (frontmatter + non-empty `type`) | 3.2.0 |
| E2 | EARS output: optional Behavior/AC-in-EARS generation mode | Generated Behavior reads as valid EARS; AC maps 1:1 | 3.3.0 |
| D | Docs woven across phases: README, CHEATSHEET, reconstruction brief, CHANGELOG, gem-description | Docs describe the modular package, build model, EARS, and OKF accurately | per phase |

Sequencing rationale: R1/R2 first (behavior-preserving, lowest risk, proves the pipeline); then EARS-input and OKF-output (both small, independent, high value); EARS-output last (largest design surface). O1 before E2 so OKF framing is in place when EARS output lands.

## 9. Risks and mitigations

- **Drift during the move (highest).** Mitigated by R1 being verbatim relocation plus an equivalence diff of the compiled artifact against today's `knowledge.md`.
- **Gem save-filter regressions.** The build must reproduce the safe formatting; add a build check for four-backtick fences, HTML comments, and execution-adjacent router phrasing.
- **SYNC-MANIFEST inversion churn.** Treat the manifest rewrite as part of R2, not an afterthought.
- **OKF timestamp/time access.** The model cannot reliably know the current time; see §10.
- **Scope.** Larger than the entire prior audit; strictly phase-gated with review checkpoints.
- **Sub-skill boundaries leaking.** Keep sub-skills genuinely self-contained (no hidden dependence on orchestrator state) so they're reusable; verify by invoking one in isolation.

## 10. Open decisions (need your input before/while building)

1. **OKF `type` value.** Proposed `UI Screen Spec`. Alternatives: `Vibe-Coding Prompt`, `Screen Specification`, `UI Requirement`. Which fits your catalog's taxonomy?
2. **`timestamp` handling.** The skill can't read the clock reliably. Options: (a) stamp with the session date when the environment provides it; (b) emit a `timestamp:` placeholder for the user/tooling to fill; (c) instruct the receiving environment to set it. Lean (a) with (b) fallback.
3. **`resource` field.** Use the Figma file URL on the Figma-MCP path, a repo/path for code targets, and omit otherwise — confirm, or specify a different URI convention for your catalog.
4. **Frontmatter vs. human header.** Make YAML frontmatter the source of record and trim the human header to avoid duplication, or keep both in full? Lean trim-and-dedupe.
5. **Sub-skill set.** Start with token-translation, accessibility, and EARS as standalone sub-skills; everything else as reference files. Add or remove any?
6. **TC-EBC primer in the Gem.** Keep it a separate knowledge file, or fold it into the flattened build? Lean keep-separate (it's conceptual, stable, and distinct).
7. **Build mechanism.** Documented manual flatten first, with an optional script later — or script from the start? Lean manual-first to avoid over-engineering before the module boundaries settle.
8. **Versioning call.** Tag the rearchitecture 3.0.0 as proposed, or keep it in the 2.x line since R1 preserves user-facing behavior? Lean 3.0.0 (it re-platforms the package and the build/sync model).
