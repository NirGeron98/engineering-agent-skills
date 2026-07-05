# Refactor Strategy Checklist

Use this before the final handoff.

- [ ] Defined refactor goal, non-goals, compatibility requirements, owners, and risk tolerance.
- [ ] Inspected current behavior, entry points, callers, tests, contracts, schemas, jobs, UI usage, and deploy/runtime surfaces where relevant.
- [ ] Identified characterization tests or equivalent behavior locks before structural changes.
- [ ] Planned incremental migration slices with verification after each slice.
- [ ] Included compatibility adapters, feature flags, dual paths, or transition boundaries where needed.
- [ ] Included rollback or abort criteria for each risky slice.
- [ ] Included cleanup plan for temporary adapters, old paths, docs, and tests.
- [ ] Avoided big-bang rewrite unless no incremental path exists and documented why.
- [ ] If code, callers, tests, runtime data, or compatibility requirements were missing, emitted exact evidence requests and marked steps pending.
