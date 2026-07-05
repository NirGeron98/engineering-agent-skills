# Trace-Based Latency Diagnosis Checklist

- [ ] Actual trace/span data for a representative slow request was inspected, not assumed.
- [ ] Latency breakdown identifies which span or gap consumes the most time.
- [ ] Bottleneck hypothesis is backed by span evidence, not the request name or symptom alone.
- [ ] Ruled-out causes are recorded, not just the winning hypothesis.
- [ ] Missing span/instrumentation gaps are explicitly noted, not treated as zero-cost.
- [ ] Conclusions are checked against more than one trace when possible, or noted as single-sample.
- [ ] Missing trace data/exports are marked pending.
- [ ] Next verification steps are concrete (add instrumentation, compare traces).
