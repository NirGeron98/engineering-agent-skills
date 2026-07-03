---
name: architecture-decision-record
description: Draft or update an Architecture Decision Record for a meaningful technical decision. Use when decision context, problem statement, constraints, options, chosen approach, consequences, tradeoffs, rejected alternatives, revisit triggers, owners, and evidence links must be captured in a durable ADR; use architecture-review first when the decision is not understood.
---

# Architecture Decision Record

## Goal

Create a concise ADR that records the decision, why it was made, which alternatives were rejected, what consequences follow, and when the decision should be revisited.

## When to use

- A technical decision has been made or is ready to document.
- The team needs durable context for future maintainers.
- A prior ADR needs updating with status, consequences, or new evidence.

## When not to use

- The architecture problem is still unclear; use `architecture-review`.
- The user needs implementation sequencing; use `skills/planning/task-breakdown`.
- The decision mainly concerns a refactor migration path; use `refactor-strategy`.

## Composition and handoff rules

- Use `architecture-review` first when options, constraints, or risks are not yet understood.
- Hand implementation planning to `skills/planning/task-breakdown`.
- Hand refactor migration planning to `refactor-strategy`.

## Evidence access rule

Do not invent the decision history. Inspect existing ADRs, design docs, issues, PRs, code, interfaces, and constraints; if history or constraints are missing, request exact links or mark them pending in the ADR.

## Workflow

1. Locate existing ADR style, numbering, status terms, and storage location.
2. Gather decision evidence: proposal, alternatives, constraints, affected systems, owners, implementation state, and links.
3. Write the problem statement and context in neutral language.
4. Compare considered options with tradeoffs, rejected alternatives, and consequences.
5. Record decision status, owners, revisit trigger, follow-up actions, and links to evidence.
6. Before final handoff, load `references/checklist.md`; consult `references/anti-patterns.md` when tempted to backfill certainty, hide tradeoffs, or write a ceremonial ADR.

## Questions to ask (only when necessary)

- What status should this ADR use if the repository has no ADR convention?
- Who owns revisiting this decision?

## Quality bar

- ADR claims link to real evidence or are labeled pending.
- Rejected alternatives include the reason they lost.
- Consequences include costs and risks, not just benefits.

## Expected output format

```markdown
## ADR draft/update: <decision>

**Status**: <proposed/accepted/superseded/etc.>
**Context**: <problem and constraints>
**Decision**: <chosen approach>
**Options considered**:
- <option> - tradeoffs: <summary>

**Consequences**: <positive, negative, operational, migration impacts>
**Rejected alternatives**: <why rejected>
**Revisit trigger**: <condition/date/metric>
**Owners and follow-up actions**: <owners/actions>
**Evidence links**: <docs/issues/PRs/files>
```

## Failure modes to avoid

- **ADR theater**: writing a polished record that hides missing evidence or unresolved tradeoffs.
- **Decision laundering**: making a preference look inevitable by omitting rejected options.
- **Eternal decision**: omitting revisit triggers for a context-sensitive choice.

More in `references/anti-patterns.md`.
