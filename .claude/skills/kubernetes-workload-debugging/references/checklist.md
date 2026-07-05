# Kubernetes Workload Debugging - Checklist

## Evidence
- [ ] Cluster/context and namespace recorded
- [ ] Workload kind/name and release/version recorded
- [ ] `kubectl get` state captured or requested
- [ ] `kubectl describe` output captured or requested
- [ ] Events captured or requested
- [ ] Current and previous logs captured where relevant
- [ ] Rollout history or manifest version checked

## Pod lifecycle
- [ ] Pending scheduling constraints checked
- [ ] Image pull/auth/tag/digest errors checked
- [ ] Init container status checked
- [ ] Exit code/restart count/CrashLoopBackOff checked
- [ ] Readiness/liveness/startup probes checked
- [ ] Requests/limits/OOMKilled/resource pressure checked
- [ ] Node selectors, affinity, taints/tolerations, PVCs checked where relevant

## Config and traffic
- [ ] ConfigMap/Secret references checked without exposing values
- [ ] Service account/RBAC checked where relevant
- [ ] Service selector and endpoints checked
- [ ] Container ports/service ports/targetPorts checked
- [ ] Ingress/Gateway rules checked where relevant
- [ ] DNS/service discovery and NetworkPolicy checked where relevant

## Safety and handoff
- [ ] Read-only evidence collected before mutations
- [ ] No pod deletion, scale, probe edit, or live manifest edit without impact and rollback
- [ ] Likely and ruled-out causes are evidence-backed
- [ ] Verification commands and rollback notes included
