---
name: security-incident-scoping
description: Scope a suspected security incident such as a compromise, credential exposure, suspicious access, or malicious activity. Use when an incident scope summary, known-vs-unknown facts, evidence preservation checklist, containment options, affected assets/users/systems, escalation path, and follow-up investigation plan are needed. Defensive only, no offensive response actions.
---

# Security Incident Scoping

## Goal

Turn a suspected security incident into an evidence-preserving scope summary and safe containment/escalation plan, without offensive countermeasures.

## When to use

- There is suspected compromise, credential exposure with possible use, suspicious access, or malicious activity.
- The user needs to know what is known vs unknown, what to preserve as evidence, and how to safely contain and escalate.
- Initial incident scoping is needed before or alongside deeper investigation.

## When not to use

- The incident is not security-specific (outage, deploy failure); use `skills/devops-infrastructure/incident-triage-runbook`.
- The concern is a secret found but with no evidence of use; use `skills/security-review/secrets-exposure-review`.
- A durable runbook needs to be authored from this scoping; use `skills/documentation/runbook-writer`.

## Composition and handoff rules

- Defer general incident coordination to `skills/devops-infrastructure/incident-triage-runbook` when it is not security-specific.
- Defer secrets-specific containment to `skills/security-review/secrets-exposure-review`.
- Defer runbook drafting to `skills/documentation/runbook-writer`.

## Evidence access rule

Inspect actual access logs, auth logs, timestamps, IPs/locations, affected resource lists, and alert/detection evidence before asserting compromise. If logs, timelines, or affected-asset lists are unavailable, request exact log sources/queries and mark scope and impact conclusions pending. Preserve evidence before taking containment actions that could destroy it.

## Workflow

1. Record what triggered the suspicion (alert, anomaly, report) with exact timestamps and source.
2. Separate known facts (confirmed from logs/evidence) from unknown/suspected facts; do not treat suspicion as confirmed compromise.
3. Produce an evidence preservation checklist (snapshot logs, do not overwrite sessions/tokens before capturing state, preserve access records) before any destructive containment step.
4. Identify likely affected assets, users, systems, or data based on evidence, marking anything unconfirmed as pending.
5. Propose containment options (e.g., revoke/rotate credential, disable account, isolate resource) each with safety framing, required approval/ownership, and rollback/verification — not executed automatically.
6. Define escalation path and a follow-up investigation plan (what evidence to gather next, who investigates).
7. Before final handoff, load `references/checklist.md`; consult `references/anti-patterns.md` when tempted to treat suspicion as confirmed or contain before preserving evidence.

## Questions to ask (only when necessary)

- Who is the designated incident commander or security escalation contact for this system?

## Quality bar

- Known and unknown facts are clearly separated.
- Evidence preservation precedes any destructive containment recommendation.
- No offensive response, attacker profiling beyond available evidence, or unauthorized-access instructions are included.

## Expected output format

```markdown
## Security incident scoping: <trigger>

**Incident scope summary**: <what is suspected>
**Known facts**: <evidence-backed>
**Unknown/suspected facts**: <pending confirmation>
**Evidence preservation checklist**: <steps, before containment>
**Affected assets/users/systems**: <confirmed vs suspected>
**Containment options**: <steps with approval/rollback>
**Escalation path**: <who/when>
**Follow-up investigation plan**: <next evidence to gather>
**Pending evidence**: <exact requests>
```

## Failure modes to avoid

- **Suspicion treated as fact**: describing an unconfirmed anomaly as a confirmed compromise.
- **Containment before preservation**: revoking access or restarting systems before capturing evidence.
- **Unowned containment**: proposing containment steps with no approval path or rollback plan.

More in `references/anti-patterns.md`.
