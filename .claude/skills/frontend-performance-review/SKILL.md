---
name: frontend-performance-review
description: Investigate or review frontend performance using measurement-first evidence: bundle size, route splitting, render waterfalls, repeated renders, data-fetch waterfalls, images/fonts, hydration, caching, and Core Web Vitals. Use when a route is slow, UI feels janky, bundle grows, repeated API calls appear, or hydration is costly; produces measured bottlenecks and prioritized fixes, not blind memoization advice.
---

# Frontend Performance Review

## Goal

Find the bottleneck that matters for the user-visible frontend performance problem, support it with measurements, and recommend the smallest fix that improves that measured path.

## When to use

- A route, interaction, or page load is slow.
- Bundle size, render count, hydration time, image/font loading, or repeated API calls are suspected.
- A PR changes data fetching, routing, rendering, assets, or build output in a performance-sensitive area.

## When not to use

- Backend latency/root-cause investigation after the frontend proves the server is slow; use `service-debugging`.
- State correctness or cache invalidation without a performance symptom; use `frontend-state-management`.
- General code review; use `code-review`.

## Workflow

**Evidence access rule:** when you cannot run the app, profiler, Lighthouse/Web Vitals, bundle analyzer, browser Network/Performance panel, or tests yourself, output the exact command or manual capture request and mark performance conclusions pending. Never recommend optimization from intuition alone.

1. **Define the performance target.** Name the route/interaction, device/network assumptions, current symptom, and target metric: load time, LCP, INP, CLS, TTFB, hydration, render duration, request count, or bundle bytes.
2. **Measure before changing.** Use existing scripts first: build analyzer, browser performance trace, React/Vue/Angular/Svelte profiler equivalents, Web Vitals, network waterfall, or test timings. If unavailable, request exact captures.
3. **Classify the bottleneck.** Bundle/route splitting, render waterfall, unnecessary re-renders, data-fetch waterfall, repeated API calls, image/font loading, hydration, cache policy, third-party script, or backend latency.
4. **Inspect the responsible code path.** Read route entry, imports, data fetching, suspense/loading boundaries, state/store selectors, expensive computations, asset loading, and caching. General principle first; framework examples only after.
5. **Choose targeted fixes.** Prefer removing work, splitting routes, fixing fetch orchestration/cache keys, sizing/lazy-loading assets, font strategy, and hydration boundaries before memoization. Memoize only a measured expensive computation or render path.
6. **Verify the delta.** Re-run the same measurement and compare before/after; if not implemented, provide exact verification commands and acceptance criteria.
7. **Write the handoff.** Before writing it, load `references/checklist.md` and verify every item; consult `references/anti-patterns.md` when tempted to optimize without measurement or blame re-renders prematurely.

## Questions to ask (only when necessary)

- Target user device/network or performance budget if not documented.
- Whether the priority is initial load, interaction latency, or bandwidth/cost.
- Access to production-like traces when local measurement would be misleading.

## Quality bar

- Every performance finding has a measurement or explicit pending capture.
- Fixes are tied to the measured bottleneck and include expected verification.
- Memoization, virtualization, and lazy loading are not recommended blindly.

## Expected output format

```markdown
## Frontend performance review: <route/interaction>

**Target**: <metric and user scenario>
**Measurements**: <before numbers, tools, environment, or pending captures>

**Bottleneck classification**: <primary bottleneck + evidence>
**Findings**
- <severity> — <issue> — evidence: <measurement/location> — fix: <change> — expected delta: <metric>

**Verification**: <after numbers or exact commands/checks>
**Handoff**: <priority order, risks, pending evidence>
```

## Failure modes to avoid

- **Memoization reflex**: adding memo/cached computed/selectors before proving render work is the bottleneck.
- **Lab-only certainty**: using one local fast-machine result to sign off a mobile/network-sensitive issue.
- **Waterfall blindness**: optimizing bundle bytes while repeated or serial API calls dominate the user wait.

More in `references/anti-patterns.md`.
