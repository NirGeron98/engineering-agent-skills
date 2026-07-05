# Code Review — Anti-patterns

## Diff tunnel vision
**Symptom**: the changed function looks correct, approved — but a caller relied on the old return value for `[]` and now crashes.
**Instead**: for every changed public behavior, open at least the direct callers.

## The vibe finding
**Symptom**: "this could be a race condition" / "might be slow at scale" with no input, no trace, no scenario.
**Why it fails**: authors can't act on it; real findings drown in maybes.
**Instead**: construct the failing scenario or run the code; if you can't, drop it or ask a labeled question.

## Nitpick flooding
**Symptom**: 20 comments on naming and import order; the missing authz check on the new admin endpoint goes unnoticed.
**Instead**: style belongs to the formatter/linter; spend review attention exclusively on what machines can't check.

## Happy-path review
**Symptom**: verifying the code works when everything succeeds; error branches, empty inputs, and partial failures never examined.
**Instead**: error paths and boundaries are the highest-yield zones — start there, not end there.

## Review-as-redesign
**Symptom**: "I would have used the strategy pattern here" on correct, conventional code.
**Instead**: review the change against correctness and repo conventions; propose redesigns as separate discussion, not blocking findings.

## Trusting the tests because they're green
**Symptom**: coverage exists, so behavior is assumed verified — but the tests assert nothing meaningful, or pass without the change.
**Instead**: spot-check that new tests fail when the change is reverted; read assertions, not just test names.

## The 40-file skim
**Symptom**: a huge PR gets uniform shallow attention across every file, reported as a full review with no caveats.
**Why it fails**: shallow-everywhere finds nothing; the report's silence reads as "verified clean" when nothing was verified.
**Instead**: partition by risk — contracts, state, error paths get depth; boilerplate gets a skim — and say in the report which areas got which.

## Uninformative approval
**Symptom**: "LGTM 👍" — nobody knows if authz, migrations, or error paths were ever looked at.
**Instead**: list what you checked and found clean; make a clean review as auditable as a critical one.
