---
name: data-consistency-incident-diagnosis
description: Diagnose data that appears inconsistent, duplicated, missing, stale, or contradictory across application tables, services, caches, queues, or replicated stores. Use when an active data consistency incident needs known-vs-unknown facts, a suspected cause, and a safe reconciliation plan. Produces a data consistency incident diagnosis with affected scope and prevention recommendations.
---

# Data Consistency Incident Diagnosis

## Goal

Turn a reported data inconsistency into an evidence-backed diagnosis of cause and scope, plus a safe reconciliation plan.

## When to use

- Data appears duplicated, missing, stale, or contradictory across tables, services, caches, queues, or replicas.
- The incident is active or recent and needs scoping before correction.
- A write path, read path, or replication/event flow needs to be traced to find the divergence point.
- Use this even if the eventual root cause may turn out to be a single service; only hand off to `skills/api-and-backend/service-debugging` once evidence shows an isolated in-process bug with no cross-store divergence.

## When not to use

- The concern is data warehouse quality contracts, not operational/application data; use `skills/data/data-quality-contracts`.
- The root cause is clearly a single service's runtime bug, not a cross-store consistency question; use `skills/api-and-backend/service-debugging`.
- Incident coordination/communication (not the data diagnosis itself) is the need; use `skills/devops-infrastructure/incident-triage-runbook`.

## Composition and handoff rules

- Defer data warehouse quality contracts to `skills/data/data-quality-contracts`.
- Defer service runtime debugging to `skills/api-and-backend/service-debugging`.
- Defer incident coordination to `skills/devops-infrastructure/incident-triage-runbook`.

## Evidence access rule

Inspect the actual affected records, the write path (code/transaction boundaries), the read path, and any replication/cache/event flow between the source of truth and the affected store. If records, write/read path code, or replication/event evidence are unavailable, request exact record identifiers or logs and mark cause and scope conclusions pending.

## Workflow

1. Record the symptom precisely: which records/entities, which stores disagree, and since when.
2. Identify the declared source of truth and trace the write path from it to the affected store(s).
3. Trace the read path and any replication, cache invalidation, or event/queue flow between them.
4. Check recent deployments, jobs, or schema changes, and idempotency/retry behavior on the write path.
5. Separate known facts from suspected cause; state the most likely divergence point with evidence.
6. Produce a safe reconciliation plan (read-before-write, scoped correction, no blind mass-update) and prevention recommendations.
7. Before final handoff, load `references/checklist.md`; consult `references/anti-patterns.md` when tempted to correct data before confirming scope or source of truth.

## Questions to ask (only when necessary)

- Which store is the authoritative source of truth for this data?
- Since when has the inconsistency been observed, and how many records are affected?

## Quality bar

- The suspected cause is tied to an actual traced path (write/read/replication/event), not guessed from symptoms alone.
- Affected scope is stated as confirmed vs estimated, not asserted as complete.
- The reconciliation plan does not perform a blind mass-write without scoping first.

## Expected output format

```markdown
## Data consistency incident diagnosis: <scope>

**Known facts**: <confirmed evidence>
**Unknown/suspected**: <open questions>
**Suspected cause**: <divergence point and reasoning>
**Affected scope**: <confirmed vs estimated>
**Safe reconciliation plan**: <steps>
**Prevention recommendations**: <steps>
**Pending evidence**: <exact requests>
```

## Failure modes to avoid

- **Data correction without scope**: fixing visible records without confirming the full affected scope.
- **Source-of-truth assumption**: assuming which store is authoritative without confirming it.
- **Suspicion treated as fact**: stating a suspected cause as confirmed before tracing the actual path.

More in `references/anti-patterns.md`.
