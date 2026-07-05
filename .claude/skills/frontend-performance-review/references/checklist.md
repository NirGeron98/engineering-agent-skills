# Frontend Performance Review — Checklist

## Evidence
- [ ] Route/interaction and user scenario defined
- [ ] Current metric captured or exact pending capture requested
- [ ] Tool/environment recorded: device, browser, network, build mode, data volume
- [ ] Existing performance budgets or baselines checked

## Measurement areas
- [ ] Bundle/build output and route chunks checked when load is affected
- [ ] Network waterfall checked for repeated, serial, or oversized requests
- [ ] Render/profiler evidence checked for interaction or jank issues
- [ ] Hydration/SSR/client boot path checked where applicable
- [ ] Images, fonts, and third-party scripts checked
- [ ] Cache headers/query cache/local cache behavior checked
- [ ] Core Web Vitals considered for user-facing pages

## Diagnosis
- [ ] Primary bottleneck classified
- [ ] Neighboring bottlenecks excluded with evidence
- [ ] Responsible files/imports/fetches/components inspected
- [ ] Data volume and worst-case states considered

## Fix & verification
- [ ] Fix removes or defers measured work
- [ ] Route splitting/lazy loading does not delay critical path incorrectly
- [ ] Fetch orchestration avoids duplicate or serial calls where parallel/cache is correct
- [ ] Memoization/virtualization only proposed with measured need
- [ ] Same measurement repeated after change or exact verification command supplied

## Handoff
- [ ] Findings prioritized by expected user impact
- [ ] Pending production-like evidence identified
- [ ] Regression test/budget/monitoring follow-up named when useful
