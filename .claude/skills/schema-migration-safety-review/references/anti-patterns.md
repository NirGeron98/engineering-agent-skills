# Schema Migration Safety Review Anti-Patterns

## Rollback fantasy

- **Symptom**: A rollback plan is stated as "just revert the migration" without checking whether the operation is actually reversible (e.g., dropped columns, lost data).
- **Why it matters**: Some migrations destroy information that a rollback script cannot restore.
- **Correction**: Verify reversibility against the actual migration; if data loss is possible, say so and plan around it.

## Migration without compatibility window

- **Symptom**: A schema change is deployed assuming app code updates instantly, ignoring that old and new versions may run concurrently.
- **Why it matters**: This causes errors or silent data corruption during rollout.
- **Correction**: Explicitly design a compatibility window (e.g., additive-then-cleanup migrations).

## Backfill as afterthought

- **Symptom**: A backfill against a large live table is planned as a single unbatched operation.
- **Why it matters**: Large unbatched backfills can lock tables, exhaust resources, or cause replication lag.
- **Correction**: Batch and throttle backfills, and monitor lock/replication impact.

## Explain plan theater (migration variant)

- **Symptom**: Migration risk is judged from the SQL statement type alone (e.g., "it's just adding a column") without checking table size or engine-specific lock behavior.
- **Why it matters**: The same statement type can be instant or table-locking depending on data volume and engine.
- **Correction**: Check actual table size and engine behavior for this specific operation.

## Silent breakage

- **Symptom**: A constraint or column is dropped without checking whether other services or reports depend on it.
- **Why it matters**: Dependents fail silently or loudly after the migration ships.
- **Correction**: Check for dependents before dropping any schema element.
