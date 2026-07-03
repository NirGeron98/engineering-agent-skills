---
name: refactor-strategy
description: Plan a nontrivial refactor that must preserve behavior and avoid big-bang risk. Use when current behavior, characterization tests, compatibility, incremental migration slices, temporary adapters, rollback, validation, ownership, cleanup, and risk need a safe refactor plan; defer root-cause debugging, code review, debt prioritization, or ADR creation to their specialist skills.
---

# Refactor Strategy

## Goal

Create a behavior-preserving refactor plan with characterization tests, incremental slices, compatibility strategy, rollback path, and cleanup plan.

## When to use

- A refactor spans important modules, APIs, jobs, UI, tests, schemas, or deployment surfaces.
- The user needs migration sequencing and risk control before editing.
- Existing behavior must remain compatible during the transition.

## When not to use

- The root cause of a bug is unknown; use `skills/debugging/diagnose-bug`.
- The request is PR review; use `skills/review/code-review`.
- The team needs debt prioritization first; use `technical-debt-triage`.
- The refactor depends on a durable decision; use `architecture-decision-record`.

## Composition and handoff rules

- Hand bug root-cause work to `skills/debugging/diagnose-bug`.
- Hand code review to `skills/review/code-review`.
- Hand technical debt prioritization to `technical-debt-triage`.
- Hand durable decision capture to `architecture-decision-record`.

## Evidence access rule

Do not propose a refactor plan without inspecting current behavior, callers, tests, contracts, migrations, and deploy/runtime surfaces. If evidence is unavailable, request exact file lists, commands, test results, or traces and mark dependent steps pending.

## Workflow

1. Define the refactor goal, non-goals, compatibility requirements, owners, and risk tolerance.
2. Inspect current behavior: entry points, callers, tests, contracts, schemas, jobs, UI usage, deployment surfaces, and data migrations.
3. Identify characterization tests and observability needed before changes.
4. Plan incremental slices with compatibility adapters, feature flags or dual paths if needed, verification per slice, and rollback options.
5. Plan cleanup: remove adapters, delete old paths, update docs, and prevent reintroduction.
6. Before final handoff, load `references/checklist.md`; consult `references/anti-patterns.md` when tempted to rewrite, skip characterization tests, or bundle cleanup with risky migration.

## Questions to ask (only when necessary)

- What compatibility window or release sequence must be honored?
- Which behavior is intentionally allowed to change, if any?

## Quality bar

- Current behavior and callers are evidenced before migration steps.
- Each slice has verification and rollback/abort criteria.
- The plan avoids big-bang changes unless no incremental path exists and says why.

## Expected output format

```markdown
## Refactor strategy: <scope>

**Goal and non-goals**: <summary>
**Evidence reviewed**: <files/tests/callers/contracts/runtime surfaces>
**Behavior to preserve**: <current behavior and characterization tests>

**Migration slices**
- <slice> - change: <change> - verification: <check> - rollback/abort: <condition>

**Compatibility strategy**: <adapters/flags/dual paths/contracts>
**Cleanup plan**: <old paths/docs/tests to remove>
**Risks and pending evidence**: <items>
```

## Failure modes to avoid

- **Rewrite as strategy**: replacing migration planning with a broad rewrite.
- **No characterization net**: changing structure before current behavior is captured.
- **Cleanup collision**: combining risky migration and cleanup so failures are hard to isolate.

More in `references/anti-patterns.md`.
