---
name: incident-triage-runbook
description: Coordinate active or recent production incident triage where the priority is impact assessment, stabilization, communication, timeline, hypotheses, mitigation, rollback, and a clean operational record. Use when error rate, latency, availability, data freshness, deploy, infrastructure, or customer-impact symptoms require an incident runbook; produces an incident triage runbook, timeline, hypothesis list, immediate mitigation plan, communication update, and post-incident follow-up items. Hands specific CI, deployment, service, IAM, Terraform, or Kubernetes root-cause work to the specialist skills after stabilization needs are clear.
---

# Incident Triage Runbook

## Goal

Stabilize and coordinate an incident with evidence, timeline, ownership, and communication before deep root-cause work.

## When to use

- Production or customer-impacting symptoms are active or recently active.
- The team needs severity, impact, timeline, mitigation, rollback, owner, and communication structure.
- Multiple systems or possible causes make specialist debugging premature.

## When not to use

- A narrow CI failure has no user impact; use `ci-cd-debugging`.
- A specific deployment/runtime issue is already scoped; use `deployment-debugging`.
- A local reproducible code defect is the task; use `diagnose-bug`.

## Composition and handoff rules

- Coordinate and stabilize first; hand root-cause threads to `service-debugging`, `deployment-debugging`, `ci-cd-debugging`, `terraform-plan-review`, `iam-permissions-debugging`, or `kubernetes-workload-debugging`.
- Do not replace specialist debugging once the boundary is narrowed.
- Keep communication neutral and evidence-based; do not write blameful language.

## Evidence access rule

If logs, metrics, traces, deploy history, alerts, or dashboards are unavailable, request exact queries/screenshots and mark conclusions pending. Do not invent telemetry, user impact, owners, or timelines.

## Workflow

1. Establish severity, current status, affected users/systems, business impact, and incident commander/owner if known.
2. Build a timeline from alerts, symptom onset, recent deploys/config/infrastructure changes, mitigations, and customer reports.
3. Capture evidence: metrics, logs, traces, deploy history, cloud/Kubernetes events, feature flag changes, and support signals.
4. List hypotheses and unknowns; rank by impact and testability, not confidence theater.
5. Choose immediate mitigation: rollback, disable flag, scale, traffic shift, queue pause, dependency fallback, or communication-only if no safe action exists.
6. Create communication update with impact, status, action, next update time, and unknowns.
7. Hand specialist root-cause tracks to the right skill after stabilization path is explicit.
8. Before final handoff, load `references/checklist.md`; consult `references/anti-patterns.md` when tempted to overfit root cause, mutate production, invent metrics, or blame people.

## Output format

```markdown
## Incident triage runbook: <incident>

**Severity/status**: <sev/current state>
**Impact**: <users/business/systems, confirmed vs pending>
**Timeline**
- <time> - <event/evidence>

**Hypotheses**
- <hypothesis> - evidence: <for/against> - next check: <query/owner>

**Immediate mitigation plan**: <action, safety check, rollback>
**Communication update**: <customer/internal update>
**Follow-up items**: <post-incident actions>
```

## Final handoff requirements

- Load `references/checklist.md` and verify the runbook against it.
- Consult `references/anti-patterns.md` when ambiguity, risk, or shortcut temptation appears.
- Mark any impact, telemetry, timeline, owner, mitigation, or root-cause conclusion that depends on missing evidence as pending.

## Failure modes to avoid

- **Root cause theater**: naming a cause before timeline and evidence exist.
- **Mitigation delay**: deep debugging while impact is ongoing and rollback is available.
- **Blameful record**: writing person-focused conclusions instead of system evidence.

More in `references/anti-patterns.md`.
