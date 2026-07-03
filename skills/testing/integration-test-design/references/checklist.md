# Integration Test Design Checklist

- [ ] Boundary under test and out-of-scope dependencies are explicit.
- [ ] Both sides of the boundary, contract, existing tests, fixtures, cleanup, and CI constraints were inspected or requested.
- [ ] Real vs fake dependency choices are justified by risk, determinism, cost, and diagnostics.
- [ ] Fixture/data setup, isolation, teardown, and idempotency are defined.
- [ ] Test cases cover success, contract mismatch, persistence, retry/idempotency, cleanup, and representative failures where relevant.
- [ ] CI runtime, parallelism, external services, and flake risk are addressed.
- [ ] Failure diagnostics are planned.
- [ ] Missing repo access, command output, service contract, logs, or CI timings are marked pending.

