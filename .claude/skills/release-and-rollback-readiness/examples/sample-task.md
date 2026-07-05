# Sample task - Release and Rollback Readiness

## Prompt

"Review whether tonight's production deployment is safe to proceed, including rollback and monitoring."

## Abbreviated run

1. Requested release scope, deploy plan, CI status, artifacts, migrations, flags, and infra changes.
2. Asked for rollback command/runbook and previous healthy version.
3. Checked monitoring and post-release verification coverage.
4. Produced a go/no-go decision with pending blockers.
5. Loaded `references/checklist.md` before handoff.

## Obstacle evidence request

```text
Please provide:
- release diff/notes and changed services/resources
- deploy sequence and artifact versions
- migration/feature flag/config changes
- rollback steps and previous healthy version
- smoke tests, dashboards, alerts, and observation window
```

## Expected output

```markdown
## Release readiness report: tonight's production deployment

**Decision**: go with conditions, pending release artifacts and rollback evidence.

**Required pre-release checks**
- Confirm previous healthy version and rollback command - status: pending - owner: release owner
- Confirm smoke tests and dashboards - status: pending - owner: release owner

**Rollback plan**: pending; release is not unconditionally ready without this.
```

## Why this passes the quality bar

It refuses unconditional approval without rollback and monitoring evidence, and routes infra/Kubernetes/API risk to specialist skills.
