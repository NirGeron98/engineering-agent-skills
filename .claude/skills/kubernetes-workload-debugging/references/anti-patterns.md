# Kubernetes Workload Debugging - Anti-patterns

## Deleting pods as diagnosis
**Symptom**: `kubectl delete pod` is suggested before reading events/logs.
**Why it matters**: it erases useful evidence and can create customer impact.
**Correction**: collect describe, logs, previous logs, events, and rollout state first.

## Probe blame shortcut
**Symptom**: readiness/liveness probes are loosened because pods restart or stay unready.
**Why it matters**: probes often reveal real startup, dependency, or config failures.
**Correction**: inspect logs, startup time, dependency readiness, ports, and probe endpoint behavior before changing probes.

## Selector mismatch miss
**Symptom**: ingress or app code is blamed while the Service has no matching endpoints.
**Why it matters**: traffic cannot reach pods when selectors, labels, or ports drift.
**Correction**: compare deployment pod labels, service selectors, endpoints, and targetPorts.

## RBAC layer blur
**Symptom**: pod RBAC, cloud IAM, and application auth are treated as one issue.
**Why it matters**: the failing authorization layer determines the fix.
**Correction**: identify which layer denied which principal/action/resource, then route permission-only work to `iam-permissions-debugging`.

## Live edit reflex
**Symptom**: `kubectl edit`, scaling, or patching production manifests is proposed as the first step.
**Why it matters**: manual live changes drift from GitOps and can be hard to roll back.
**Correction**: prefer read-only evidence, then patch through the project convention with rollback.
