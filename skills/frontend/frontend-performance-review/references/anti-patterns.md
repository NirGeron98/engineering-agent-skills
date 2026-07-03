# Frontend Performance Review — Anti-patterns

## Optimize by folklore
**Symptom**: advice starts with memoization, virtualization, or lazy loading without a measurement.
**Instead**: measure the route/interaction, classify the bottleneck, then choose the fix.

## Dev-mode profiling
**Symptom**: React/Vue/Angular/Svelte dev build overhead is treated as production performance.
**Instead**: use production builds for load/runtime numbers, and dev tooling only for diagnosis where appropriate.

## Bundle tunnel vision
**Symptom**: shaving 20 KB gets priority while four serial API calls add two seconds.
**Instead**: compare bundle, network, render, and asset costs in the same user path.

## Lazy-load everything
**Symptom**: critical above-the-fold UI or data moves behind lazy boundaries and perceived load gets worse.
**Instead**: split non-critical work and keep the first meaningful path fast.

## Virtualization mismatch
**Symptom**: a list of 40 rows gets virtualization complexity, breaking accessibility or find-in-page.
**Instead**: virtualize only when row count/render cost justifies it and verify keyboard/screen-reader impact.

## Cache forever
**Symptom**: stale cache hides repeated calls but serves wrong data after mutations.
**Instead**: tune cache freshness/invalidation with `frontend-state-management` when correctness is at risk.
