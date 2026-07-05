# Throughput Bottleneck Triage Checklist

- [ ] Target vs actual throughput and workload shape were confirmed.
- [ ] Resource utilization (CPU/memory/IO/network/DB) was inspected at each candidate layer.
- [ ] Queue depth and concurrency/connection limits were checked at each pipeline stage.
- [ ] The effect of prior scaling attempts (adding workers/instances) was reviewed as evidence.
- [ ] The bottleneck hypothesis is tied to specific utilization/queue evidence, not assumption.
- [ ] Proposed experiments to confirm the hypothesis are safe and reversible.
- [ ] Scaling/optimization recommendations target the confirmed saturated layer.
- [ ] A verification plan uses a workload shape representative of production.
- [ ] Missing utilization metrics, queue depth, or concurrency limits are marked pending.
- [ ] Alert/metric-quality concerns (if raised) are deferred, not solved here.
