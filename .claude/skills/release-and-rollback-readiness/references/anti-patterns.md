# Release and Rollback Readiness - Anti-patterns

## Rollback-free approval
**Symptom**: a production release is marked ready without a rollback path.
**Why it matters**: recovery becomes improvised during failure.
**Correction**: require rollback steps, owner, verification, or an explicit rollback-impossible risk decision.

## Green CI equals release-ready
**Symptom**: passing CI is treated as sufficient for production.
**Why it matters**: runtime config, migrations, monitoring, and compatibility can still fail.
**Correction**: check deploy plan, smoke tests, health checks, alerts, and rollback.

## Migration optimism
**Symptom**: schema/data changes are approved without backward compatibility or recovery.
**Why it matters**: rollbacks can fail if old code cannot read new data or migrations are destructive.
**Correction**: verify expand/contract plan, backups, forward-fix plan, and read/write compatibility.

## Monitoring afterthought
**Symptom**: the release proceeds with no observation window or alert owner.
**Why it matters**: failures can be detected first by customers.
**Correction**: define dashboards, alerts, logs, synthetics, business metrics, and no-go triggers.

## Infra side quest
**Symptom**: Terraform or network changes are waved through inside a release checklist.
**Why it matters**: infrastructure changes carry independent blast radius.
**Correction**: route plan risk to `terraform-plan-review` and include its decision as a condition.
