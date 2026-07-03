---
name: kubernetes-workload-debugging
description: Debug Kubernetes workloads that are unhealthy, Pending, CrashLoopBackOff, not Ready, not receiving traffic, or behaving differently inside the cluster. Use when pods, deployments, replicasets, statefulsets, jobs, cronjobs, events, logs, describe output, image pulls, probes, resources, node scheduling, configmaps/secrets, service selectors, ingress/gateway, DNS, namespaces, or RBAC evidence is central; produces a Kubernetes workload diagnosis, evidence table, likely and ruled-out causes, minimal safe fix, verification commands, and rollback notes. Hands application logic bugs to service-debugging or diagnose-bug, general deployment issues to deployment-debugging, and permission-only root causes to iam-permissions-debugging.
---

# Kubernetes Workload Debugging

## Goal

Diagnose workload health and traffic problems from Kubernetes state, events, logs, and manifests using read-only commands first.

## When to use

- Pods are Pending, CrashLoopBackOff, ImagePullBackOff, not Ready, restarting, or missing.
- A Deployment, StatefulSet, Job, CronJob, Service, Ingress, or Gateway does not behave as expected.
- Cluster-internal config, scheduling, probes, resources, selectors, DNS, or RBAC may be involved.

## When not to use

- CI failed before manifests or images deployed; use `ci-cd-debugging`.
- Platform/deployment artifact/config is central but Kubernetes evidence is not; use `deployment-debugging`.
- A permission-only root cause is proven; use `iam-permissions-debugging`.
- Pod/runtime health is good and the defect is application logic; use `service-debugging` or `diagnose-bug`.

## Composition and handoff rules

- Use `deployment-debugging` for non-Kubernetes release/artifact/config boundaries.
- Use `iam-permissions-debugging` for RBAC/IAM fixes after the denied principal/action/resource is isolated.
- Use `service-debugging` only after Kubernetes readiness, routing, config, and dependency evidence are healthy.

## Evidence access rule

If you cannot run `kubectl` or inspect cluster evidence, emit exact read-only commands and mark workload conclusions pending. Do not recommend deleting pods, scaling production, editing live manifests, or changing probes without explaining impact and rollback.

## Workflow

1. Record cluster/context, namespace, workload, release/version, rollout time, and symptom.
2. Gather read-only state: `kubectl get`, `describe`, `logs`, previous logs, events, rollout history, and relevant manifests.
3. Inspect pod lifecycle: scheduling, image pull, init containers, crashes, exit codes, restarts, probes, resources, node selectors, taints/tolerations, and PVCs.
4. Inspect config and identity: ConfigMaps, Secrets references, env vars, service account, RBAC, and mounted volumes without exposing secret values.
5. Inspect traffic path: Service selectors/endpoints, ports, readiness, Ingress/Gateway rules, DNS/service discovery, and NetworkPolicy where relevant.
6. Classify likely cause and ruled-out causes with evidence.
7. Propose minimal safe fix, verification commands, and rollback to previous manifest/image/release.
8. Before final handoff, load `references/checklist.md`; consult `references/anti-patterns.md` when tempted to delete pods, change probes, scale, or blame application code without cluster evidence.

## Output format

```markdown
## Kubernetes workload diagnosis: <namespace/workload>

**Evidence table**
| Check | Observed | Meaning | Status |
|---|---|---|---|

**Likely cause**: <cause and evidence>
**Ruled out**: <causes ruled out with evidence>
**Minimal safe fix**: <manifest/config/image/RBAC/traffic fix>
**Verification commands**: <kubectl commands>
**Rollback notes**: <previous revision/image/manifest>
```

## Final handoff requirements

- Load `references/checklist.md` and verify the diagnosis against it.
- Consult `references/anti-patterns.md` when ambiguity, risk, or shortcut temptation appears.
- Mark any pod state, event, log, scheduling, routing, RBAC, or rollout conclusion that depends on missing evidence as pending.

## Failure modes to avoid

- **Deleting pods as diagnosis**: removing evidence and hoping the replacement works.
- **Probe blame shortcut**: loosening probes before checking startup, logs, and dependencies.
- **App blame before cluster proof**: debugging code while pods are unscheduled, unready, or misrouted.

More in `references/anti-patterns.md`.
