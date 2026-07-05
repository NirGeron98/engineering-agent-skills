# Service Debugging — Anti-patterns

## Turn it off and on again (first)
**Symptom**: pod restarted before anyone captured logs, heap, or connection state; symptom gone; cause unknown; it returns Friday night.
**Instead**: capture evidence first; restart only as an authorized mitigation, with the diagnostic cost acknowledged.

## First-ERROR-line theory
**Symptom**: a scary log line becomes the diagnosis — but it's been logging 50×/day for a year.
**Instead**: baseline every candidate signal: did its rate change at symptom onset?

## Source-code archaeology for an env problem
**Symptom**: three hours reading retry logic; the cause was `DB_POOL_SIZE=5` in staging vs `50` in prod.
**Instead**: diff environment config against a healthy environment in the first 15 minutes.

## Mean-value blindness
**Symptom**: "latency is only up 8%" — while p99 is 30× and one enterprise tenant is completely down.
**Instead**: segment by percentile, endpoint, tenant, instance before characterizing severity.

## Diagnostic state mutation
**Symptom**: "let's purge the DLQ and see if it recovers" — 4,000 messages of evidence (and customer data) gone.
**Instead**: read-only diagnosis; every state change is a proposed remediation with authorization.

## Correlation shopping
**Symptom**: onset was 14:30, a deploy happened 14:25, case closed — but the marketing email at 14:28 tripled traffic and the deploy was a README change.
**Instead**: enumerate *all* candidate changes near onset and check what the correlated change actually contains.

## The herd after the fix
**Symptom**: dependency recovers, and every client's accumulated retries hit at once, re-killing it; or the queue backlog double-charges customers.
**Instead**: before remediating, answer: what replays when this comes back, and is that processing idempotent?

## Invented telemetry
**Symptom**: the diagnosis cites "the logs show pool exhaustion since 13:39" — but the agent never had log access; the line is an inference dressed as an observation.
**Why it fails**: fabricated evidence is worse than missing evidence — it forecloses the investigation paths that would have found the real cause.
**Instead**: when access is missing, output the exact query/command for the user to run and paste back; mark dependent conclusions as pending until the result arrives.

## Fixing the environment in code
**Symptom**: staging times out because its pool size is 5 vs prod's 50 — and the "fix" patches application logic to tolerate connection starvation.
**Why it fails**: two lies now coexist: the config drift remains, and the code carries a workaround whose reason nobody will remember.
**Instead**: when the failure layer is config/environment drift, fix the config (or escalate to whoever owns it); change code only when code is the cause.

## Works-locally dismissal
**Symptom**: "can't reproduce locally, must be infra's problem."
**Instead**: reproduce the deployed configuration (same env vars, same data shape, same concurrency) — "works locally" usually means "works with different config".
