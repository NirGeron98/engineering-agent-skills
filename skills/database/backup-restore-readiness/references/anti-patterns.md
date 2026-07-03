# Backup Restore Readiness Anti-Patterns

## Untested backup as ready

- **Symptom**: A backup schedule existing is treated as proof that recovery works.
- **Why it matters**: Backups can be silently corrupt, incomplete, or restore-incompatible without a real test ever surfacing it.
- **Correction**: Require a verified, recent restore test before calling a system restore-ready.

## RPO/RTO by assumption

- **Symptom**: Stated RPO/RTO targets are assumed met because backups run "regularly."
- **Why it matters**: Backup frequency alone doesn't guarantee recovery time or acceptable data loss window without a tested restore process.
- **Correction**: Validate RPO/RTO against actual restore test timing and backup recency evidence.

## Restore procedure drift

- **Symptom**: A restore runbook written months/years ago is trusted without checking it still matches current backup tooling/format.
- **Why it matters**: Infrastructure and backup tooling change; a stale runbook can fail exactly when needed.
- **Correction**: Re-validate the restore procedure against current backup configuration periodically.

## Rollback fantasy (backup variant)

- **Symptom**: A restore is assumed to "just work" in an emergency without a named owner or rehearsed steps.
- **Why it matters**: Under real incident pressure, an unrehearsed restore process is a major source of extended downtime.
- **Correction**: Name an owner and rehearse the restore process before relying on it.

## Synthetic load as launch proof (backup variant)

- **Symptom**: A restore test is performed only on a small or synthetic dataset, then readiness is declared for the full production dataset.
- **Why it matters**: Restore time and failure modes can scale non-linearly with real data volume.
- **Correction**: Test restore against production-representative volume where feasible, and note the gap otherwise.
