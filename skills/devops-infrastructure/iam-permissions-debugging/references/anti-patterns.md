# IAM Permissions Debugging - Anti-patterns

## Admin-first fix
**Symptom**: owner, editor, admin, wildcard, or cluster-admin is proposed first.
**Why it matters**: it hides the missing permission and expands breach impact.
**Correction**: identify principal/action/resource and grant the narrowest role or permission.

## Wrong-principal chase
**Symptom**: policies are checked for the developer while the runtime service identity is failing.
**Why it matters**: human and workload permissions usually differ.
**Correction**: prove the caller from logs, token claims, workload identity, or service configuration.

## Scope confusion
**Symptom**: permissions are added to a project/account when the failure is resource-, namespace-, or condition-scoped.
**Why it matters**: broad scope creates unnecessary access and may still not satisfy conditions.
**Correction**: bind at the narrowest resource/scope that covers the failing action.

## Token-scope blind spot
**Symptom**: roles look correct, but OAuth/session/token scopes are ignored.
**Why it matters**: a token can lack the allowed scope even when IAM policy is correct.
**Correction**: inspect token audience/scopes/session/federation and refresh path.

## RBAC and cloud IAM blur
**Symptom**: Kubernetes RBAC is fixed by changing cloud IAM, or vice versa.
**Why it matters**: separate authorization layers may both be involved.
**Correction**: identify which layer returned the denial and route workload symptoms to `kubernetes-workload-debugging`.
