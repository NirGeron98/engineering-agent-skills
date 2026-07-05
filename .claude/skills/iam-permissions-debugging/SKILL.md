---
name: iam-permissions-debugging
description: Debug access denied, forbidden, unauthorized, permission denied, missing role, token scope, service account, impersonation, Kubernetes RBAC, or cross-project/account permission failures. Use when a principal cannot perform an action on a resource across GCP, AWS, Azure, Kubernetes, CI tokens, or service identities; produces a permission failure diagnosis, principal/action/resource matrix, least-privilege fix, verification command, and risk notes. Hands CI context problems to ci-cd-debugging, runtime deployment identity issues to deployment-debugging after root cause, and Kubernetes workload/RBAC symptoms to kubernetes-workload-debugging when pod state is central.
---

# IAM Permissions Debugging

## Goal

Resolve permission failures by proving the principal, action, resource, and policy boundary, then recommending the narrowest safe permission change.

## When to use

- Errors mention access denied, forbidden, unauthorized, permission denied, missing role, missing scope, impersonation, or RBAC.
- A human, CI token, service account, managed identity, pod, or workload identity cannot access a cloud or cluster resource.
- Cross-project/account/subscription/namespace access is suspected.

## When not to use

- The pipeline context is the uncertain part; use `ci-cd-debugging` first.
- The deployed service is unhealthy for broader runtime reasons; use `deployment-debugging`.
- Kubernetes pod scheduling/runtime evidence is central; use `kubernetes-workload-debugging`.

## Composition and handoff rules

- Send CI runner/token context questions to `ci-cd-debugging` when the failing identity is not proven.
- Send post-fix runtime validation to `deployment-debugging` for service identity issues.
- Send pod/RBAC plus CrashLoop/Pending/traffic symptoms to `kubernetes-workload-debugging`.

## Evidence access rule

If principal, action, resource, error message, token scope, or policy binding is unknown, request exact commands or policy snippets and mark conclusions pending. Never suggest broad admin/editor/owner permissions as the first fix.

## Workflow

1. Capture exact error code/message, operation, timestamp, environment, and caller context.
2. Identify the effective principal: human, CI token, service account, workload identity, pod service account, assumed role, managed identity, or impersonated identity.
3. Build a principal/action/resource matrix with requested API method/verb, resource name/scope, and policy boundary.
4. Inspect relevant policies/bindings/roles/scopes/inheritance, plus org/project/account/subscription/namespace boundaries.
5. Check token/session details: impersonation, audience, OAuth scopes, expiration, federation, trust policy, and conditional bindings.
6. Recommend the least-privilege fix: existing narrow role, custom permission set, binding scope, condition, or identity correction.
7. Provide read-only verification command and rollback/risk notes for the permission change.
8. Before final handoff, load `references/checklist.md`; consult `references/anti-patterns.md` when tempted to grant admin, skip principal proof, or ignore inherited/conditional policy.

## Output format

```markdown
## Permission failure diagnosis: <operation>

**Error evidence**: <code/message/source>
**Matrix**
| Principal | Action | Resource | Current scope | Needed scope | Status |
|---|---|---|---|---|---|

**Least-privilege fix**: <role/permission/binding/identity correction>
**Verification**: <exact command or pending check>
**Risk notes**: <blast radius, conditions, rollback>
```

## Final handoff requirements

- Load `references/checklist.md` and verify the diagnosis against it.
- Consult `references/anti-patterns.md` when ambiguity, risk, or shortcut temptation appears.
- Mark any principal, action, resource, token, policy, or least-privilege conclusion that depends on missing evidence as pending.

## Failure modes to avoid

- **Admin-first fix**: granting owner/editor/admin to make the error disappear.
- **Wrong-principal chase**: debugging a human identity when the runtime service account is calling.
- **Scope confusion**: adding permission at project/account scope when the failing resource or condition is narrower.

More in `references/anti-patterns.md`.
