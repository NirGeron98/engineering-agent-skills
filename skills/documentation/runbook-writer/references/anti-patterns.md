# Runbook Writer Anti-Patterns

## Runbook that starts with mutation

- **Symptom**: The first step restarts, deletes, applies, purges, or scales.
- **Why it matters**: Responders can worsen incidents before understanding them.
- **Correction**: Start with read-only evidence and explicit stop conditions.

## Command without expectation

- **Symptom**: The runbook lists commands but not what output means.
- **Why it matters**: Operators cannot decide the next step safely.
- **Correction**: Add expected healthy/unhealthy signals and interpretation.

## Hidden permissions

- **Symptom**: Steps assume console, production, cluster, or cloud access.
- **Why it matters**: The runbook fails during incidents.
- **Correction**: Document prerequisites, roles, escalation, and fallback evidence requests.

## No rollback validation

- **Symptom**: Mitigation or rollback steps do not say how to prove recovery.
- **Why it matters**: Responders may stop before service is healthy.
- **Correction**: Add validation checks and post-action monitoring.

