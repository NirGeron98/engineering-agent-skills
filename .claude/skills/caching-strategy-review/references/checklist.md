# Caching Strategy Review Checklist

- [ ] The cache's intended purpose (latency, load, cost) is stated explicitly.
- [ ] Actual freshness/staleness requirements for the data were confirmed, not assumed.
- [ ] Read/write ratio and access pattern were checked before recommending caching.
- [ ] Cache key design was reviewed for correctness (uniqueness, scope, versioning).
- [ ] TTL and/or explicit invalidation strategy is concrete, with reasoning for the chosen value.
- [ ] Stampede risk (concurrent misses on hot-key expiry) is addressed.
- [ ] Fallback behavior when the cache is unavailable was checked, not assumed safe.
- [ ] Observability recommendations (hit rate, staleness monitoring) are included.
- [ ] Missing read/write metrics or existing cache config are marked pending.
- [ ] Data consistency incidents (if raised) are deferred, not diagnosed here.
