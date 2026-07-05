---
name: connection-pool-and-locking-debugging
description: Debug database timeouts, deadlocks, connection pool exhaustion, connection leaks, lock waits, transaction contention, or connection storm behavior using error messages, DB metrics, pool config, and lock/wait evidence. Use when the symptom is connection- or lock-related rather than a single slow query. Produces a connection/locking diagnosis with a likely root cause and safe mitigation options.
---

# Connection Pool and Locking Debugging

## Goal

Turn intermittent DB timeouts, deadlocks, or pool exhaustion into an evidence-backed root cause and a safe mitigation and verification plan.

## When to use

- The app reports DB timeouts, deadlocks, connection pool exhaustion, or connection leaks.
- Lock waits or transaction contention are suspected under concurrent load.
- Connection storm behavior appears after a deploy, traffic spike, or scaling event.

## When not to use

- The symptom is a single slow query without contention/pool signals; use `.claude/skills/query-performance-review`.
- The root cause is clearly general application logic, not DB connections/locks; use `.claude/skills/service-debugging`.
- Incident coordination/communication (not the technical diagnosis) is the need; use `.claude/skills/incident-triage-runbook`.

## Composition and handoff rules

- Defer general service debugging to `.claude/skills/service-debugging`.
- Defer query plan tuning to `.claude/skills/query-performance-review`.
- Defer incident response coordination to `.claude/skills/incident-triage-runbook`.

## Evidence access rule

Inspect actual error messages, database connection/lock metrics, application pool configuration, transaction boundaries in code, and recent deployment/runtime changes. If metrics, pool config, or lock/wait graphs are unavailable, request exact commands or dashboards and mark root-cause conclusions pending.

## Workflow

1. Collect the actual error messages/symptoms and when they occur (load pattern, deploy correlation).
2. Check DB-side evidence: connection count, pool saturation, lock/wait graphs, deadlock logs.
3. Check app-side evidence: pool size/config, connection lifecycle (checkout/release), transaction boundaries and duration.
4. Correlate with slow queries or recent deployment/runtime changes that could hold connections/locks longer.
5. Identify the likely root cause (leak, undersized pool, long transaction, lock ordering) with evidence.
6. Produce safe mitigation options (pool sizing, transaction scoping, retry/backoff, lock ordering fix) and a verification loop.
7. Before final handoff, load `references/checklist.md`; consult `references/anti-patterns.md` when tempted to raise the pool size or timeout as the only fix.

## Questions to ask (only when necessary)

- What is the current connection pool configuration (size, timeout, max lifetime)?
- Can I see deadlock logs or lock/wait metrics from the database during the incident window?

## Quality bar

- The root cause is tied to specific metrics/logs/code, not guessed from symptom description alone.
- Mitigations address the underlying cause (leak, long transaction, lock order), not just resource limits.
- A verification loop confirms the fix under representative load before declaring it resolved.

## Expected output format

```markdown
## Connection/locking diagnosis: <scope>

**Evidence table**: <errors, DB metrics, pool config, lock/wait evidence>
**Likely root cause**: <finding>
**Safe mitigation options**: <steps>
**Verification loop**: <steps>
**Pending evidence**: <exact requests>
```

## Failure modes to avoid

- **Timeout/pool-size as fix**: raising timeouts or pool size without diagnosing the underlying cause.
- **Local reproduction assumption**: assuming local behavior reflects production concurrency/load.
- **Lock evidence skipped**: diagnosing deadlocks without reading actual deadlock/lock-wait logs.

More in `references/anti-patterns.md`.
