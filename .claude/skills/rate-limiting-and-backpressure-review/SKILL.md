---
name: rate-limiting-and-backpressure-review
description: Review how a backend service protects itself from overload — inbound rate limiting and quotas, concurrency limits, load shedding, timeouts and bounded queues on outbound dependencies, and graceful degradation — by reading the middleware, client, and pool configuration in the actual codebase. Use when adding or reviewing rate limits, when a service falls over under spikes or a slow dependency, or when someone asks "will this hold up under load / is our rate limiting correct". Produces an overload-protection findings report grounded in config and code. For achieving a throughput target on a healthy service, use throughput-bottleneck-triage.
---

# Rate Limiting & Backpressure Review

## Goal

Judge whether a service degrades gracefully instead of collapsing when demand exceeds capacity or a downstream dependency slows down — the two ways services fall over — and produce findings a developer can apply before a traffic spike or a slow dependency takes the whole thing down.

## When to use

- Adding, changing, or reviewing rate limits, quotas, or throttling.
- A service falls over under traffic spikes, retries storms, or a slow/broken dependency.
- Reviewing timeout/pool/concurrency config for calls to databases or third-party APIs.
- Someone asks whether the service will "hold up under load" from a protection standpoint.

## When not to use

- The goal is to *reach* a QPS/throughput target on an otherwise healthy service — use `throughput-bottleneck-triage`.
- A live overload incident needing telemetry/timeline diagnosis right now — use `service-debugging`.
- Whether specific writes are safe when clients retry after a 429/timeout — hand off to `idempotency-and-retry-safety-review`.
- Queue/worker delivery reliability (DLQ, visibility) — use `background-job-queue-review`.

## Workflow

**Evidence access rule (applies to every step):** when you cannot read middleware/pool config, a load-balancer setting, or a metrics dashboard yourself, output the exact file, console path, or query for the user to provide. Mark conclusions depending on missing evidence as *pending*. Never assume a limit exists — find it in config.

1. **Map the capacity chain.** For the path under review, list the finite resources between the edge and the data: worker/thread pool size, event-loop concurrency, DB connection pool, downstream API client pool, memory. The smallest one is the real ceiling; overload protection must engage before it's exhausted.
2. **Inventory inbound protection.** Where are requests limited: LB/gateway, middleware, per-route? Read the actual limiter config: algorithm (token bucket / fixed / sliding window), scope (per-IP / per-user / per-tenant / global), limit and burst, and the storage backing it (in-memory per-instance vs shared store — per-instance limits multiply by replica count and don't actually cap the fleet).
3. **Check the limit's response contract.** Over-limit returns 429 (not 500) with a `Retry-After`; the limiter itself is cheap (doesn't do the expensive work before rejecting); high-cost endpoints have tighter limits than cheap ones; authenticated abuse and unauthenticated abuse both considered.
4. **Check outbound backpressure — the more commonly missed half.** For each downstream call: a timeout is set (an unbounded call is how one slow dependency exhausts the pool); the connection pool is bounded; there's a circuit breaker or failure threshold so a dead dependency fails fast instead of queuing; retries have backoff + jitter and a budget (retry storms amplify overload). No unbounded in-memory queue absorbing backlog until OOM.
5. **Check load shedding and prioritization.** Under sustained overload, does the service shed load deliberately (reject early, drop low-priority work) or accept everything and collapse? Are health checks / critical paths protected from being starved by bulk traffic?
6. **Check graceful degradation.** Fallbacks for a failed dependency (cached/stale response, reduced feature, queue-for-later) vs. hard failure; whether a non-critical dependency's outage takes down a critical path (it shouldn't).
7. **Check the client's experience.** 429/503 with `Retry-After`; documented limits; behavior that doesn't push well-behaved clients into hammering. Confirm limits are observable (metrics on throttle rate, rejections) so operators can see them bite.
8. **Report** in the output format, citing config/code, ranked by how badly each gap fails under load. Before reporting, load `references/checklist.md`; consult `references/anti-patterns.md` when weighing a finding.

## Questions to ask (only when necessary)

- The expected/peak traffic shape and known spike sources (launches, batch jobs), if not inferable.
- The relative criticality of downstream dependencies, when the code doesn't reveal which are optional.
- Replica count and whether limiter storage is shared, if not visible in config.

Limit values, pool sizes, and timeout settings come from config, not from asking.

## Quality bar

- The binding capacity ceiling identified from real pool/concurrency config.
- Inbound limits cited with algorithm, scope, and storage (per-instance vs shared called out).
- Every downstream call checked for a timeout and bounded pool; unbounded ones flagged.
- Retry behavior checked for backoff/jitter/budget (retry-storm risk named).
- Over-limit response contract verified (429 + Retry-After, cheap rejection).
- Findings ranked by failure severity under load (cascading collapse first).

## Expected output format

```markdown
## Rate limiting & backpressure review: <service / path>

**Capacity ceiling**: <smallest finite resource + value> (from <config>)
**Replica/storage note**: <per-instance limits × N replicas / shared store>

**Cascading-collapse risks**
1. <finding> — under <spike / slow dependency> → <pool exhaustion / OOM / cascade> — suggest: <fix> (<evidence>)

**Inbound limiting gaps**
- <algorithm/scope/limit/response finding> (<config evidence>)

**Outbound backpressure gaps**
- <timeout/pool/circuit-breaker/retry finding> (<code evidence>)

**Degradation / shedding**
- <finding> (<evidence>)

**Checked and clean**
- <verified areas>

**Pending evidence**: <config/metrics awaiting user, if any>
```

## Failure modes to avoid

- **Inbound-only review**: adding a shiny per-IP rate limiter while every downstream call is unbounded — the service still dies when the database slows, just from a different direction. Backpressure on dependencies matters as much as the front door.
- **Per-instance limits sold as fleet limits**: an in-memory limit of 100/s on 20 replicas is a 2000/s fleet limit — and zero protection for a shared backend. Call out the storage.
- **Retry amplification**: client retries on 5xx with no backoff turn a blip into a self-sustained storm; a limiter that returns 500 instead of 429 invites exactly this.

More in `references/anti-patterns.md`.
