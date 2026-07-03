# DevOps / Cloud Engineer Bundle

Optional role-based recommendations for engineers handling CI/CD, deployments, infrastructure, cloud IAM, Kubernetes workloads, incidents, release readiness, and rollback workflows.

For task-based discovery, start from [../SKILL_CATALOG.md](../SKILL_CATALOG.md).

## Minimum Start

Start with shared workflow skills, then add one DevOps/infrastructure skill that matches the current work.

- [skills/planning/repo-onboarding](../skills/planning/repo-onboarding/SKILL.md) - map the repo, scripts, deployment surfaces, and test commands.
- [skills/debugging/diagnose-bug](../skills/debugging/diagnose-bug/SKILL.md) - fix a narrowed, reproducible code defect after operational evidence points there.
- [skills/review/code-review](../skills/review/code-review/SKILL.md) - review implementation or config risk before merge.
- One of:
  - [skills/devops-infrastructure/ci-cd-debugging](../skills/devops-infrastructure/ci-cd-debugging/SKILL.md) - when the current work is a pipeline failure or deploy trigger.
  - [skills/devops-infrastructure/deployment-debugging](../skills/devops-infrastructure/deployment-debugging/SKILL.md) - when the current work is a broken deployed runtime.
  - [skills/devops-infrastructure/terraform-plan-review](../skills/devops-infrastructure/terraform-plan-review/SKILL.md) - when the current work is Terraform/OpenTofu review before apply.
  - [skills/devops-infrastructure/iam-permissions-debugging](../skills/devops-infrastructure/iam-permissions-debugging/SKILL.md) - when the current work is permission denied, forbidden, identity, impersonation, or RBAC failure.
  - [skills/devops-infrastructure/kubernetes-workload-debugging](../skills/devops-infrastructure/kubernetes-workload-debugging/SKILL.md) - when the current work is a Kubernetes workload stuck, crashing, unready, or misrouted.

## Recommended Set

After the minimum start feels useful, add these based on the kind of DevOps, cloud, or infrastructure work you do most often.

- [skills/devops-infrastructure/ci-cd-debugging](../skills/devops-infrastructure/ci-cd-debugging/SKILL.md) - debug pipeline failures, flakes, cache/dependency problems, artifacts, secrets, tokens, and deploy triggers.
- [skills/devops-infrastructure/deployment-debugging](../skills/devops-infrastructure/deployment-debugging/SKILL.md) - debug successful deployments whose runtime service, app, job, worker, or endpoint is broken.
- [skills/devops-infrastructure/terraform-plan-review](../skills/devops-infrastructure/terraform-plan-review/SKILL.md) - review Terraform/OpenTofu plans before apply for replacement, destroy, IAM, state, provider, and production risk.
- [skills/devops-infrastructure/iam-permissions-debugging](../skills/devops-infrastructure/iam-permissions-debugging/SKILL.md) - debug permission denied, forbidden, token scope, service identity, impersonation, or RBAC failures with least privilege.
- [skills/devops-infrastructure/kubernetes-workload-debugging](../skills/devops-infrastructure/kubernetes-workload-debugging/SKILL.md) - debug Kubernetes workloads that are Pending, crashing, unready, misrouted, or behaving differently inside the cluster.

## Situational Skills

- [skills/devops-infrastructure/incident-triage-runbook](../skills/devops-infrastructure/incident-triage-runbook/SKILL.md) - use during active or recent incidents when severity, impact, ownership, communication, mitigation, and follow-up need structure.
- [skills/devops-infrastructure/release-and-rollback-readiness](../skills/devops-infrastructure/release-and-rollback-readiness/SKILL.md) - use for go/no-go readiness, rollback checks, monitoring coverage, ownership, and post-release verification.

## Related Skills

- [skills/api-and-backend/service-debugging](../skills/api-and-backend/service-debugging/SKILL.md) when operational evidence proves deployed runtime state is healthy and the defect is service behavior.
- [skills/frontend/api-integration-debugging](../skills/frontend/api-integration-debugging/SKILL.md) when browser Network evidence is needed to classify a frontend/backend boundary issue.
- [skills/data/db-to-dwh-pipeline](../skills/data/db-to-dwh-pipeline/SKILL.md) when the operational symptom is really ingestion, replication, reconciliation, or warehouse correctness.
