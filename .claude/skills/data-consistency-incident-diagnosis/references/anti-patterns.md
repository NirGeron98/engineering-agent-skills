# Data Consistency Incident Diagnosis Anti-Patterns

## Data correction without scope

- **Symptom**: Visible bad records are corrected immediately without confirming how many other records are affected.
- **Why it matters**: Partial correction leaves hidden inconsistency that resurfaces later, often worse.
- **Correction**: Confirm full affected scope (query the actual divergence condition) before correcting.

## Source-of-truth assumption

- **Symptom**: A store is assumed to be authoritative without confirming it with the system's owners or design docs.
- **Why it matters**: Reconciling toward the wrong source of truth propagates the error instead of fixing it.
- **Correction**: Confirm the declared source of truth before planning reconciliation direction.

## Suspicion treated as fact

- **Symptom**: A suspected cause (e.g., "probably a race condition") is stated as the confirmed root cause without tracing the path.
- **Why it matters**: Incorrect root-cause claims lead to fixes that don't address the actual issue.
- **Correction**: Label suspected causes as suspected until traced through actual code/logs/evidence.

## Cache as magic

- **Symptom**: A cache layer is assumed to always reflect the source of truth without checking invalidation logic.
- **Why it matters**: Cache staleness or invalidation bugs are a common source of consistency incidents.
- **Correction**: Trace cache invalidation/TTL behavior explicitly as part of the read path.

## Retry-without-idempotency blindness

- **Symptom**: Retries or reprocessing are assumed safe without checking whether the write path is idempotent.
- **Why it matters**: Non-idempotent retries can be the actual cause of duplication.
- **Correction**: Check idempotency keys/guards on the write path before ruling retries out as a cause.
