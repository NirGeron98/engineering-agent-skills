---
name: backup-restore-readiness
description: Review database backup coverage, restore procedures, point-in-time recovery, retention, and recovery testing history for disaster-recovery readiness. Use when the question is whether backups and restore actually work, not incident-in-progress recovery. Produces a backup/restore readiness review with gaps, a risk rating, and a restore test plan.
---

# Backup Restore Readiness

## Goal

Turn assumed backup coverage into an evidence-backed readiness assessment of whether a real restore would actually succeed and meet recovery objectives.

## When to use

- Backup schedule, retention, or restore procedure needs a readiness review before being trusted.
- RPO/RTO targets need to be checked against actual backup/restore evidence.
- Restore testing history is unclear or has never been verified.

## When not to use

- An incident is actively in progress and recovery must be coordinated now; use `.claude/skills/incident-triage-runbook`.
- The task is writing or updating the runbook document itself; use `.claude/skills/runbook-writer`.
- The question is cloud infrastructure setup mechanics, not backup/restore readiness; use the relevant DevOps skill.

## Composition and handoff rules

- Defer operational procedure writing to `.claude/skills/runbook-writer`.
- Defer cloud infrastructure implementation to existing DevOps skills.
- Defer incident-specific recovery coordination to `.claude/skills/incident-triage-runbook`.

## Evidence access rule

Inspect the actual backup schedule/configuration, retention settings, restore procedure documentation, and restore test history/logs. If backup configuration, retention settings, or restore test history are unavailable, request exact configuration exports or logs and mark readiness and risk conclusions pending.

## Workflow

1. Confirm actual backup schedule, retention period, and encryption/access controls in place.
2. Check whether point-in-time recovery is supported and configured if required by RPO.
3. Review the documented restore procedure and confirm it matches actual backup format/location.
4. Check restore test history: has a real restore ever been performed and verified, and when.
5. Compare actual RPO/RTO capability against stated targets, and check cross-region/cross-account storage if relevant.
6. Produce gaps, a risk rating, a restore test plan, and named ownership/follow-up actions.
7. Before final handoff, load `references/checklist.md`; consult `references/anti-patterns.md` when tempted to treat an untested backup as restore-ready.

## Questions to ask (only when necessary)

- What are the stated RPO/RTO targets for this database?
- When was the last successful, verified restore test performed?

## Quality bar

- Restore readiness is based on actual test history, not backup existence alone.
- RPO/RTO capability is checked against real configuration, not assumed from the backup schedule's label.
- Gaps and risk rating are evidence-based, with ownership named for follow-up.

## Expected output format

```markdown
## Backup/restore readiness review: <scope>

**Gaps**: <findings>
**Risk rating**: <level and reasoning>
**Restore test plan**: <steps>
**Ownership and follow-up actions**: <owners, deadlines>
**Pending evidence**: <exact requests>
```

## Failure modes to avoid

- **Untested backup as ready**: treating a backup schedule as sufficient without a verified restore test.
- **RPO/RTO by assumption**: assuming recovery targets are met without checking actual restore evidence.
- **Restore procedure drift**: trusting a documented restore procedure that hasn't been validated against current backup format/location.

More in `references/anti-patterns.md`.
