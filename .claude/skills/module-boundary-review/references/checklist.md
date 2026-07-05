# Module Boundary Review Checklist

Use this before the final handoff.

- [ ] Defined the boundary under review and the expected responsibility.
- [ ] Inspected module contents, imports, exports/public interfaces, callers, and tests.
- [ ] Checked ownership docs, domain language, and deployment boundaries where relevant.
- [ ] Classified responsibilities as core, adjacent, misplaced, duplicated, or unclear.
- [ ] Identified leaked abstractions, cross-layer calls, unstable APIs, circular knowledge, and shared utility sprawl.
- [ ] Reviewed test seams and whether tests depend on internals.
- [ ] Proposed boundary changes with migration steps and compatibility notes.
- [ ] Avoided turning the review into broad code review or frontend component API review.
- [ ] If code, dependency graph, caller list, ownership docs, or runtime boundaries were missing, emitted exact evidence requests and marked conclusions pending.
