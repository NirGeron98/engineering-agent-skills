# Data Reconciliation Investigation — Anti-patterns

## Total-staring
**Symptom**: comparing two grand totals and brainstorming reasons they might differ.
**Why it fails**: a total is one number; it hides where the discrepancy lives. Theories multiply and none is tested.
**Instead**: decompose both sides by a shared dimension and diff the slices — the discrepancy usually concentrates and points at the cause.

## Theory without rows
**Symptom**: "it's probably a fan-out join" or "maybe late data" — stated as the conclusion.
**Why it fails**: reconciliation is the one place proof is cheap; an unproven mechanism is just a guess that may send the fix in the wrong direction.
**Instead**: show the duplicated keys, the anti-join result, or the mismatched values before naming the cause.

## Assuming they should match
**Symptom**: treating any difference between two "revenue" numbers as a bug.
**Why it fails**: they may measure different things — gross vs net, pre- vs post-refund, different timezone cutoff — so there's no bug to find.
**Instead**: reconcile the *definitions* first; only debug once the two are genuinely supposed to be equal.

## Reconciling count but not sum (or vice versa)
**Symptom**: row counts match at every stage, so the stage is declared clean.
**Why it fails**: a join can preserve row count while duplicating or altering the measured value; counts and sums fail differently.
**Instead**: reconcile both count and the aggregated metric at each stage.

## Stopping at the first plausible cause
**Symptom**: finding one dropped-row filter and declaring victory, when the delta is only partly explained.
**Why it fails**: multiple mechanisms can compound; a fix that closes half the gap looks broken.
**Instead**: confirm the identified mechanism accounts for the *full* delta; if not, keep decomposing the remainder.

## Ignoring as-of / timing
**Symptom**: comparing a number pulled now against one pulled yesterday and calling the difference a defect.
**Why it fails**: late-arriving data and different snapshot times create real, non-bug differences.
**Instead**: align the as-of times, or account for late data explicitly, before attributing the delta to logic.

## Fix without restatement or guardrail
**Symptom**: the pipeline is corrected, but historical outputs keep the wrong numbers and no check is added.
**Instead**: state the restatement/backfill scope and recommend the standing reconciliation check (→ data-quality-contracts).
