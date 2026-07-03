# Rate Limiting & Backpressure Review — Anti-patterns

## Front-door-only protection
**Symptom**: a per-IP rate limiter at the edge, and every database/third-party call is unbounded with no timeout.
**Why it fails**: overload also arrives from the back — a slow dependency fills the connection pool and the service stalls regardless of the front-door limit.
**Instead**: review outbound timeouts, bounded pools, and circuit breakers with the same rigor as inbound limits.

## Per-instance limit mistaken for a fleet limit
**Symptom**: an in-memory token bucket of "100 req/s" running on 20 replicas.
**Why it fails**: the real fleet allowance is 2000/s, and it protects nothing shared (like the database) because each instance counts independently.
**Instead**: use a shared store for fleet-wide limits, or state explicitly that the limit is per-instance and compute the aggregate.

## Rejecting with 500
**Symptom**: over-limit or overloaded requests return 500.
**Why it fails**: clients (and gateways) retry 5xx, amplifying the overload; 500 also pages on-call for expected shedding.
**Instead**: return 429/503 with `Retry-After`; reserve 5xx for actual server faults.

## Expensive rejection
**Symptom**: the limiter runs after auth, DB lookups, and serialization, then rejects.
**Why it fails**: the work you were trying to avoid already happened; the limiter doesn't reduce load.
**Instead**: check the limit as early and cheaply as possible, before expensive work.

## Retry without backoff
**Symptom**: clients or internal callers retry immediately on failure, no jitter, no budget.
**Why it fails**: synchronized retries turn a brief blip into a self-sustaining storm that prevents recovery.
**Instead**: exponential backoff with jitter and a bounded retry budget; consider a circuit breaker.

## Unbounded absorbing queue
**Symptom**: incoming work is pushed onto an in-memory queue "so we don't drop anything".
**Why it fails**: under sustained overload the queue grows until OOM, and latency balloons long before that — dropping would have been kinder.
**Instead**: bound the queue and shed (reject) past the bound; backpressure to the caller.

## No timeout on downstream calls
**Symptom**: an HTTP/DB client with default (or no) timeout.
**Why it fails**: when the dependency hangs, every worker waiting on it is consumed; the pool empties and the service is down.
**Instead**: set aggressive, explicit timeouts on every outbound call; fail fast.

## One optional dependency sinks the ship
**Symptom**: a recommendation/analytics call in the critical request path with no fallback.
**Why it fails**: that non-critical service's outage takes down checkout.
**Instead**: make non-critical dependencies best-effort with a fallback; isolate them from critical paths.
