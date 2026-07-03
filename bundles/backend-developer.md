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
- [skills/api-and-backend/background-job-queue-review](../skills/api-and-backend/background-job-queue-review/SKILL.md) - review async job/queue reliability: delivery semantics, retries, dead-lettering, idempotent consumers, ordering, and backlog.
- [skills/api-and-backend/webhook-reliability-review](../skills/api-and-backend/webhook-reliability-review/SKILL.md) - review webhook sending and receiving for signature verification, replay protection, retries, and idempotent receipt.
- [skills/api-and-backend/idempotency-and-retry-safety-review](../skills/api-and-backend/idempotency-and-retry-safety-review/SKILL.md) - audit which write operations are safe to retry or receive twice, and where duplicate-suppression must live.
- [skills/api-and-backend/rate-limiting-and-backpressure-review](../skills/api-and-backend/rate-limiting-and-backpressure-review/SKILL.md) - review inbound rate limiting and outbound backpressure so the service degrades instead of collapsing under load.
- [skills/planning/task-breakdown](../skills/planning/task-breakdown/SKILL.md) - turn a backend feature into an evidence-backed implementation plan.

## Situational Skills

Add frontend skills when browser or UI evidence owns the symptom. Add data skills when the failure is a warehouse, model, reconciliation, or freshness issue. Add DevOps/infrastructure skills when CI/CD, deployment state, IAM, Terraform, Kubernetes, incident coordination, or rollback safety is the main risk.

## Related Skills

- [skills/frontend/api-integration-debugging](../skills/frontend/api-integration-debugging/SKILL.md) when browser Network evidence must classify a frontend/backend boundary issue.
- [skills/devops-infrastructure/deployment-debugging](../skills/devops-infrastructure/deployment-debugging/SKILL.md) when a successful deploy leaves runtime state broken.
- [skills/devops-infrastructure/iam-permissions-debugging](../skills/devops-infrastructure/iam-permissions-debugging/SKILL.md) when the backend failure is a principal/action/resource permission problem.
