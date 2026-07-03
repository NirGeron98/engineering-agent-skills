# Memory Leak Diagnosis Checklist

- [ ] Actual memory metrics over time were inspected to confirm the growth pattern.
- [ ] Heap/profile snapshots (ideally two points in time) were obtained and diffed.
- [ ] Suspected retention paths (caches, queues, timers, listeners, object lifecycle) were traced in code.
- [ ] Growth was correlated with a specific workload pattern where possible.
- [ ] Ruled-out causes are stated explicitly with reasons.
- [ ] The suspected retention path is tied to heap diff/profile evidence, not assumed.
- [ ] The mitigation plan fixes the retention path, not just resource limits or restarts.
- [ ] A verification loop repeats the same measurement after the fix.
- [ ] Missing memory metrics or heap profiles are marked pending.
- [ ] Kubernetes/pod-level OOM policy questions are deferred, not answered here.
