# To Do List
The following issues were found in the prompt the Gem created.

(No open items.)


## Completed

**Done 2026-06-04 (v2.19.0)**
* Specific references to Tailwind when none should exist (e.g., Claude Code + Figma MCP). Resolved by tech-stack intake (Q1d/Q1e/Q1f) + no-assumed-stack rule: framework-neutral output and generic CSS-variable tokens when the styling system is unknown, with a "to be confirmed by developer" call-out. Figma Make is the only sanctioned implicit default (React + Tailwind + shadcn/ui + Radix, overridable).
* Reference to a "style asset" (DESIGN.md / token JSON) the user hadn't confirmed, plus possible Figma Variables issue. Resolved by Q7 asset-availability confirmation (present-and-ready vs planned → readiness call-out) and a "never invent a style asset the user did not name" rule.
* "Create a design in Figma" assumed for Claude Code + Figma MCP. Resolved by the Q1-Direction gate: (a) build in Figma Design (`use_figma`) vs (b) build code from a Figma reference (`get_design_context`/`get_screenshot`, Q1a-ref). The (b) branch routes into the tech-stack questions and produces read-from-Figma/write-to-code instructions; a developer call-out covers unknown stack details.
* shadcn/ui referenced when the platform was WordPress. Resolved by the same tech-stack intake + no-assumed-stack rule — no library is assumed; the prompt builds from primitives or leaves a call-out when the component library is unspecified.

**Done 2026-06-01 (v2.18.0)**
* Case/capitalization guidelines — sentence-case default, ALL CAPS for overlines via `text-transform`, Q16, Casing Flag, Constraints row, AC item.
* Step-by-step (guided) review of the generated prompt — Phase 3 Generation Mode with per-section approve/revise, then single assembled raw-Markdown block.
* Elevation/shadow token support in Raw Value Translation — `--shadow-*` / `elevation.*` scale, always-present Elevation Constraints row.
* Motion/animation defaults — `--duration-*` / `--easing-*` tokens, reduced-motion requirement, conditional Motion Constraints row.
