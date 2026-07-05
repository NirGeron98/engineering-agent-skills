# Refactor Strategy Anti-patterns

## Rewrite as strategy

- **Symptom**: The plan is mostly "replace the old system with the new one."
- **Why it matters**: It hides compatibility, verification, and rollback risk.
- **Correction**: Slice the migration and preserve behavior through adapters or dual paths where possible.

## No characterization net

- **Symptom**: Structural changes begin before current behavior is captured.
- **Why it matters**: Regressions become indistinguishable from intended changes.
- **Correction**: Add or identify characterization tests, golden outputs, contract tests, or manual checks first.

## Cleanup collision

- **Symptom**: Migration, cleanup, renames, and behavior changes happen together.
- **Why it matters**: Failures are hard to isolate and rollback.
- **Correction**: Separate behavior-preserving migration from cleanup and intentional behavior changes.

## Big-bang flag flip

- **Symptom**: The whole refactor ships behind one switch with no partial verification.
- **Why it matters**: The rollback path is coarse and confidence is low.
- **Correction**: Define slice-level verification and progressive rollout.

## Compatibility amnesia

- **Symptom**: Existing callers, jobs, UI, tests, or persisted data are not included.
- **Why it matters**: Refactors often fail at the edges.
- **Correction**: Inventory all consumers and contracts before sequencing changes.
