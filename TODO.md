# To Do List

**Added 2026-05-21**
* Add refinement to ask the user about Case guidelines. What should our general case rules be? When do we use Title Case? When do we use Sentence case? Do we ever use ALL CAPS or all lower case?
* Add an improvement to do a step-by-step review of the created prompt asking for approval of each step before proceeding to the next step.

**Added 2026-06-01**
* Add elevation/shadow token support to Raw Value Translation. Color, typography, spacing, and radius are tokenised on intake, but elevation/depth is not — it only appears as a Make Kit / DESIGN.md body topic. Define a semantic elevation scale (e.g. `--shadow-sm/md/lg` / `elevation.*`) and detect raw `box-shadow` values.
* Add motion/animation defaults. Behavior captures states and transitions, but there are no built-in duration/easing token defaults. Define standard motion tokens (e.g. `--duration-fast/base/slow`, `--easing-standard`) and reference them when behaviors specify transitions.
* 