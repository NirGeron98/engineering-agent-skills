# Sample task: review overload protection before a product launch

## Prompt

> We're launching next week and expect a big traffic spike on the search API. Can you review whether our rate limiting and overload protection are actually going to hold? Service is in `services/search/`.

## Abbreviated agent run

1. Capacity chain: read pool config — Node service, event-loop concurrency effectively unbounded; downstream Elasticsearch client `maxSockets: 50`; Postgres pool `max: 10` (used for auth lookup on every request). Binding ceiling: the 10-connection PG pool, hit long before ES.
2. Inbound: `express-rate-limit` per-IP, 300/min, in-memory store, applied globally. Deployment runs 8 replicas — so the effective per-IP fleet limit is 2400/min, and PG (shared) sees no aggregate cap.
3. Response: over-limit returns 429 with `Retry-After` — good. But the limiter runs *after* the auth middleware, which hits PG — so rejected requests still consume a PG connection.
4. Outbound: ES client has `requestTimeout: 30000` (30s — very long); no circuit breaker. Under ES slowness, all 50 sockets fill and requests pile onto the event loop.
5. Retries: the ES client retries 3× on timeout with no backoff — a slow ES turns into 3× load.
6. Shedding/degradation: no load shedding; health check `/healthz` shares the same PG pool, so pool exhaustion also fails health checks → the orchestrator kills healthy pods mid-spike.

## Obstacle / missing evidence moment

> The agent can't confirm the live replica count or whether the rate-limit store was switched to Redis in the deployed environment (the repo shows in-memory, but infra may override).

## Exact evidence request

```
Show: the deployment replica count (kubectl get deploy search -o jsonpath='{.spec.replicas}')
      and whether RATE_LIMIT_STORE / REDIS_URL is set in the search env
Paste back: the replica count and those env values
```

The "2400/min effective fleet limit" figure is marked pending on the replica count and store.

## Expected output

```markdown
## Rate limiting & backpressure review: search API

**Capacity ceiling**: Postgres pool max=10 (auth lookup per request) (db.ts:8)
**Replica/storage note**: per-IP limit is in-memory × 8 replicas → ~2400/min fleet,
no aggregate cap on shared PG [pending replica count / Redis store]

**Cascading-collapse risks**
1. Health check shares the PG pool — under pool exhaustion /healthz fails → pods killed
   during the spike (cascade) — suggest: dedicated health path with no PG dependency
2. 30s ES timeout + 3 no-backoff retries — slow ES fills 50 sockets and triples load —
   suggest: cut timeout to ~2s, add circuit breaker + backoff/jitter (es-client.ts:12)

**Inbound limiting gaps**
- Per-IP limit is per-instance in-memory; shared PG unprotected at the fleet level —
  suggest: Redis-backed limiter, or a concurrency cap on the PG-touching path
- Limiter runs after auth (PG hit) — rejected requests still consume a connection —
  suggest: move the limiter before auth middleware

**Outbound backpressure gaps**
- ES client pool bounded (50) but no circuit breaker — dead ES has no fast-fail

**Degradation / shedding**
- No deliberate shedding; no fallback for ES outage on the search path

**Checked and clean**
- Over-limit returns 429 with Retry-After

**Pending evidence**: replica count and rate-limit store (awaiting user)
```

## Why this passes the quality bar

The binding ceiling is identified from real pool config, not assumed; the most dangerous finding (health check sharing the pool → orchestrator killing pods) is ranked first as a cascade; the per-instance-vs-fleet limit distinction is called out with the replica multiplier; the fleet-limit number is marked pending because replica count and store couldn't be confirmed.
