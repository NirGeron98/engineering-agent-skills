---
name: code-review
description: Review a diff, branch, or PR for correctness bugs, contract breaks, missing tests, and convention violations — with every finding verified against the actual code, ranked by severity. Use when asked to review changes, before merging your own nontrivial work, or when handed a PR link/branch. Produces a findings list with file:line references and concrete failure scenarios, not style nitpicks or vague concerns.
---

# Code Review

## Goal

Find the defects in a change that would matter in production — correctness bugs, broken contracts, missing edge cases, absent tests — and report them with evidence, so the author can act on each finding without re-investigating.

## When to use

- Asked to review a PR, branch, or diff.
- You just completed nontrivial changes and are about to hand them off or merge.
- A teammate's change touches code you're responsible for.

## When not to use

- Formatting/lint concerns a machine already enforces — don't spend review on them.
- Architecture debates about code that isn't in the diff (raise separately).
- Generated or vendored code, unless the generator config changed.
- The review question is the *shape of an API contract* (naming, pagination, error semantics, compatibility) — defer to `api-design-review` for depth; in a mixed diff, still review the endpoint's implementation here and flag the contract-shape questions for hand-off.
- The review question is *who can do what* — access control, token lifecycle, IDOR — defer to `auth-flow-review` for depth; here, flag the gap and hand off.
- The finding is about system-level structure — module boundaries, coupling, ownership, or an architectural tradeoff — flag it and defer to `skills/architecture/architecture-review` rather than resolving it here.

## Workflow

1. **Establish what the change claims to do.** Read the PR description, commit messages, and linked issue. The review question is: does the diff do this, only this, and safely?
2. **Read the full diff once for shape.** Note the surface: public APIs, schemas, error paths, concurrency, permissions, and anything deleted. Diff against the PR's base branch, not stale local state; skip generated files and lockfiles. If the diff is too large to review in one pass, partition it by risk (contracts → state → error paths → the rest) and say explicitly which parts got lighter coverage.
3. **Read changed code in its real context.** For each significant hunk, open the surrounding file — callers, error handling above/below, invariants. A diff line is only judgeable inside its function and its callers.
4. **Hunt in the high-yield zones**, in order:
   - Contract changes: API shapes, DB schema, event formats, serialization — who consumes these and are they updated?
   - Boundaries: null/empty/zero, off-by-one, timezone, encoding, pagination edges.
   - Error paths: what happens on failure — swallowed, retried, half-committed?
   - State & concurrency: shared mutation, transactions, idempotency, ordering.
   - Security: input validation, authz on new endpoints, secrets, injection.
5. **Verify every suspected finding before reporting.** Trace the failing input through the code or run the test that would expose it. A finding you can't articulate a failure scenario for is not a finding — drop it or mark it as a question.
6. **Check the tests.** Do added tests fail without the change? Are the risky paths from step 4 covered? Missing coverage for a changed behavior is itself a finding.
7. **Check for what's absent.** Migrations without rollback, endpoints without authz, config referenced but not added, docs/OpenAPI now stale.
8. **Report** in the output format: findings ranked by severity, each with location, failure scenario, and suggested direction. Separate "must fix" from "consider". State explicitly what you checked and found clean. Before reporting, load `references/checklist.md` and confirm every section was covered; consult `references/anti-patterns.md` if unsure whether a finding meets the bar.

## Questions to ask (only when necessary)

- Intent, when the diff and its description conflict — which is right?
- Whether a behavior change visible in the diff is deliberate (silently changed default, removed validation).

Never ask the author to explain code you can read yourself.

## Quality bar

- Every finding cites file:line and a concrete failure scenario ("empty cart → division by zero at checkout.ts:88").
- Findings verified by tracing or execution, not pattern-matched ("this looks like it could race" is below the bar).
- Severity ranking honest: blocking correctness issues first, preferences clearly labeled as preferences.
- Clean areas reported ("checked authz on both new endpoints — correct") so absence of findings is informative.
- Zero style nitpicks that a formatter/linter owns.

## Expected output format

```markdown
## Review: <branch/PR — one-line change summary>

**Verdict**: <approve / approve with fixes / needs changes>

**Must fix**
1. <finding> — <file:line> — failure: <concrete scenario> — suggest: <direction>

**Consider** (non-blocking)
- <finding> — <file:line> — <why it matters>

**Test gaps**
- <changed behavior without coverage>

**Checked and clean**
- <risk area inspected, no issue>
```

## Failure modes to avoid

- **Vibe findings**: "this might have concurrency issues" with no scenario. Verify or drop.
- **Diff-only myopia**: approving a hunk that's fine in isolation but breaks its callers — read the context.
- **Silent clean bill**: "LGTM" with no record of what was actually examined.

More in `references/anti-patterns.md`.
