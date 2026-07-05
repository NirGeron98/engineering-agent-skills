---
name: integration-test-design
description: Design tests across component or system boundaries such as API+DB, service+queue, frontend+API mock, or data pipeline stages. Use when dependencies, fixtures, contracts, cleanup, retries, idempotency, determinism, CI cost, and diagnostics need an integration test design and verification commands.
---

# Integration Test Design

## Goal

Design boundary tests that prove important interactions work while keeping fixtures, dependencies, cleanup, cost, and diagnostics reliable enough for CI.

## When to use

- A behavior crosses a boundary: API/database, service/queue, frontend/API mock, worker/storage, or pipeline stage.
- Existing tests miss contract, persistence, retry, cleanup, or dependency behavior.
- The user needs a test design rather than immediate root-cause debugging.

## When not to use

- API contract design itself needs review; use `.claude/skills/api-design-review`.
- Data pipeline architecture is the main task; use `.claude/skills/db-to-dwh-pipeline`.
- Browser/user-flow E2E failure is the main symptom; use `.claude/skills/e2e-test-debugging`.

## Composition and handoff rules

- Hand API contract judgment to `.claude/skills/api-design-review`.
- Hand data pipeline behavior to `.claude/skills/db-to-dwh-pipeline`.
- Hand E2E browser debugging to `.claude/skills/e2e-test-debugging`.

## Evidence access rule

Inspect both sides of the boundary, existing test harnesses, fixture setup, cleanup mechanisms, CI constraints, and failure diagnostics before designing tests. If unavailable, request exact files, commands, service contracts, logs, or CI timings and mark dependent choices pending.

## Workflow

1. Define the boundary under test, contract, success criteria, failure modes, and out-of-scope dependencies.
2. Inspect existing integration tests, helpers, fixtures, containers/mocks, cleanup, retries, idempotency, and CI runtime.
3. Decide real vs fake dependencies and justify each choice by risk, determinism, cost, and diagnostic value.
4. Design test cases for happy path, contract mismatch, retry/idempotency, persistence, cleanup, and representative failures.
5. Specify data/fixture lifecycle, isolation, concurrency, teardown, and exact verification commands.
6. Before final handoff, load `references/checklist.md`; consult `references/anti-patterns.md` when tempted to mock the boundary under test, use shared mutable fixtures, or ignore cleanup.

## Questions to ask (only when necessary)

- Which external dependency may not be run in local or CI environments?
- What maximum CI runtime is acceptable for this test layer?

## Quality bar

- The chosen dependency strategy is justified, not assumed.
- Tests are deterministic, isolated, and diagnostic when they fail.
- Cleanup and idempotency are part of the design.

## Expected output format

```markdown
## Integration test design: <boundary>

**Boundary and contract**: <what crosses the boundary>
**Evidence reviewed**: <code/tests/fixtures/CI>
**Dependency strategy**: <real/fake and rationale>
**Fixture and data plan**: <setup/isolation/cleanup>
**Recommended test cases**: <case - risk - command - expected evidence>
**Reliability risks**: <determinism/CI/cost/pending evidence>
```

## Failure modes to avoid

- **Mocked integration**: mocking the very boundary the test is meant to prove.
- **Shared-state fixture**: tests pass alone but fail in order/concurrency.
- **No failure diagnostics**: a failing test says "timeout" without exposing the boundary evidence.

More in `references/anti-patterns.md`.
