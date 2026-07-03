# Sample task - Incident Triage Runbook

## Prompt

"Production error rate spiked after a release. We need a triage runbook and mitigation plan."

## Abbreviated run

1. Established severity, affected service, release time, and current impact as pending.
2. Requested metrics, logs, traces, deploy history, and rollback candidate.
3. Built a timeline and hypothesis list without declaring root cause.
4. Proposed rollback evaluation before invasive debugging.
5. Loaded `references/checklist.md` before handoff.

## Obstacle evidence request

```text
Please provide:
- error-rate dashboard screenshot or query for the last 2 hours
- deploy history with timestamps and versions
- top error logs/traces around onset
- rollback command or previous healthy version
- current customer/support impact notes
```

## Expected output

```markdown
## Incident triage runbook: production error spike

**Severity/status**: pending owner confirmation.
**Impact**: pending metrics/customer evidence.

**Timeline**
- pending - release deployed
- pending - error rate spike detected

**Immediate mitigation plan**: evaluate rollback to previous healthy version before deep root-cause work.
```

## Why this passes the quality bar

It prioritizes stabilization, requests exact missing evidence, avoids invented metrics, and keeps root-cause hypotheses separate from facts.
