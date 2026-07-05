---
name: db-to-dwh-pipeline
description: Design and implement a pipeline that replicates operational database tables (Postgres/MySQL/etc.) into a data warehouse (BigQuery/Snowflake/Redshift), covering source-to-target mapping, incremental extraction, deletes, schema drift, and validation. Use when asked to "get table X into the warehouse", build or fix EL/ELT ingestion, or when source-vs-warehouse counts disagree. Produces a documented mapping, incremental strategy with proof of correctness, and reconciliation checks — not just a copy script. For transformations inside the warehouse, use dataform-bigquery-development instead.
---

# DB-to-DWH Pipeline

## Goal

Deliver a replication pipeline whose warehouse output provably matches the source — with an explicit source-to-target mapping, an incremental strategy that can't silently miss rows, a story for deletes and schema drift, and reconciliation checks that keep it honest over time.

## When to use

- "Get these operational tables into the warehouse."
- Building or modifying extract/load jobs (custom, Fivetran/Airbyte-style, CDC).
- Diagnosing why warehouse counts don't match the source.
- Adding a new table to an existing ingestion framework.

## When not to use

- Transformations *within* the warehouse (use `dataform-bigquery-development` or your dbt equivalent).
- One-off analytical exports where staleness and drift don't matter.
- Streaming event pipelines from applications (different design space: schemas-on-write, brokers).

## Workflow

**Evidence access rule (applies to every step):** when you cannot run a profiling query or pipeline command yourself, output the exact SQL/command for the user to run and paste back. Mark any design decision that depends on missing results as *pending*. Never substitute an assumption for a profile you couldn't obtain.

1. **Profile the source tables before designing anything — and profile safely.** Per table, query: row count and daily churn, primary-key uniqueness (verify, don't assume), `updated_at` reliability (does every write path set it? check ORM config/triggers), hard- vs soft-delete behavior, column types and real nullability. Run profiling against a read replica where one exists, off-peak, with bounded queries — a COUNT/checksum sweep on the primary during business hours is an incident waiting to happen. Every downstream decision hangs on these facts.
2. **Mirror the existing framework.** If the repo already ingests other tables, use the same tooling, naming, and orchestration. Deviations need stated reasons.
3. **Write the source-to-target mapping.** Every column: carried, renamed, converted (lossy conversions and timezone semantics flagged), or excluded (PII dropped/hashed — decide explicitly); plus metadata columns (`_loaded_at`, `_extracted_at`, source). The full item list lives in `references/checklist.md`.
4. **Choose the incremental strategy from the profile, not from habit.** Reliable `updated_at` + no hard deletes → high-watermark with a lookback window sized from real transaction durations. Hard deletes or unreliable `updated_at` → CDC if available, else snapshot-diff or PK anti-join. Small tables → nightly full reload is often the correct answer. Then write the **"what could this miss" analysis**: which row change would escape the strategy, and why that can't happen or is accepted.
5. **Fix the correctness contract.** Three decisions, each explicit in code or config: idempotency (MERGE on PK or partition replace — re-runs are a certainty), delete propagation (mechanism, or documented non-handling with reason), and schema drift policy (new column: add or alert; type change/drop: fail loudly — never silent).
6. **Verify on a real slice, with reconciliation built in.** Run the pipeline on one table; wire the reconciliation checks (source-vs-target counts, freshness lag vs SLA, checksum/sample on one high-value table) into the pipeline with alerting — not as a someday-task; deliberately double-run the same window and prove zero duplicates; if deletes are in scope, delete a test row at the source and show it propagates.
7. **Plan the backfill and hand off.** Initial-load path distinct from the incremental path; **estimate the backfill's cost and load** (bytes scanned/moved, source read pressure, runtime) before running it; verify the completed backfill with the reconciliation checks before cutover. Write the handoff per the output format. Before writing it, load `references/checklist.md` and verify every item; consult `references/anti-patterns.md` whenever a shortcut looks tempting.

## Questions to ask (only when necessary)

- Freshness SLA (hourly? daily?) — this drives strategy and cost and is rarely inferable.
- PII policy for columns like email/name if no existing convention in the repo.
- Access to source (read replica? load window constraints?) if not documented.

Everything about the source's actual behavior (keys, deletes, `updated_at`) should be answered by profiling queries — run by you, or run by the user via the evidence access rule — not by guessing.

## Quality bar

- Source profile documented with the actual queries/results, per table; profiling load on the source was considered.
- Mapping covers every column — no unmentioned columns.
- Incremental strategy includes the "what could this miss" analysis and a lookback justification.
- Loads proven idempotent by an actual double-run, not by reading the MERGE.
- Delete handling explicit — even if the decision is "deletes not propagated; accepted because X".
- Reconciliation checks exist in the pipeline with alerting, and passed on the verified slice.
- Backfill cost estimated before execution.

## Expected output format

```markdown
## Pipeline: <source db> → <warehouse> (<tables>)

**Source profile** (queried <date>, against <replica/primary + when>)
| table | rows | PK verified | updated_at reliable | deletes | daily churn |

**Mapping**: <link/section per table: column map, type conversions, exclusions, metadata cols>

**Incremental strategy**: <watermark/CDC/snapshot per table> — lookback: <window + why>
**What it could miss**: <analysis + why acceptable/impossible>
**Deletes**: <mechanism or explicit non-handling + reason>
**Schema drift policy**: <add/alert/fail behavior>

**Idempotency**: <mechanism> — double-run verified: <result>
**Reconciliation**: <checks wired + where they alert> — verified slice: <results>
**Backfill**: <plan/status — estimated cost/load + verification>

**Lineage**: source.<schema.table> → <landing dataset> → <staging model>
**Downstream consumers notified/affected**: <list or "new tables, none yet">
**Pending evidence**: <profiles/results awaiting user-run queries, if any>
```

## Failure modes to avoid

- **Trusting `updated_at`**: assuming every write path maintains it; bulk updates, raw SQL fixes, and triggers routinely bypass it — verify against the ORM/config and by sampling.
- **Exact-boundary watermarks**: `WHERE updated_at > :last_run` with no lookback loses rows from transactions that committed late with earlier timestamps.
- **Non-idempotent loads**: append-only loaders that duplicate rows on every retry — prove idempotency by double-run before shipping.

More in `references/anti-patterns.md`.
