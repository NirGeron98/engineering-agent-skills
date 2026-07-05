---
name: access-control-boundary-review
description: Review service-to-service permissions, resource-level authorization, IAM boundaries, tenant isolation, admin/user boundaries, or internal permission models. Use when an access boundary review, privilege map, missing/overbroad permissions, confused-deputy or ownership-boundary risks, and a remediation plan are needed. Defensive only.
---

# Access Control Boundary Review

## Goal

Turn an internal permission model into an evidence-based privilege map and boundary review, surfacing overbroad access and confused-deputy risk without exploit detail.

## When to use

- Service-to-service permissions, resource-level authorization, tenant isolation, or admin/user boundaries need review.
- The user needs to know if a permission model has overbroad, missing, or ambiguous boundaries.
- Internal IAM/permission code (not cloud provider IAM debugging) needs a security review.

## When not to use

- The concern is user-facing auth flow behavior (login, session, token issuance); use `.claude/skills/auth-flow-review`.
- The concern is cloud provider IAM debugging (AWS/GCP/Azure roles and policies not resolving as expected); use `.claude/skills/iam-permissions-debugging`.
- The concern is Terraform plan mechanics for IAM resources; use `.claude/skills/terraform-plan-review`.

**Rule of thumb**: use this skill for resource ownership, tenant boundaries, service-to-service permissions, admin/user boundaries, or privilege boundaries. If the review centers on login, session management, token issuance, authentication flow, password reset, or OAuth/OIDC behavior, use `.claude/skills/auth-flow-review` instead. If it centers on cloud IAM failure/debugging, use `.claude/skills/iam-permissions-debugging` instead.

## Composition and handoff rules

- Defer user-facing auth flow behavior to `.claude/skills/auth-flow-review`.
- Defer cloud IAM debugging to `.claude/skills/iam-permissions-debugging`.
- Defer Terraform plan mechanics to `.claude/skills/terraform-plan-review`.

## Evidence access rule

Inspect the actual permission model: role/permission definitions, authorization checks in code, resource ownership checks, and tenant-isolation enforcement. If the permission model, role definitions, or enforcement code is unavailable, request exact files/config and mark boundary conclusions pending.

## Workflow

1. Map the privilege model: roles, permissions, resource ownership, and service-to-service trust relationships as defined in code/config.
2. Identify boundaries that should exist: user vs admin, tenant vs tenant, service vs service, and check whether enforcement code actually implements them.
3. Look for confused-deputy risk (a trusted service acting on behalf of a caller without re-checking the caller's own permission) and ownership-boundary gaps (checking existence but not ownership of a resource).
4. Flag missing and overbroad permissions (wildcard scopes, default-allow, permissions granted but unused).
5. Produce a remediation plan (narrow scopes, add ownership checks, add tenant-isolation tests) with safe verification steps.
6. Before final handoff, load `references/checklist.md`; consult `references/anti-patterns.md` when tempted to recommend a broad permission fix or assume enforcement exists without seeing it in code.

## Questions to ask (only when necessary)

- Who owns approval for permission model changes in this system?

## Quality bar

- Every boundary finding is traced to actual authorization code, not assumed from naming conventions.
- Remediation narrows scope rather than broadening it "to unblock" something.
- No exploitation of a found gap is demonstrated.

## Expected output format

```markdown
## Access control boundary review: <scope>

**Privilege map**: <roles/permissions/ownership/trust relationships>
**Boundary findings**: <expected boundary - actual enforcement>
**Missing/overbroad permissions**: <details>
**Confused-deputy / ownership-boundary risks**: <details>
**Remediation plan**: <steps>
**Pending evidence**: <exact requests>
```

## Failure modes to avoid

- **Naming-convention trust**: assuming a function named `checkAccess` actually enforces the right boundary without reading it.
- **Broad permission fix**: widening a role to resolve an access error instead of correcting the boundary.
- **Existence-only check**: verifying a resource exists but not that the caller owns/may access it.

More in `references/anti-patterns.md`.
