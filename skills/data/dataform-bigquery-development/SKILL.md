---
name: dataform-bigquery-development
description: Develop, modify, or review Dataform (SQLX) models on BigQuery — table/view/incremental configs, dependency graph via ref(), assertions, partitioning/clustering, and cost-aware SQL. Use when working in a Dataform repo (definitions/*.sqlx, workflow_settings.yaml/dataform.json), adding or changing warehouse transformations, or fixing incremental models that drift or over-bill. Produces compiled-and-tested model changes with assertions and a downstream-impact check, following the repo's layering conventions.
---

# Dataform BigQuery Development

## Goal

Ship Dataform model changes that compile, produce verifiably correct data, cost what they should (partition pruning, sane incrementals), carry assertions for their contracts, and don't silently break anything downstream in the dependency graph.

## When to use

- Adding or changing `.sqlx` models, assertions, or declarations in a Dataform project.
- An incremental model shows duplicates, gaps, or runaway BigQuery cost.
- Reviewing a Dataform PR.
- Restructuring the dependency graph (layers, staging → marts).

## When not to use

- Getting the raw data *into* BigQuery (use `db-to-dwh-pipeline`).
- dbt projects — concepts transfer but commands/config don't; don't apply Dataform mechanics to dbt files.
- Ad-hoc analytical queries with no model lifecycle.

## Workflow

**Evidence access rule (applies to every step):** when you cannot run `dataform compile`, a BigQuery query, or a `bq` dry-run yourself, output the exact command or SQL for the user to run and paste back. Mark any conclusion that depends on missing results (grain, bytes scanned, assertion status) as *pending*. Never substitute an assumption for a result you couldn't obtain.

1. **Read the project's shape first.** `workflow_settings.yaml` (or legacy `dataform.json`): default dataset/project, vars. Map the layer convention from `definitions/` (e.g. `sources/` declarations → `staging/` → `intermediate/` → `marts/`), naming style (`stg_`, `int_`, `fct_`/`dim_`), and where assertions live. New work follows the observed convention.
2. **Locate the change in the graph.** For a new model: which layer, which upstreams (always via `ref()`, never hardcoded table paths). For a change: run `dataform compile` and enumerate direct and transitive dependents of the touched model — this is your blast radius.
3. **Understand the source data before writing SQL.** Query the upstream(s): grain (one row per what? verify with a `GROUP BY key HAVING COUNT(*) > 1`), null rates on join/filter keys, time column semantics (event vs. load time). Never join or aggregate on assumed grain.
4. **Write the model with explicit config.**
   - `type`: view for cheap/light staging, table for consumed models, incremental only when volume justifies its complexity.
   - Declare the model's grain in a comment and enforce it with a uniqueness assertion.
   - Partition on the main time filter column, cluster on frequent filter/join keys; verify consumers' queries can actually prune (they filter on the partition column, not a function of it).
5. **For incrementals, design the merge contract deliberately.**
   - `uniqueKey` for MERGE semantics; without it Dataform appends — duplicates on any re-run.
   - The `when(incremental(), ...)` filter must use a window with lookback (late-arriving data), and the filter column must prune partitions on both target and source sides.
   - Answer explicitly: what happens on backfill (`--full-refresh`)? On a re-run of the same window? On late data older than the lookback?
6. **Attach assertions as the model's contract.** Built-in: `uniqueKey`, `nonNull` on grain and critical columns. Custom assertions for business rules (freshness vs. SLA, accepted ranges, referential integrity to dim tables). A model without assertions on its grain is unfinished.
7. **Compile, then test on real data cheaply.** `dataform compile` must be clean. Run the model in a dev workspace/schema; validate: grain query returns no dupes, row counts plausible vs. upstream, spot-check 5 rows against source, and `bq` dry-run the compiled SQL to check bytes scanned (a full-scan incremental is a bug even when results are right). For **changes to an existing model**, also diff the dev output against current production output on a bounded window (counts, key aggregates, sample rows) — every difference must be explained by the intended change.
8. **Check downstream impact before merging.** For every dependent found in step 2: does it reference columns you renamed/retyped/dropped? Compile catches missing refs, not semantic changes — grep dependents for the changed columns. Renamed/changed-meaning columns require coordinated updates or a deprecation window.
9. **Hand off** per the output format: what changed, verification results, cost profile, downstream notes. Before handing off, load `references/checklist.md` and verify every item; consult `references/anti-patterns.md` when an incremental or pruning decision feels uncertain.

## Questions to ask (only when necessary)

- Freshness/latency SLA for a new mart if not documented anywhere in the repo.
- Whether a full-refresh of a large incremental is permitted (cost) when a backfill is needed.

Grain, null behavior, and conventions are discoverable — query and read; don't ask.

## Quality bar

- Model compiles; all refs via `ref()`; layer and naming match repo convention.
- Grain stated in the model and enforced by an assertion that passed on real data.
- Incremental models: uniqueKey present (or append-only justified), lookback reasoned, re-run and full-refresh behavior stated and tested.
- Bytes-scanned checked via dry-run; partition pruning verified for the model and its main consumers.
- Downstream dependents enumerated and checked for the specific columns touched.
- Assertions run green in the dev workspace before merge.

## Expected output format

```markdown
## Dataform change: <model(s)>

**Graph position**: <layer>; upstreams: <refs>; dependents: <N models: names>
**Grain**: <one row per X> — enforced by <assertion>

**Config**: type=<view/table/incremental>; partition=<col>; cluster=<cols>
**Incremental contract** (if applicable): uniqueKey=<cols>; lookback=<window + reason>;
re-run: <behavior>; full-refresh: <behavior/cost>

**Verification**
- compile: clean; assertions: <pass> (dev workspace)
- grain dupe check: 0; row count vs upstream: <plausible: numbers>
- spot-check vs source: <5 rows matched>
- dry-run bytes: <before → after if changed>; pruning verified: <how>

**Downstream impact**: <per dependent: unaffected / updated / needs coordination>
**Follow-ups**: <optional>
```

## Failure modes to avoid

- **Incremental without uniqueKey**: silent append semantics; every retry duplicates the window.
- **Grain by vibes**: joining "one row per order" tables that actually have retries/versions — fan-out inflates every downstream metric.
- **Compile-green fallacy**: treating a clean compile as semantic safety for dependents; renames and meaning changes need a grep through dependents.

More in `references/anti-patterns.md`.
