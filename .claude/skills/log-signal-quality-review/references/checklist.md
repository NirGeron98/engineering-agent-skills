# Log Signal Quality Review Checklist

- [ ] Actual log samples (normal and failure conditions) were inspected, not recalled from memory.
- [ ] Noise and sparsity findings point to specific log lines/points.
- [ ] Correlation/context gaps (request/trace/tenant IDs, structured fields) are checked.
- [ ] Sensitive-data logging risk is checked without treating it as an already-confirmed incident.
- [ ] Recommended changes are specific (add/remove/restructure this log point), not generic.
- [ ] Verification examples show before/after log output.
- [ ] Missing log samples, logging code, or log-level config is marked pending.
- [ ] Any exposure-level sensitive-data finding notes a handoff to secrets-exposure-review.
