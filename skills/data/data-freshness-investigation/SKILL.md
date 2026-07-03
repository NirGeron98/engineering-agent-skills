---
name: data-freshness-investigation
description: Investigate why a warehouse table, model, or dashboard is stale — data hasn't updated, is lagging its SLA, or stopped arriving — by tracing the pipeline backward from the stale output to the first stage where freshness breaks, using run history, watermarks, source arrival times, and row-level timestamps. Use when someone says "the dashboard is out of date", "this table stopped updating", or a freshness check fired. Produces a freshness diagnosis pinning the broken stage with evidence, plus a remediation and backfill recommendation. For defining freshness SLAs and checks, use data-quality-contracts.
---

# Data Freshness Investigation

## Goal

Pin the exact pipeline stage where freshness breaks — source, ingestion, transformation, scheduler, or the consuming layer — with evidence at each hop, so remediation targets the real cause instead of re-running the last job and hoping. Distinguish "the pipeline failed" from "the pipeline ran but had nothing to load" from "the source itself stopped producing".

## When to use

- "The dashboard/report is showing old numbers" or "this table hasn't updated since <time>".
- A freshness/SLA check fired on a warehouse dataset.
- A downstream model is missing recent data while upstream looks fine (or vice versa).
- Data arrives but is lagging its expected cadence.

## When not to use

- Defining freshness expectations, thresholds, or checks in the first place — use `data-quality-contracts`.
- The values are present but *wrong/mismatched* between systems (not stale) — use `data-reconciliation-investigation`.
- Building or repairing the replication mechanism itself (watermark/delete/drift design) — use `db-to-dwh-pipeline`.
- The stale symptom is a live application/service/cache issue in an operational system — use `service-debugging` or `data-consistency-incident-diagnosis`.

## Workflow

**Evidence access rule (applies to every step):** when you cannot query the warehouse, read scheduler run history, or check the source yourself, output the exact SQL/command for the user to run and paste back. Mark conclusions depending on missing evidence as *pending*. Never claim a stage "ran fine" or "has no new data" without the query result that shows it.

1. **Quantify the staleness precisely.** What is the newest record/partition in the stale output, and what should it be? Query `max(event_time)`, `max(_loaded_at)`, and the latest partition — the gap between load-time and event-time freshness often localizes the break by itself. State the SLA it's violating.
2. **Establish the pipeline topology backward.** From the stale table, identify its immediate upstream(s): the model/job that writes it, that job's sources, back to the raw ingestion and the operational source. Lineage/`ref()`/scheduler DAG, not guesses. This is the search space.
3. **Bisect the chain.** At each stage from the top, check `max` timestamp / latest partition and last successful run time. The break is between the last stage that *is* fresh and the first that *isn't*. Don't start at the bottom and read code — localize first, then read only the broken stage.
4. **Classify the break at the broken stage.**
   - **Job didn't run**: scheduler paused/failed/misconfigured cron, dependency never triggered, disabled DAG. Check run history and last exit status.
   - **Job ran, loaded nothing**: an empty source read, a watermark stuck in the future, a `WHERE` filter excluding recent data, a timezone bug on the incremental boundary, an upstream that itself is stale.
   - **Source stopped producing**: the operational system/event stream stopped emitting — the pipeline is healthy and faithfully reflecting an empty source. Verify at the true source.
   - **Ran, loaded, but not visible**: partition/clustering not refreshed, view pointing at an old table, cache/BI extract not refreshed, materialization stale.
5. **Confirm the cause with a direct query, not inference.** E.g., show the watermark value vs. source max; show the scheduler's last run row; show source row counts by hour around the cutoff. One query that proves the classification.
6. **Assess the blast radius.** Which downstream models/dashboards/consumers are also stale because of this, and since when. Note whether stale data was silently served (decisions made on old numbers).
7. **Recommend remediation and recovery.** The fix for the broken stage; whether a backfill/catch-up is needed and its scope (hand off to `backfill-planning-and-verification` for a nontrivial one); and the missing check that would have caught this earlier (hand off to `data-quality-contracts`). Before finishing, load `references/checklist.md`; consult `references/anti-patterns.md` when classifying the break.

## Questions to ask (only when necessary)

- The expected cadence/SLA, if no contract or convention states it.
- Access to the true operational source, when confirming "source stopped producing".
- Which scheduler/orchestrator owns the job, if lineage doesn't reveal it.

The current state of each stage is queried, never assumed.

## Quality bar

- Staleness quantified with actual max-timestamp/partition queries at the output and the break point.
- The broken stage localized by bisecting the chain, not by guessing or reading all the code.
- The break classified into one of the four categories with a query that proves it.
- "Source stopped producing" verified at the true source, not inferred from an empty warehouse table.
- Blast radius of downstream staleness enumerated.
- Remediation names the stage-specific fix and whether a backfill is required.

## Expected output format

```markdown
## Freshness diagnosis: <stale table/dashboard>

**Staleness**: newest data <timestamp>; expected <timestamp>; lag <duration> vs SLA <SLA>
**Freshness type**: <load-time / event-time> gap

**Chain (top → bottom)**
| stage | max ts / last run | fresh? |
|---|---|---|
| source | ... | yes |
| raw ingest | ... | yes |
| staging model | ... | NO ← break |
| mart | ... | no (downstream of break) |

**Break stage**: <stage>
**Classification**: <didn't run / ran-loaded-nothing / source stopped / not visible>
**Proof**: <the query + result that establishes it>

**Blast radius**: <downstream models/dashboards stale + since when>

**Remediation**: <stage-specific fix>
**Recovery**: <backfill needed? scope; hand off to backfill-planning-and-verification if nontrivial>
**Missing guardrail**: <the freshness check that would have caught it → data-quality-contracts>

**Pending evidence**: <queries awaiting user, if any>
```

## Failure modes to avoid

- **Re-run and pray**: kicking off the last job without localizing the break — if the source stopped or a watermark is stuck, the re-run "succeeds" and loads nothing, hiding the real cause.
- **Empty table ≠ source stopped**: concluding the source dried up from an empty warehouse table when the real break is a stuck watermark or a filter — verify at the true source.
- **Load-time vs event-time confusion**: `_loaded_at` is current (the job runs nightly and touches the table) but `event_time` is days old — the table looks fresh by one metric and is stale by the one that matters. Check both.

More in `references/anti-patterns.md`.
