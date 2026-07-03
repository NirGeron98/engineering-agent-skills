---
name: technical-debt-triage
description: Prioritize a backlog of technical debt using evidence of impact, frequency, cost, risk, blast radius, cost of delay, and dependency on other work. Use when a team needs a ranked debt list, recommended next actions, defer/ignore decisions with reasons, and suggested tracking issues; defer actual refactor planning to refactor-strategy.
---

# Technical Debt Triage

## Goal

Turn a noisy debt list into a prioritized, evidence-backed set of actions that separates urgent debt, strategic fixes, quick wins, and items to defer or ignore.

## When to use

- A codebase, subsystem, or team has many technical debt complaints.
- The user needs prioritization, not immediate implementation.
- Debt impact must be tied to delivery, reliability, cost, risk, or user/business outcomes.

## When not to use

- A specific refactor needs a migration plan; use `refactor-strategy`.
- A task needs implementation sequencing; use `skills/planning/task-breakdown`.
- The system structure itself needs architecture review; use `architecture-review`.

## Composition and handoff rules

- Hand actual refactor planning to `refactor-strategy`.
- Hand issue breakdown to `skills/planning/task-breakdown`.
- Hand architecture-wide structural review to `architecture-review`.

## Evidence access rule

Do not rank debt by annoyance alone. Inspect issues, incident history, PR churn, test failures, ownership notes, code hotspots, onboarding friction, performance/cost evidence, and user/business impact; if missing, request exact evidence and mark scores pending.

## Workflow

1. Build a debt inventory from user list, issues, TODOs, incidents, flaky tests, code hotspots, and recent PR/review pain.
2. For each item, collect evidence of impact: frequency, user/business impact, engineering cost, risk, blast radius, cost of delay, and dependencies.
3. Score and group items: fix now, schedule, opportunistic quick win, monitor, defer, or ignore.
4. Identify items that must not be fixed yet because prerequisites, ownership, or evidence are missing.
5. Produce recommended next actions and suggested tracking issues with acceptance criteria.
6. Before final handoff, load `references/checklist.md`; consult `references/anti-patterns.md` when tempted to fix everything, prioritize by taste, or treat a debt list as a plan.

## Questions to ask (only when necessary)

- Which business or engineering objective should break ties between similarly scored items?
- Who owns the subsystem if ownership is undocumented?

## Quality bar

- Every priority has impact evidence or is marked pending.
- The defer/ignore list is explicit and justified.
- Next actions are small enough to track and verify.

## Expected output format

```markdown
## Technical debt triage: <scope>

**Evidence reviewed**: <issues/files/incidents/churn/tests>
**Scoring factors**: <impact/frequency/risk/cost/blast radius>

**Prioritized debt**
- P<level> - <item> - evidence: <source> - reason: <why now> - next action: <issue/action>

**Defer or ignore**
- <item> - reason: <why not now>

**Tracking issues**: <suggested issues>
**Pending evidence**: <items>
```

## Failure modes to avoid

- **Debt list without prioritization**: restating complaints without ranking or action.
- **Fixing everything at once**: treating all debt as equally urgent.
- **Loudest pain wins**: ranking by frustration instead of impact evidence.

More in `references/anti-patterns.md`.
