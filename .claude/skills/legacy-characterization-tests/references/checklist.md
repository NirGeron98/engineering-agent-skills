# Legacy Characterization Tests Checklist

- [ ] Risky code path, callers, side effects, data dependencies, and change boundary are identified.
- [ ] Existing tests, examples, logs, fixtures, snapshots, or production-safe samples were inspected or requested.
- [ ] Current behavior inventory separates normal, edge, error, side-effect, ordering, and known-bug behavior.
- [ ] Desired future behavior is separated from behavior to preserve.
- [ ] Safest test seams are named with rationale.
- [ ] Snapshot/golden tests are scoped and deterministic.
- [ ] Harness limits and missing behavior evidence are marked pending.
- [ ] First tests include inputs, expected current outputs, and commands.

