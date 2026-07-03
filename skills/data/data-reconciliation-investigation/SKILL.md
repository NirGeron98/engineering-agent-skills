---
name: data-reconciliation-investigation
description: Investigate why two datasets that should agree don't — a warehouse metric vs. the source system, two reports showing different totals, a model output vs. a hand count — by isolating where in the transformation chain the numbers diverge and classifying the cause (double-counting, dropped rows, filter/join mismatch, grain mismatch, timezone/currency, late data). Use when someone says "these numbers don't match" or "the total is off by X". Produces a reconciliation diagnosis pinning the divergence stage with row-level evidence and a correction plan. For source-vs-warehouse replication row counts, use db-to-dwh-pipeline.
---

# Data Reconciliation Investigation

## Goal

Find the exact stage and mechanism where two figures that should be equal stop being equal — and prove it with row-level evidence, not a plausible theory. Reconciliation is subtraction done carefully: narrow the discrepancy from "the total is wrong" to "these specific rows are counted twice / dropped / filtered here, for this reason".

## When to use

- "The dashboard says X, the source system says Y."
- Two reports/models that should tie out show different totals.
- A metric doesn't match a hand-calculated or externally-provided number.
- A migration/refactor changed a total and you need to know whether the new or old number is right.

## When not to use

- The data is *stale* (old but internally consistent), not mismatched — use `data-freshness-investigation`.
- Source-vs-warehouse row-count drift in a replication pipeline specifically — use `db-to-dwh-pipeline` (its reconciliation checks own that).
- Operational data inconsistency across live services/caches/queues (OLTP) — use `data-consistency-incident-diagnosis`.
- Defining the ongoing reconciliation checks rather than investigating a specific break — use `data-quality-contracts`.

## Workflow

**Evidence access rule (applies to every step):** when you cannot run reconciliation queries on either side yourself, output the exact SQL for the user to run on each system and paste back. Mark conclusions depending on missing evidence as *pending*. Never attribute a discrepancy to a cause you haven't shown with rows.

1. **Pin the discrepancy exactly.** Get both numbers with their precise definitions: metric, filters, time range, grain, currency/units, as-of time. Two "revenue" numbers often differ because they mean different things — confirm they're supposed to be equal before hunting a bug. Compute the exact delta (absolute and %).
2. **Reproduce both sides from their true definitions.** Write the query that produces each figure from its own source of truth. If you can't reproduce a reported number, that mismatch (report vs. its own query) is itself the finding.
3. **Decompose the delta instead of eyeballing totals.** Break both sides along a shared dimension — by day, by category, by tenant — and diff the slices. A discrepancy concentrated in one slice (one day, one region) localizes the cause far faster than a total. Is the delta constant, proportional, or spiky?
4. **Trace the divergence stage by stage.** Run the reconciliation at each transformation hop from source to output: source → staging → intermediate → mart. The stage where the two sides first disagree is where the cause lives. Reconcile counts *and* sums (a join can preserve count while inflating sum).
5. **Classify the mechanism at the divergence stage.**
   - **Double-counting**: a fan-out join (1:many) multiplying rows; a re-run appending instead of replacing; overlapping incremental windows.
   - **Dropped rows**: an `INNER JOIN` that should be `LEFT`; a `WHERE` excluding nulls/edge cases; a filter on a dimension with missing values.
   - **Grain mismatch**: summing a metric that's already aggregated, or joining at the wrong grain so values repeat.
   - **Semantic mismatch**: different currency/timezone/tax treatment, gross vs net, different status filter (includes refunds? cancelled?), different as-of cutoff.
   - **Timing / late data**: one side counted before late-arriving records landed; different snapshot times.
6. **Prove the mechanism with the offending rows.** Show the specific duplicated keys (group-by having count > 1), the dropped rows (anti-join), or the mismatched values — not just "the join looks wrong". The proof is a small result set a reviewer can see.
7. **Determine which number is correct** (or that both are wrong) and the correction: the fix to the pipeline, and whether historical outputs need a backfill/restatement (hand off to `backfill-planning-and-verification` if nontrivial). Recommend the standing reconciliation check that would catch recurrence (→ `data-quality-contracts`). Before finishing, load `references/checklist.md`; consult `references/anti-patterns.md` when classifying.

## Questions to ask (only when necessary)

- The precise definition of each figure (filters, grain, as-of time) when the report doesn't state it.
- Which number is considered authoritative, if a business rule (not the data) decides it.
- Access to the second system, when only one side is queryable by the agent.

The actual numbers and where they diverge are queried, never assumed.

## Quality bar

- Both figures reproduced from their own sources with the exact query and definition; any report-vs-query mismatch itself flagged.
- Delta decomposed along a shared dimension, not compared as bare totals.
- Divergence localized to the first stage where the two sides disagree, checking both count and sum.
- Mechanism classified and **proven with the specific offending rows**, not asserted.
- A verdict on which number is right (or both wrong), with the correction and restatement scope.

## Expected output format

```markdown
## Reconciliation diagnosis: <figure A> vs <figure B>

**Definitions**: A = <metric/filters/grain/as-of>; B = <...> — expected equal because <reason>
**Delta**: <A> vs <B> = <abs> (<%>); shape: <constant / proportional / concentrated in <slice>>

**Stage reconciliation (count / sum)**
| stage | A-side | B-side | agree? |
|---|---|---|---|
| source | ... | ... | yes |
| staging | ... | ... | NO ← diverges |

**Mechanism**: <double-count / dropped rows / grain / semantic / timing>
**Proof**: <the offending rows — duplicated keys / anti-join result / mismatched values>

**Verdict**: <which figure is correct, or both wrong> — because <evidence>
**Correction**: <pipeline fix> — restatement: <backfill scope, or none; → backfill-planning-and-verification if nontrivial>
**Standing check**: <reconciliation check to add → data-quality-contracts>

**Pending evidence**: <queries awaiting user, if any>
```

## Failure modes to avoid

- **Comparing totals, not slices**: staring at two grand totals and theorizing; decomposing by day/category usually points straight at the offending rows.
- **Theory without rows**: "it's probably a join fan-out" — show the duplicated keys. An unproven mechanism is a guess, and reconciliation is exactly where guesses are cheap to disprove.
- **Assuming the two should be equal**: chasing a "bug" between two numbers that legitimately differ (gross vs net, different as-of) — confirm the definitions match before debugging.

More in `references/anti-patterns.md`.
