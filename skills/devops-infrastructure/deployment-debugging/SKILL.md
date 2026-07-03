---
name: deployment-debugging
description: Debug deployments that report success but produce broken runtime behavior in services, apps, jobs, workers, or endpoints. Use for Cloud Run, Kubernetes, ECS, App Service, VMs, or similar when artifact version, runtime config, env vars, ports, health checks, service identity, networking, ingress, logs, or rollback candidates must be verified; produces a deployment diagnosis, runtime evidence checklist, boundary classification, mitigation/rollback recommendation, and verification steps. Hands application defects to service-debugging or diagnose-bug, IAM failures to iam-permissions-debugging, and Kubernetes pod/workload symptoms to kubernetes-workload-debugging.
---

# Deployment Debugging

## Goal

Determine why a deployed runtime is broken after an apparently successful deployment, using artifact, config, health, routing, and log evidence before changing live systems.

## When to use

- A deployment succeeds but endpoint, worker, job, or service behavior is unhealthy.
- Staging/production differs from local or another environment.
- Runtime configuration, image/artifact, health checks, ingress, egress, or rollback state is suspected.

## When not to use

- CI/CD failed before a deploy artifact was produced; use `ci-cd-debugging`.
- Kubernetes pod events, scheduling, probes, or service selectors are central; use `kubernetes-workload-debugging`.
- Permission denied, 403, or access scope is the main symptom; use `iam-permissions-debugging`.
- Runtime evidence proves a service logic defect; use `service-debugging` or `diagnose-bug`.

## Composition and handoff rules

- Hand off to `service-debugging` after artifact, config, identity, network, health, and routing evidence show deployment is correct.
- Hand off to `kubernetes-workload-debugging` when pod/workload state is the evidence source.
- Hand off to `iam-permissions-debugging` for principal/action/resource permission fixes.

## Evidence access rule

When you cannot inspect cloud console, deployment history, logs, health checks, runtime config, or network evidence, emit exact read-only commands or screenshots to request and mark conclusions pending. Never infer deployed cloud state from repository code alone.

## Workflow

1. Record service, environment, platform, deploy time, version/image/digest/artifact, and symptom.
2. Verify deployment status and rollback candidate from deploy history or release metadata.
3. Capture runtime evidence: logs, health checks, startup errors, readiness, ports, env vars/config references, service identity, and resource limits.
4. Check ingress/routing, DNS, load balancer, service URL, TLS, path rules, and egress/dependency connectivity where relevant.
5. Compare config drift against a healthy environment or previous version.
6. Classify the boundary: wrong artifact, bad runtime config, health check, identity/IAM, network/routing, platform capacity, application behavior, or backend dependency.
7. Prefer mitigation/rollback before invasive production changes; document blast radius and verification.
8. Before final handoff, load `references/checklist.md`; consult `references/anti-patterns.md` when tempted to restart, redeploy, blame code, or mutate production without evidence.

## Output format

```markdown
## Deployment diagnosis: <service/env>

**Symptom**: <what fails and where>
**Runtime evidence**: <deploy id/version/logs/health/config/routing, with pending items>
**Boundary classification**: <artifact/config/health/identity/network/routing/platform/app/dependency>

**Findings**
- <severity> - <issue> - evidence: <deployment/log/config> - action: <mitigation/fix/handoff>

**Mitigation or rollback**: <recommendation and safety check>
**Verification**: <exact commands/checks>
```

## Final handoff requirements

- Load `references/checklist.md` and verify the diagnosis against it.
- Consult `references/anti-patterns.md` when ambiguity, risk, or shortcut temptation appears.
- Mark any cloud state, log, health check, runtime config, routing, or rollback conclusion that depends on missing evidence as pending.

## Failure modes to avoid

- **Code blame for config drift**: patching source before proving the deployed artifact and runtime config.
- **Production mutation first**: restarting or redeploying before read-only evidence.
- **Rollback-free fix**: changing live deployment state without a known rollback candidate.

More in `references/anti-patterns.md`.
