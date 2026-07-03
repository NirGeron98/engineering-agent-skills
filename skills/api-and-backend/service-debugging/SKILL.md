---
name: service-debugging
description: Debug a misbehaving backend service in a running environment (staging/prod) using logs, metrics, traces, and request-path analysis — safely, without mutating state. Use for symptoms like elevated 5xx rates, latency spikes, timeouts, stuck queues, or "the service is acting weird in env X" where the failure lives in a deployed system, not just in local code. Works with or without direct observability access: when the agent can't query logs or dashboards, it requests exact evidence from the user instead of guessing. Produces an evidence-backed diagnosis and a safe remediation proposal. For a reproducible code-level defect, use diagnose-bug instead.
---

# Service Debugging

## Goal

Locate the cause of a live service misbehavior using observability data and read-only investigation, distinguish code defect from environment/dependency/data issue, and propose remediation with explicit blast-radius assessment — without making anything worse along the way.

## When to use

- Elevated error rates, latency, or timeouts in a deployed environment.
- Works locally / fails in staging or prod.
- Queue backlogs, stuck jobs, connection-pool exhaustion, memory growth.
- "The service went weird after the deploy / at 14:30 / under load."

## When not to use

- A reproducible code-level bug with a stack trace pointing at logic (use `diagnose-bug`).
- Active customer-impacting incident where your org's incident process applies — mitigate per that process first; use this skill for the follow-up diagnosis.
- Performance *optimization* of a healthy service (different workflow: profile against a target).

## Workflow

**Evidence access rule (applies to every step):** when you cannot run a query, log search, dashboard check, or deploy-history lookup yourself, output the exact command, query, or evidence request for the user to run and paste back. Mark any conclusion that depends on missing evidence as *pending*. Never substitute an assumption for a result you couldn't obtain.

1. **Freeze the timeline.** When did the symptom start (dashboard, first bad log)? What changed near that time — deploys, config/flag flips, migrations, dependency deploys, traffic shifts, certificate/credential expiry? `git log`, deploy history, flag audit logs. Correlation with a change is your strongest early signal.
2. **Characterize the symptom quantitatively.** Which endpoints/operations, what error rate vs. baseline, which percentiles (p50 vs p99 tells load-vs-poison-request apart), all instances or some, all tenants or one. Read-only queries only.
3. **Follow one failing request end-to-end.** Pick a concrete failing request ID/trace: edge → LB → service → downstream calls → DB/queue. Find the exact hop where it goes wrong. If you can't trace one request, that's a finding too (observability gap) — fall back to correlating logs across hops by time and correlation ID.
4. **Classify the failure layer.** Code defect / bad deploy · config or environment (env var, secret, flag) · dependency (DB, cache, third-party API) · resource saturation (CPU, memory, connections, disk) · data poison (one malformed record). Each has distinct evidence; state which you have.
5. **Read the code path last, informed by evidence.** Open the handler/consumer at the failing hop and check it against the observed behavior — timeout values, retry policies, pool sizes, error handling. Compare config in the failing env against a healthy one.
6. **Verify safely.** Prefer, in order: existing dashboards/logs → read-only queries → a synthetic request against staging → local reproduction of the deployed config. Never mutate prod state (no restarts, deletes, flag flips, queue purges) as a *diagnostic* step, and confirm any state-changing action with the user unless already authorized.
7. **Propose remediation with blast radius.** Rollback vs. config fix vs. code fix vs. scale-up — for each: risk, reversibility, and what confirms success. Note whether retries/queued messages will replay once fixed (thundering herd, duplicate processing).
8. **Close the loop.** After remediation, verify the symptom metric returns to baseline; write the handoff. Include observability gaps found (missing log, absent trace propagation) as follow-ups. Before writing the handoff, load `references/checklist.md` and verify each item; consult `references/anti-patterns.md` whenever a diagnostic shortcut looks tempting.

## Questions to ask (only when necessary)

- Access you lack: dashboard links, log queries, deploy history location.
- Authorization for any state-changing action (restart, rollback, flag change).
- Whether known concurrent events (marketing campaign, batch job) explain a traffic shift, if not visible in your data.

## Quality bar

- Symptom onset pinned to a time and correlated (or explicitly not) with a change.
- At least one concrete failing request traced across hops, or the tracing gap reported.
- Failure layer classified with the evidence that distinguishes it from neighbors.
- All diagnostic actions read-only; state changes gated on authorization.
- Remediation proposal includes blast radius, reversibility, and success criterion.
- Handoff distinguishes confirmed facts from hypotheses.

## Expected output format

```markdown
## Service diagnosis: <service — symptom>

**Timeline**: symptom since <time>; correlated change: <deploy/config/none found>
**Blast pattern**: <endpoints/instances/tenants affected; p50/p99; rate vs baseline>

**Traced request**: <request/trace id> — fails at <hop> with <exact error>
**Failure layer**: <code / config / dependency / saturation / data> 
**Evidence**: <the observations that establish it and exclude alternatives>

**Remediation proposal**
- <action> — risk: <blast radius> — reversible: <yes/how> — success check: <metric>
- Replay concern: <queued/retried work behavior after fix>

**Confirmed vs hypothesis**: <what is proven; what remains theory>
**Observability gaps**: <missing logs/traces/metrics found along the way>
```

## Failure modes to avoid

- **Restart-first debugging**: bouncing the service before capturing evidence — the symptom may vanish along with the diagnosis (and the heap/state you needed).
- **Diagnostic mutations**: purging a queue or flipping a flag "to see what happens" in a shared environment.
- **Invented telemetry**: summarizing logs or metrics you never actually saw. If you couldn't fetch it, request it and mark the conclusion pending.

More in `references/anti-patterns.md`.
