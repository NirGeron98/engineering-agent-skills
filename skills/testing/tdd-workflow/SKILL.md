---
name: tdd-workflow
description: Drive a code change through a test-first red/green/refactor loop. Use when a bug fix, feature slice, or behavior change should start with a minimal failing test, proceed only after the failure is observed, and produce a TDD implementation plan plus verification summary; not for broad coverage strategy or unknown root-cause debugging.
---

# TDD Workflow

## Goal

Implement a small behavior change by proving the missing behavior with a failing test first, making the minimal code change to pass, then refactoring only after green verification.

## When to use

- The user asks to implement a feature or bug fix test-first.
- A regression test should be added before fixing a known defect.
- A change can be sliced around externally observable behavior.

## When not to use

- The bug is not understood; use `skills/debugging/diagnose-bug` first.
- The user needs a broad coverage plan; use `skills/testing/test-strategy`.
- Legacy behavior must be captured before refactor; use `skills/testing/legacy-characterization-tests`.

## Composition and handoff rules

- Hand root-cause diagnosis to `skills/debugging/diagnose-bug` before writing tests when the cause is unknown.
- Hand broader test planning to `skills/testing/test-strategy`.
- Hand legacy behavior capture to `skills/testing/legacy-characterization-tests`.

## Evidence access rule

Inspect the public interface, closest existing tests, test commands, and behavior evidence before writing or proposing a test. If the test cannot be run, output the exact command to run and mark red/green conclusions pending.

## Workflow

1. Define the smallest observable behavior change and public interface to exercise.
2. Inspect existing test style, fixtures, factories, helpers, and the command that runs the narrowest relevant test.
3. Write or propose the minimal failing test, avoiding implementation details and broad setup.
4. Observe or request the red result before changing implementation.
5. Implement the smallest change that makes the test pass; then run the focused test and relevant regression suite.
6. Refactor only after green, keeping behavior stable and recording verification.
7. Before final handoff, load `references/checklist.md`; consult `references/anti-patterns.md` when tempted to write implementation first, test internals, or skip the red result.

## Questions to ask (only when necessary)

- Is any behavior intentionally changing beyond the named user-visible outcome?

## Quality bar

- The first test targets public behavior and fails for the expected reason.
- Implementation follows the observed failing test.
- Verification includes focused and relevant broader tests, or exact pending commands.

## Expected output format

```markdown
## TDD implementation: <behavior>

**Behavior target**: <observable outcome>
**Evidence reviewed**: <interfaces/tests/commands>
**First failing test**: <test name and expected red result>
**Minimal implementation step**: <change>
**Refactor notes**: <after-green cleanup only>
**Verification**: <commands/results or pending requests>
```

## Failure modes to avoid

- **Green-first coding**: changing implementation before seeing the failing test.
- **Implementation-detail test**: asserting private structure instead of behavior.
- **Refactor before green**: mixing cleanup into the failure-to-pass step.

More in `references/anti-patterns.md`.
