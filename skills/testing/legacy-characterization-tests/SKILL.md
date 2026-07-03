---
name: legacy-characterization-tests
description: Capture current behavior in legacy or poorly tested code before refactoring or risky change. Use when seams, golden tests, approval-style checks, snapshots, high-risk paths, and harness limitations must be turned into a characterization test plan and first safe tests; defer refactor sequencing to refactor-strategy.
---

# Legacy Characterization Tests

## Goal

Preserve current behavior in poorly tested code by identifying safe seams and writing characterization tests that make future refactors or fixes less risky.

## When to use

- Legacy or poorly tested code must be refactored, migrated, or changed safely.
- Current behavior is relied on but undocumented.
- The user needs initial tests before modifying risky code.

## When not to use

- The refactor sequence is the main task; use `skills/architecture/refactor-strategy`.
- A bug root cause is unknown; use `skills/debugging/diagnose-bug`.
- The user needs a whole-system testing plan; use `skills/testing/test-strategy`.

## Composition and handoff rules

- Hand refactor sequencing to `skills/architecture/refactor-strategy`.
- Hand bug diagnosis to `skills/debugging/diagnose-bug`.
- Hand broad testing plans to `skills/testing/test-strategy`.

## Evidence access rule

Inspect current behavior, callers, tests, inputs/outputs, fixtures, persisted examples, and side effects before proposing characterization tests. If behavior evidence cannot be run, request exact commands, sample inputs, logs, fixtures, or approval outputs and mark behavior claims pending.

## Workflow

1. Identify the risky code path, callers, side effects, data dependencies, and intended refactor/change boundary.
2. Inspect existing tests, production examples, logs, snapshots, fixtures, and stable public seams.
3. Inventory current behavior: normal cases, edge cases, errors, formats, ordering, side effects, and known bugs that must not be accidentally changed.
4. Choose the safest seam for first tests: public API, service boundary, CLI, rendered output, database result, or golden file.
5. Design initial characterization tests with snapshot/golden caution, determinism controls, and harness limitations. When correctness of an observed behavior is ambiguous or suspicious, default to preserve-and-label rather than silently deciding it is a bug or silently fixing it.
6. Before final handoff, load `references/checklist.md`; consult `references/anti-patterns.md` when tempted to rewrite first, snapshot everything, or "fix" behavior during capture.

## Questions to ask (only when necessary)

- Which current odd behaviors are known and intentionally preserved for compatibility?
- Are there fixtures or production examples that can be safely used as golden inputs?

## Quality bar

- Current behavior is separated from desired future behavior.
- First tests use stable seams and deterministic inputs.
- Harness gaps and pending behavior evidence are explicit.

## Expected output format

```markdown
## Characterization test plan: <scope>

**Evidence reviewed**: <callers/tests/examples/outputs>
**Behavior inventory**: <preserve/change/pending>
**Safest seams**: <where to test and why>
**Recommended first tests**: <test - input - expected current output - command>
**Harness limits and pending evidence**: <items>
```

## Failure modes to avoid

- **Refactor before capture**: changing structure before behavior is locked down.
- **Snapshot sprawl**: snapshotting large unstable output without intent.
- **Bug laundering**: silently preserving or fixing suspected bugs without labeling them.

More in `references/anti-patterns.md`.
