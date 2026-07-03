---
name: module-boundary-review
description: Review whether modules, packages, services, layers, or components have clear responsibilities and healthy boundaries. Use when ownership, public interfaces, imports, leaked abstractions, shared utilities, domain boundaries, circular or unstable boundaries, test seams, or deployment boundaries need a boundary review with proposed changes and safe migration steps.
---

# Module Boundary Review

## Goal

Identify unclear responsibilities and boundary violations, then propose focused boundary changes that improve ownership, testability, and change safety without redesigning the whole system.

## When to use

- A module, package, layer, service, or component imports too much or owns unclear responsibilities.
- Public interfaces, shared utilities, domain boundaries, or test seams are hard to reason about.
- The user wants boundary health, not general code review.

## When not to use

- The concern is a reusable frontend component API; use `skills/frontend/component-design-review`.
- The concern is runtime service behavior; use `skills/api-and-backend/service-debugging`.
- The concern is dependency graph depth or circular imports; use `dependency-and-coupling-review`.

## Composition and handoff rules

- Hand component-level frontend design to `skills/frontend/component-design-review`.
- Hand service behavior debugging to `skills/api-and-backend/service-debugging`.
- Hand dependency graph and coupling depth to `dependency-and-coupling-review`.

## Evidence access rule

Inspect actual modules, imports, public interfaces, callers, tests, ownership docs, and deployment boundaries before judging boundary health. If the dependency graph or code access is missing, request exact commands/artifacts and mark conclusions pending.

## Workflow

1. Define the boundary under review and the expected responsibility from docs, naming, callers, and domain language.
2. Inspect module contents, imports, exported interfaces, callers, tests, shared utilities, and ownership/deployment notes.
3. Classify responsibilities: core, adjacent, misplaced, duplicated, or unclear.
4. Identify boundary violations: leaked internals, circular knowledge, cross-layer calls, unstable public APIs, test-only seams, and shared utility sprawl.
5. Propose boundary changes with migration steps, compatibility notes, and verification checks.
6. Before final handoff, load `references/checklist.md`; consult `references/anti-patterns.md` when tempted to split by taste, hide coupling behind interfaces, or move everything at once.

## Questions to ask (only when necessary)

- Which team or subsystem owns the boundary if ownership is undocumented?

## Quality bar

- Findings cite concrete files, imports, interfaces, callers, or tests.
- Proposed changes are smaller than a rewrite and include migration safety.
- Public interface and ownership impact are explicit.

## Expected output format

```markdown
## Module boundary review: <boundary>

**Evidence reviewed**: <files/import graph/docs/tests>
**Current responsibility**: <observed responsibility>
**Boundary findings**
- <severity> - <violation> - evidence: <source> - impact: <impact>

**Proposed boundary changes**: <changes>
**Safe migration steps**: <ordered steps>
**Verification**: <tests/graph checks/caller checks>
**Pending evidence**: <items>
```

## Failure modes to avoid

- **Abstraction by taste**: moving code because it feels cleaner without showing change-safety or ownership benefit.
- **Interface paint**: adding an interface while the same hidden coupling remains.
- **Boundary big bang**: proposing a large split without compatibility and migration steps.

More in `references/anti-patterns.md`.
