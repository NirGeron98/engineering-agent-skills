# Frontend State Management — Anti-patterns

## Global store gravity
**Symptom**: every awkward prop or duplicated value is moved into a global store.
**Instead**: use the narrowest owner that matches actual consumers; local state is a feature, not a failure.

## Derived-state drift
**Symptom**: `selectedCount`, filtered rows, or display labels are stored separately from the values that define them.
**Instead**: derive during render/computation unless profiling proves it is too expensive.

## URL amnesia
**Symptom**: filters, tabs, and pagination vanish on refresh or cannot be shared despite being page identity.
**Instead**: put navigational state in the URL when the state defines the view.

## Server cache shadowing
**Symptom**: API response data is copied into component/store state and then diverges from the cache.
**Instead**: use the server cache as source of truth and store only local draft or optimistic metadata.

## Last response wins
**Symptom**: a slow request for old filters overwrites a fast request for new filters.
**Instead**: cancel, ignore stale responses, or key requests by the full state that produced them.

## Memoization as medicine
**Symptom**: stale state or re-render bugs are treated with `memo`, computed caching, or selectors before ownership is fixed.
**Instead**: fix source-of-truth and invalidation first; optimize only measured hot paths.
