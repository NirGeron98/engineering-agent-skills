# Backend Developer Bundle

Optional role-based recommendations for engineers building and operating HTTP/RPC services: API contracts, live-system debugging, and access control.

For task-based discovery, start from [../SKILL_CATALOG.md](../SKILL_CATALOG.md).

## Minimum Start

Start with shared workflow skills, then add one backend skill that matches the work in front of you.

- [.claude/skills/repo-onboarding](../.claude/skills/repo-onboarding/SKILL.md) - map an unfamiliar service before changing it.
- [.claude/skills/diagnose-bug](../.claude/skills/diagnose-bug/SKILL.md) - fix a narrowed, reproducible bug from evidence.
- [.claude/skills/code-review](../.claude/skills/code-review/SKILL.md) - review implementation risk before merge.
- One of:
  - [.claude/skills/api-design-review](../.claude/skills/api-design-review/SKILL.md) - when the main work is an API contract.
  - [.claude/skills/service-debugging](../.claude/skills/service-debugging/SKILL.md) - when the main work is a deployed service failure.

## Recommended Set

After the minimum start feels useful, add these based on the kind of backend work you do most often.

- [.claude/skills/api-design-review](../.claude/skills/api-design-review/SKILL.md) - review API contracts for consistency, compatibility, pagination, idempotency, and error semantics.
- [.claude/skills/service-debugging](../.claude/skills/service-debugging/SKILL.md) - diagnose deployed service failures through logs, traces, metrics, config, and timeline evidence.
- [.claude/skills/auth-flow-review](../.claude/skills/auth-flow-review/SKILL.md) - verify authentication and authorization route by route, including object-level access.
- [.claude/skills/background-job-queue-review](../.claude/skills/background-job-queue-review/SKILL.md) - review async job/queue reliability: delivery semantics, retries, dead-lettering, idempotent consumers, ordering, and backlog.
- [.claude/skills/webhook-reliability-review](../.claude/skills/webhook-reliability-review/SKILL.md) - review webhook sending and receiving for signature verification, replay protection, retries, and idempotent receipt.
- [.claude/skills/idempotency-and-retry-safety-review](../.claude/skills/idempotency-and-retry-safety-review/SKILL.md) - audit which write operations are safe to retry or receive twice, and where duplicate-suppression must live.
- [.claude/skills/rate-limiting-and-backpressure-review](../.claude/skills/rate-limiting-and-backpressure-review/SKILL.md) - review inbound rate limiting and outbound backpressure so the service degrades instead of collapsing under load.
- [.claude/skills/task-breakdown](../.claude/skills/task-breakdown/SKILL.md) - turn a backend feature into an evidence-backed implementation plan.

## Situational Skills

Add frontend skills when browser or UI evidence owns the symptom. Add data skills when the failure is a warehouse, model, reconciliation, or freshness issue. Add DevOps/infrastructure skills when CI/CD, deployment state, IAM, Terraform, Kubernetes, incident coordination, or rollback safety is the main risk.

## Related Skills

- [.claude/skills/api-integration-debugging](../.claude/skills/api-integration-debugging/SKILL.md) when browser Network evidence must classify a frontend/backend boundary issue.
- [.claude/skills/deployment-debugging](../.claude/skills/deployment-debugging/SKILL.md) when a successful deploy leaves runtime state broken.
- [.claude/skills/iam-permissions-debugging](../.claude/skills/iam-permissions-debugging/SKILL.md) when the backend failure is a principal/action/resource permission problem.
