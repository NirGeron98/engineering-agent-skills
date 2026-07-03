---
name: throughput-bottleneck-triage
description: Triage why a service or system cannot meet target QPS, throughput, concurrency, or job-processing rate by identifying the actual saturated layer (CPU, memory, IO, network, DB, or concurrency limit). Use when scaling workers/instances hasn't fixed a throughput ceiling. Produces a throughput bottleneck triage with a bottleneck hypothesis, evidence table, and safe experiments.
---

# Throughput Bottleneck Triage

## Goal

Turn a throughput ceiling into an evidence-backed hypothesis about the saturated layer and a safe set of experiments to confirm and relieve it.

## When to use

- A service or system can't meet target QPS, throughput, or job-processing rate.
- Adding workers/instances hasn't increased throughput, suggesting a shared bottleneck.
- The saturation point and scaling behavior need to be understood before choosing a fix.

## When not to use

- The bottleneck is clearly a specific database query; use `skills/database/query-performance-review`.
- The concern is only whether alerts/metrics for throughput are well-designed, not diagnosing the bottleneck itself; use `skills/observability/metrics-and-alerts-review`.
- The question is release/rollout readiness for a scaling change, not the diagnosis itself; use `skills/devops-infrastructure/release-and-rollback-readiness`.

## Composition and handoff rules

- Defer query-specific bottlenecks to `skills/database/query-performance-review`.
- Defer alert/metrics quality to `skills/observability/metrics-and-alerts-review`.
- Defer infrastructure rollout readiness to `skills/devops-infrastructure/release-and-rollback-readiness`.

## Evidence access rule

Inspect actual throughput metrics (target vs actual), resource utilization (CPU/memory/IO/network/DB), queue depth, concurrency/connection limits at each layer, external service/API limits or downstream quotas, and error/retry rates. If utilization metrics, queue depth, concurrency limits, external limits, or retry rates are unavailable, request exact dashboards/commands and mark bottleneck-layer conclusions pending.

## Workflow

1. Confirm target vs actual throughput and the workload shape (request size, job type, arrival pattern).
2. Check resource utilization at each layer (CPU, memory, IO, network, DB) for signs of saturation.
3. Check queue depth, concurrency limits, and backpressure behavior at each stage of the pipeline, including external service/API limits, downstream quotas, and error/retry rates (a retry storm can look like a throughput ceiling).
4. Correlate the saturation point with scaling attempts already made (did adding workers help at all, or plateau immediately).
5. State a bottleneck hypothesis ranked by evidence strength, and propose safe experiments to confirm it (not yet destructive/production-risking changes).
6. Produce scaling/optimization options once the bottleneck layer is confirmed, with a verification plan.
7. Before final handoff, load `references/checklist.md`; consult `references/anti-patterns.md` when tempted to recommend scaling without confirming which layer is saturated.

## Questions to ask (only when necessary)

- What is the target throughput, and what happened to actual throughput when workers/instances were added?
- Can I get utilization metrics (CPU/memory/IO/network/DB) and queue depth during the throughput ceiling?

## Quality bar

- The bottleneck hypothesis is tied to actual utilization/queue evidence at a specific layer, not assumed from "we added workers and it didn't help."
- Experiments proposed to confirm the hypothesis are safe and reversible.
- Scaling/optimization recommendations target the confirmed saturated layer, not a generic "add more resources."

## Expected output format

```markdown
## Throughput bottleneck triage: <scope>

**Bottleneck hypothesis**: <layer and reasoning>
**Evidence table**: <utilization, queue depth, concurrency limits per layer>
**Safe experiments**: <steps to confirm hypothesis>
**Scaling/optimization options**: <steps, once confirmed>
**Verification plan**: <steps>
**Pending evidence**: <exact requests>
```

## Failure modes to avoid

- **Throughput by scaling only**: adding more workers/instances repeatedly without confirming which layer is actually saturated.
- **Utilization theater**: eyeballing dashboards without checking actual utilization numbers at each layer.
- **Synthetic load as launch proof**: confirming a fix under unrealistic load shape instead of the actual production workload pattern.
- **External-limit blindness**: attributing a ceiling only to internal saturation without checking downstream/external service limits, quotas, rate limits, and retry/error amplification.

More in `references/anti-patterns.md`.
