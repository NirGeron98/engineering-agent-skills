# Rate Limiting & Backpressure Review — Checklist

## Capacity chain
- [ ] Finite resources on the path listed (worker pool, event-loop concurrency, DB pool, downstream client pool, memory)
- [ ] Smallest / binding ceiling identified from config
- [ ] Protection confirmed to engage before that ceiling is exhausted

## Inbound limiting
- [ ] Limiter location(s) found (LB/gateway/middleware/per-route)
- [ ] Algorithm recorded (token bucket / fixed / sliding window)
- [ ] Scope recorded (per-IP / per-user / per-tenant / global)
- [ ] Limit + burst values recorded
- [ ] Storage backing recorded; per-instance vs shared called out (× replica count)
- [ ] Expensive endpoints limited tighter than cheap ones
- [ ] Rejection is cheap (no expensive work before the 429)

## Over-limit response
- [ ] Returns 429 (not 500) with Retry-After
- [ ] Both authenticated and unauthenticated abuse considered

## Outbound backpressure
- [ ] Timeout set on every downstream call (no unbounded waits)
- [ ] Connection/client pools bounded
- [ ] Circuit breaker / failure threshold for dead dependencies (fail fast)
- [ ] Retries have backoff + jitter + a budget (retry-storm risk assessed)
- [ ] No unbounded in-memory queue absorbing backlog until OOM

## Degradation & shedding
- [ ] Deliberate load shedding under sustained overload (reject early / drop low-priority)
- [ ] Health checks / critical paths protected from starvation
- [ ] Fallbacks for failed non-critical dependencies; they can't take down critical paths

## Observability & client experience
- [ ] Throttle/rejection rate is observable (metrics)
- [ ] Client-facing limits documented; behavior doesn't push good clients into hammering

## Reporting
- [ ] Findings ranked by failure severity under load
- [ ] Every finding cites config/code evidence
- [ ] Pending evidence listed where access was missing
