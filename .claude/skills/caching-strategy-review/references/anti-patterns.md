# Caching Strategy Review Anti-Patterns

## Cache as magic

- **Symptom**: Caching is recommended as a default fix for "make it faster" without checking freshness requirements or access pattern.
- **Why it matters**: Caching the wrong data path introduces staleness risk for little or no real performance benefit.
- **Correction**: Confirm read/write ratio and freshness tolerance before recommending a cache.

## Stampede blindness

- **Symptom**: A cache design has no plan for what happens when a hot key expires and many requests miss simultaneously.
- **Why it matters**: Cache stampedes can cause a thundering-herd load spike on the underlying store, sometimes worse than having no cache.
- **Correction**: Use request coalescing, jittered TTLs, or stale-while-revalidate patterns for hot keys.

## Fallback assumption

- **Symptom**: The system is assumed to "just fall back to the source" safely if the cache is down, without checking that path.
- **Why it matters**: An unverified fallback path can itself fail or overload the source system during a cache outage.
- **Correction**: Explicitly verify and test the fallback behavior when the cache is unavailable.

## TTL by convention

- **Symptom**: A TTL value is copied from another cache ("we usually use 5 minutes") without reasoning about this data's actual freshness need.
- **Why it matters**: An arbitrary TTL can either serve stale data too long or invalidate too often to help performance.
- **Correction**: Choose TTL based on this data's actual acceptable staleness window.

## Rollback fantasy (cache variant)

- **Symptom**: Removing a cache is assumed to be a simple, safe change with no consistency or load implications.
- **Why it matters**: Removing a cache can expose the underlying store to load it was never sized for.
- **Correction**: Check underlying store capacity and access pattern before removing an existing cache.
