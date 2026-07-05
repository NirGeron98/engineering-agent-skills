# Data Freshness Investigation — Anti-patterns

## Re-run and pray
**Symptom**: the first action is to re-trigger the last job without finding where freshness breaks.
**Why it fails**: if a watermark is stuck or the source stopped, the re-run completes "successfully" and loads nothing — masking the cause and wasting the incident window.
**Instead**: localize the broken stage first; re-run only once you know it will actually load.

## Empty table read as "source stopped"
**Symptom**: the warehouse table has no recent rows, so the conclusion is "the source stopped sending data".
**Why it fails**: a stuck watermark, a `WHERE` filter, or a timezone-off incremental boundary produces the same empty result while the source is healthy.
**Instead**: verify at the true operational source (query it, or ask for a source-side count) before blaming it.

## Load-time freshness illusion
**Symptom**: `_loaded_at` is recent, so the table is declared fresh.
**Why it fails**: a nightly job can touch the table (updating load time) while loading only stale event data — the metric that matters, `event_time`, is days behind.
**Instead**: check event-time freshness, not just when the job last ran.

## Bottom-up code reading
**Symptom**: opening the transformation SQL and reading it line by line before checking where the data actually stops being fresh.
**Why it fails**: burns time reading healthy stages; the break might be a paused scheduler, not the code.
**Instead**: bisect the chain by timestamp first, then read only the stage that's actually broken.

## Ignoring the consuming layer
**Symptom**: the warehouse table is fresh, so the investigation stops — but the dashboard still shows old data.
**Why it fails**: the staleness is in the BI extract, a cached view, or a materialization that didn't refresh.
**Instead**: include the view/materialization/BI-cache layer as the last stage of the chain.

## No blast-radius pass
**Symptom**: the broken stage is found and fixed, but nobody enumerates what else was stale and for how long.
**Why it fails**: other teams may have made decisions on stale numbers, and other downstream models need the same catch-up.
**Instead**: list every downstream consumer affected and since when, and note whether stale data was served.

## Fix without a guardrail
**Symptom**: the immediate break is patched, but the check that would have caught it earlier is never added.
**Instead**: recommend the freshness check (hand off to data-quality-contracts) so the next occurrence alerts instead of being noticed by a confused stakeholder.
