---
name: diagnose-bug
description: Systematically find the root cause of a bug through reproduction and evidence before proposing any fix. Use when given an error message, stack trace, failing test, or "X is broken / behaves wrong" report. Produces a confirmed root cause with a minimal reproduction and a proposed minimal fix — never a speculative patch. Do not use for feature work or known-cause changes.
---

# Diagnose Bug

## Goal

Identify the actual root cause of a reported defect — confirmed by reproduction or direct evidence — and propose the smallest safe fix, with a regression test. Diagnosis first; the fix is a consequence, never a guess.

## When to use

- An error message, stack trace, or failing test is reported.
- "It worked yesterday", "it works locally but not in prod", "sometimes it fails".
- A user describes wrong behavior without an error ("totals are off by one day").

## When not to use

- The root cause is already confirmed and the task is just implementing the fix (use `task-breakdown` if nontrivial).
- Feature requests phrased as bugs ("bug: there's no CSV export").
- The problem is still production/service-level — no reproduction yet, and diagnosis requires logs, traces, deploy history, or environment comparison — defer to `service-debugging`; return here once it narrows the issue to a reproducible code defect.
- Production incident triage where mitigation must precede diagnosis — mitigate first, then return here.
- The failure is primarily intermittent, order-dependent, seed-dependent, timing-dependent, or only fails sometimes in tests/CI — use or hand off to `skills/testing/flaky-test-debugging`; this skill covers reproducible bugs and consistent local failures.

## Workflow

1. **Capture the symptom precisely.** Exact error text, expected vs. actual behavior, environment, frequency (always/sometimes), and when it last worked. Quote it — don't paraphrase.
2. **Reproduce before theorizing.** Find or build the cheapest reliable reproduction: a failing test, a curl command, a script. If you cannot reproduce, say so and gather more evidence (logs, inputs) — do not proceed to a fix on an unreproduced bug without flagging it.
3. **Locate the failure boundary.** Follow the stack trace or trace the data path to the last point where state is correct and the first point where it's wrong. Read the actual code at that boundary — all of the relevant function, not just the highlighted line.
4. **Form at most 2–3 ranked hypotheses.** Each must be falsifiable and name the evidence that would confirm or kill it.
5. **Test hypotheses with evidence, cheapest first.** Add a log/assert, run a narrowed test, inspect data, `git log`/`git bisect` if it's a regression. Every rejected hypothesis gets one line of record.
6. **Confirm root cause.** The standard: you can explain the full causal chain from code to symptom, and predict exactly how the reproduction changes when the cause is altered. "This looks suspicious" is not root cause.
7. **Write the regression test first.** It must fail for the reported reason before the fix. If the repo has no test harness, script the reproduction (a runnable command or file) and include it — don't skip the failing-first proof.
8. **Apply the minimal fix.** Fix the cause, not the symptom. No opportunistic refactoring in the same change. If the same defect pattern exists elsewhere (search for it), list the other sites rather than silently expanding the diff.
9. **Verify**: regression test passes, full relevant suite passes, the original reproduction now behaves correctly.
10. **Write the handoff summary** in the output format. Before writing it, load `references/checklist.md` and verify each item; if any diagnostic step felt ambiguous or risky, consult `references/anti-patterns.md` before proceeding.

## Questions to ask (only when necessary)

- Reproduction inputs you cannot obtain yourself (prod-only payloads, credentials, tenant IDs).
- Whether behavior X or Y is the *intended* behavior, when code and docs disagree and the fix depends on it.

Never ask "have you tried…" — try it yourself.

## Quality bar

- Root cause stated as a causal chain, backed by a reproduction or direct evidence (log line, data sample, bisect result).
- Rejected hypotheses documented in one line each.
- The regression test demonstrably failed before the fix and passes after.
- Fix diff is minimal; unrelated cleanups excluded.
- Sibling occurrences of the same defect pattern searched for and reported.

## Expected output format

```markdown
## Bug diagnosis: <one-line symptom>

**Root cause**: <causal chain: code → wrong state → symptom> (<file:line>)
**Evidence**: <reproduction / log / bisect that confirms it>
**Hypotheses rejected**: <hypothesis — disproving evidence> (one line each)

**Fix**: <what changed and why it's minimal> (<files>)
**Regression test**: <test name/path> — failed before fix: yes
**Verification**: <suites/commands run + results>

**Same pattern elsewhere**: <other sites found, or "searched, none">
**Follow-ups (not done)**: <optional>
```

## Failure modes to avoid

- **Fix-first debugging**: patching the first suspicious line, seeing if the symptom moves. Each wrong patch destroys evidence.
- **Unreproduced "fixes"**: declaring a bug fixed without ever having seen it fail.
- **Confidence inflation**: reporting "root cause found" when the standard reached was only "plausible theory". Label theories as theories.

More in `references/anti-patterns.md`.
