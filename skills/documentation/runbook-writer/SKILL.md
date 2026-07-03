---
name: runbook-writer
description: Create or review an operational runbook for incidents, alerts, deployments, jobs, or recurring procedures. Use when trigger, scope, impact, prerequisites, read-only evidence commands, dashboards/logs, mitigation, rollback, escalation, verification, and known failure modes need a safe step-by-step runbook.
---

# Runbook Writer

## Goal

Produce an operational runbook that a responder can execute safely, starting with evidence and verification before mutation, with clear escalation and rollback paths.

## When to use

- A recurring alert, incident class, deployment, job, or procedure needs a runbook.
- An existing runbook may be unsafe, stale, incomplete, or unactionable.
- The user needs exact commands, checks, escalation, and verification steps documented.

## When not to use

- An active incident needs coordination now; use `skills/devops-infrastructure/incident-triage-runbook`.
- Release readiness or rollback safety is the main question; use `skills/devops-infrastructure/release-and-rollback-readiness`.
- Kubernetes workload diagnosis is the main task; use `skills/devops-infrastructure/kubernetes-workload-debugging`.

## Composition and handoff rules

- Hand active incident coordination to `skills/devops-infrastructure/incident-triage-runbook`.
- Hand deployment readiness to `skills/devops-infrastructure/release-and-rollback-readiness`.
- Hand Kubernetes diagnosis to `skills/devops-infrastructure/kubernetes-workload-debugging`.

## Evidence access rule

Inspect existing runbooks, alerts, dashboards, logs, commands, service ownership, deployment docs, rollback procedures, and postmortems before drafting. If access is unavailable, request exact alert text, dashboard links, command outputs, service names, and escalation contacts; mark unverified steps pending.

## Workflow

1. Define trigger, audience, scope, severity/impact, prerequisites, permissions, and stop conditions.
2. Inspect existing procedures, alert definitions, dashboards/logs, service docs, ownership, and known incident history.
3. Draft read-only diagnosis steps first, with exact commands/queries and expected healthy/unhealthy signals.
4. Add mitigation, rollback, or mutation steps only after safety checks, blast radius, approval, and verification are clear.
5. Add escalation, communication, validation, cleanup, and known failure modes.
6. Before final handoff, load `references/checklist.md`; consult `references/anti-patterns.md` when tempted to start with mutation, omit expected outputs, or assume access.

## Questions to ask (only when necessary)

- Who owns escalation if ownership cannot be found in docs or code?
- What production-change approval policy applies to mutation steps?

## Quality bar

- Every command has purpose, expected output, and safety notes.
- Read-only evidence comes before mutation.
- Escalation, rollback/mitigation, and validation are explicit.

## Expected output format

```markdown
## Runbook: <trigger/procedure>

**Scope and impact**: <what this covers>
**Prerequisites and access**: <tools/permissions>
**Read-only diagnosis**: <step - command/query - expected evidence>
**Mitigation or rollback**: <guarded steps and approvals>
**Validation**: <how to prove recovery>
**Escalation and cleanup**: <owners/follow-up>
**Pending evidence**: <unverified commands/links/owners>
```

## Failure modes to avoid

- **Mutation-first runbook**: restarting, deleting, applying, or purging before evidence.
- **Command without expectation**: listing commands but not healthy/unhealthy interpretation.
- **No escalation path**: leaving responders stuck when the procedure fails.

More in `references/anti-patterns.md`.
