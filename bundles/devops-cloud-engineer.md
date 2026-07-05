# DevOps / Cloud Engineer Bundle

Optional role-based recommendations for engineers handling CI/CD, deployments, infrastructure, cloud IAM, Kubernetes workloads, incidents, release readiness, and rollback workflows.

For task-based discovery, start from [../SKILL_CATALOG.md](../SKILL_CATALOG.md).

## Minimum Start

Start with shared workflow skills, then add one DevOps/infrastructure skill that matches the current work.

- [.claude/skills/repo-onboarding](../.claude/skills/repo-onboarding/SKILL.md) - map the repo, scripts, deployment surfaces, and test commands.
- [.claude/skills/diagnose-bug](../.claude/skills/diagnose-bug/SKILL.md) - fix a narrowed, reproducible code defect after operational evidence points there.
- [.claude/skills/code-review](../.claude/skills/code-review/SKILL.md) - review implementation or config risk before merge.
- One of:
  - [.claude/skills/ci-cd-debugging](../.claude/skills/ci-cd-debugging/SKILL.md) - when the current work is a pipeline failure or deploy trigger.
  - [.claude/skills/deployment-debugging](../.claude/skills/deployment-debugging/SKILL.md) - when the current work is a broken deployed runtime.
  - [.claude/skills/terraform-plan-review](../.claude/skills/terraform-plan-review/SKILL.md) - when the current work is Terraform/OpenTofu review before apply.
  - [.claude/skills/iam-permissions-debugging](../.claude/skills/iam-permissions-debugging/SKILL.md) - when the current work is permission denied, forbidden, identity, impersonation, or RBAC failure.
  - [.claude/skills/kubernetes-workload-debugging](../.claude/skills/kubernetes-workload-debugging/SKILL.md) - when the current work is a Kubernetes workload stuck, crashing, unready, or misrouted.

## Recommended Set

After the minimum start feels useful, add these based on the kind of DevOps, cloud, or infrastructure work you do most often.

- [.claude/skills/ci-cd-debugging](../.claude/skills/ci-cd-debugging/SKILL.md) - debug pipeline failures, flakes, cache/dependency problems, artifacts, secrets, tokens, and deploy triggers.
- [.claude/skills/deployment-debugging](../.claude/skills/deployment-debugging/SKILL.md) - debug successful deployments whose runtime service, app, job, worker, or endpoint is broken.
- [.claude/skills/terraform-plan-review](../.claude/skills/terraform-plan-review/SKILL.md) - review Terraform/OpenTofu plans before apply for replacement, destroy, IAM, state, provider, and production risk.
- [.claude/skills/iam-permissions-debugging](../.claude/skills/iam-permissions-debugging/SKILL.md) - debug permission denied, forbidden, token scope, service identity, impersonation, or RBAC failures with least privilege.
- [.claude/skills/kubernetes-workload-debugging](../.claude/skills/kubernetes-workload-debugging/SKILL.md) - debug Kubernetes workloads that are Pending, crashing, unready, misrouted, or behaving differently inside the cluster.

## Situational Skills

- [.claude/skills/incident-triage-runbook](../.claude/skills/incident-triage-runbook/SKILL.md) - use during active or recent incidents when severity, impact, ownership, communication, mitigation, and follow-up need structure.
- [.claude/skills/release-and-rollback-readiness](../.claude/skills/release-and-rollback-readiness/SKILL.md) - use for go/no-go readiness, rollback checks, monitoring coverage, ownership, and post-release verification.

## Related Skills

- [.claude/skills/service-debugging](../.claude/skills/service-debugging/SKILL.md) when operational evidence proves deployed runtime state is healthy and the defect is service behavior.
- [.claude/skills/api-integration-debugging](../.claude/skills/api-integration-debugging/SKILL.md) when browser Network evidence is needed to classify a frontend/backend boundary issue.
- [.claude/skills/db-to-dwh-pipeline](../.claude/skills/db-to-dwh-pipeline/SKILL.md) when the operational symptom is really ingestion, replication, reconciliation, or warehouse correctness.
