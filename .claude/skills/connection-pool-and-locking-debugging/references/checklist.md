# Connection Pool and Locking Debugging Checklist

- [ ] Actual error messages and their timing/load pattern were collected.
- [ ] DB-side evidence (connection count, pool saturation, lock/wait, deadlock logs) was inspected.
- [ ] App-side pool configuration (size, timeout, max lifetime) was inspected.
- [ ] Connection lifecycle (checkout/release) and transaction boundaries in code were checked.
- [ ] Correlation with recent deploys, scaling events, or slow queries was checked.
- [ ] The root cause is tied to specific evidence, not guessed from symptoms.
- [ ] Mitigations address the underlying cause, not only resource limits (pool size/timeout).
- [ ] A verification loop under representative load is included.
- [ ] Missing metrics, pool config, or lock/wait evidence is marked pending.
- [ ] Incident coordination needs (if any) are deferred, not handled here.
