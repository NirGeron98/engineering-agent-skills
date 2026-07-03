# Backend Developer Bundle

Optional role-based recommendations for engineers building and operating HTTP/RPC services: API contracts, live-system debugging, and access control.

For task-based discovery, start from [../SKILL_CATALOG.md](../SKILL_CATALOG.md).

## Minimum Start

Start with shared workflow skills, then add one backend skill that matches the work in front of you.

- [skills/planning/repo-onboarding](../skills/planning/repo-onboarding/SKILL.md) - map an unfamiliar service before changing it.
- [skills/debugging/diagnose-bug](../skills/debugging/diagnose-bug/SKILL.md) - fix a narrowed, reproducible bug from evidence.
- [skills/review/code-review](../skills/review/code-review/SKILL.md) - review implementation risk before merge.
- One of:
  - [skills/api-and-backend/api-design-review](../skills/api-and-backend/api-design-review/SKILL.md) - when the main work is an API contract.
  - [skills/api-and-backend/service-debugging](../skills/api-and-backend/service-debugging/SKILL.md) - when the main work is a deployed service failure.

## Recommended Set

After the minimum start feels useful, add these based on the kind of backend work you do most often.

- [skills/api-and-backend/api-design-review](../skills/api-and-backend/api-design-review/SKILL.md) - review API contracts for consistency, compatibility, pagination, idempotency, and error semantics.
- [skills/api-and-backend/service-debugging](../skills/api-and-backend/service-debugging/SKILL.md) - diagnose deployed service failures through logs, traces, metrics, config, and timeline evidence.
- [skills/api-and-backend/auth-flow-review](../skills/api-and-backend/auth-flow-review/SKILL.md) - verify authentication and authorization route by route, including object-level access.
- [skills/planning/task-breakdown](../skills/planning/task-breakdown/SKILL.md) - turn a backend feature into an evidence-backed implementation plan.

## Situational Skills

Add frontend skills when browser or UI evidence owns the symptom. Add data skills when the failure is a warehouse, model, reconciliation, or freshness issue. Add DevOps/infrastructure skills when CI/CD, deployment state, IAM, Terraform, Kubernetes, incident coordination, or rollback safety is the main risk.

## Related Skills

- [skills/frontend/api-integration-debugging](../skills/frontend/api-integration-debugging/SKILL.md) when browser Network evidence must classify a frontend/backend boundary issue.
- [skills/devops-infrastructure/deployment-debugging](../skills/devops-infrastructure/deployment-debugging/SKILL.md) when a successful deploy leaves runtime state broken.
- [skills/devops-infrastructure/iam-permissions-debugging](../skills/devops-infrastructure/iam-permissions-debugging/SKILL.md) when the backend failure is a principal/action/resource permission problem.
