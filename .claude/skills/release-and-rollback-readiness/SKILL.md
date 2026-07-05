---
name: release-and-rollback-readiness
description: Review whether a deployment, release, migration rollout, infrastructure change, or production cutover is safe to proceed and how to roll back. Use before production or shared-environment changes when scope, changed services/resources, deployment plan, smoke tests, health checks, alerts, feature flags, config/env changes, database migrations, Terraform/infrastructure, compatibility, owners, monitoring, and go/no-go decision must be assessed; produces a release readiness report, go/no-go decision, pre-release checks, rollback plan, monitoring plan, and post-release verification steps. Hands PR review to code-review, API contract risk to api-design-review, Terraform plan risk to terraform-plan-review, Kubernetes workload readiness to kubernetes-workload-debugging, and active incidents to incident-triage-runbook.
---

# Release and Rollback Readiness

## Goal

Decide whether a release or production change should proceed, with concrete verification, monitoring, ownership, and rollback before execution.

## When to use

- Before a deployment, migration rollout, infrastructure change, cutover, or production release.
- A team needs a go/no-go decision with rollback and monitoring.
- The release touches config, feature flags, database migrations, infrastructure, services, jobs, or user-facing behavior.

## When not to use

- The task is a PR implementation review; use `code-review`.
- A Terraform plan needs detailed resource risk review; use `terraform-plan-review`.
- The release is already causing an incident; use `incident-triage-runbook`.
- Kubernetes workload health is the central question; use `kubernetes-workload-debugging`.

## Composition and handoff rules

- Route API compatibility concerns to `api-design-review`.
- Route Terraform plan risk to `terraform-plan-review`.
- Route deployment failure after release to `deployment-debugging`.
- Route active mitigation and communication to `incident-triage-runbook`.

## Evidence access rule

If release notes, diff, deploy plan, migration plan, dashboards, alerts, smoke tests, or rollback commands are unavailable, request exact artifacts and mark go/no-go conclusions pending. Do not mark a production release ready without a rollback path or an explicit reason rollback is impossible.

## Workflow

1. Define release scope: services/resources, environments, users, data, infra, flags, migrations, and owners.
2. Verify deployment plan: sequence, gates, CI status, artifact versions, config/env changes, and compatibility.
3. Review pre-release checks: tests, smoke tests, health checks, data migration dry runs, Terraform plan review, and stakeholder approvals.
4. Review rollback: previous version, data compatibility, migration reversal/forward fix, flag disable, traffic rollback, state recovery, and owner.
5. Review monitoring: dashboards, alerts, logs, SLOs, synthetic checks, business metrics, and observation window.
6. Identify risks and required conditions; decide go, go with conditions, or no-go.
7. Produce post-release verification steps and communication notes.
8. Before final handoff, load `references/checklist.md`; consult `references/anti-patterns.md` when tempted to approve without rollback, skip monitoring, or ignore data/infra compatibility.

## Output format

```markdown
## Release readiness report: <release>

**Scope**: <services/resources/users/data/infra>
**Decision**: <go / go with conditions / no-go>

**Required pre-release checks**
- <check> - status: <confirmed/pending> - owner: <owner>

**Rollback plan**: <steps, owner, constraints>
**Monitoring plan**: <dashboards/alerts/logs/window>
**Post-release verification**: <exact checks>
**Risks/conditions**: <known risks and no-go triggers>
```

## Final handoff requirements

- Load `references/checklist.md` and verify the readiness decision against it.
- Consult `references/anti-patterns.md` when ambiguity, risk, or shortcut temptation appears.
- Mark any go/no-go, rollback, monitoring, migration, infrastructure, or compatibility conclusion that depends on missing evidence as pending.

## Failure modes to avoid

- **Rollback-free approval**: saying ready without a recovery path.
- **Green CI equals release-ready**: ignoring runtime, data, infra, and monitoring checks.
- **Migration optimism**: approving irreversible data changes without compatibility and recovery notes.

More in `references/anti-patterns.md`.
