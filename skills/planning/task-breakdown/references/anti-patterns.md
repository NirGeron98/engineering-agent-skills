# Task Breakdown — Anti-patterns

## Imagination-driven planning
**Symptom**: "Step 3: update `UserService.updateProfile()`" — a method that doesn't exist.
**Why it fails**: the plan collapses at execution time and trust in all other steps evaporates.
**Instead**: open the file first; plan only against code you've seen.

## The generic to-do list
**Symptom**: "1. Design the schema 2. Implement backend 3. Implement frontend 4. Write tests 5. Deploy."
**Instead**: steps must be specific enough that a different agent could execute them without re-planning.

## Tests as an afterthought
**Symptom**: final step "add tests" — which then gets dropped under time pressure.
**Instead**: each behavior change names its test in the same step.

## Big-bang integration
**Symptom**: five steps that each build an isolated piece, then "step 6: wire everything together" fails for a day.
**Instead**: sequence so every step lands on a working system; wire incrementally.

## Novel architecture in a conventional repo
**Symptom**: planning a new folder structure / abstraction when the repo has an established pattern for this exact feature type.
**Instead**: mirror the closest existing feature; propose deviations explicitly with a reason.

## Question flooding
**Symptom**: opening with eight clarifying questions, half answerable by reading the code.
**Instead**: investigate first; ask at most 2–3 genuinely blocking questions, bundled together.

## Silent scope inflation
**Symptom**: the plan for "add an endpoint" includes migrating the auth library.
**Instead**: adjacent improvements go to the out-of-scope list; the user opts in.

## Plan-mode déjà vu
**Symptom**: the user scoped the task (or a built-in plan mode already produced a plan), and the skill re-runs a full breakdown from scratch — adding latency and no information.
**Why it fails**: the skill's value is investigation receipts and per-step verification, not the ceremony of planning; duplicating an existing plan erodes trust in both.
**Instead**: enrich the existing plan — verify its file references against the code, attach verification to each step, add the out-of-scope list — instead of replacing it.
