---
name: handoff-documentation
description: Summarize completed or paused work so another person or agent can continue. Use after an investigation, bug fix, PR, incident, or feature slice when context, evidence, decisions, files touched, tests run, pending work, risks, unresolved questions, and next steps need a concise handoff note.
---

# Handoff Documentation

## Goal

Create a continuation-ready handoff that preserves context, evidence, decisions, verification, risks, and next steps without replacing the domain-specific work that produced them.

## When to use

- Work is paused and another person or agent needs to resume safely.
- An investigation, bug fix, PR, incident, or feature slice needs a concise summary.
- The user needs unresolved questions, risks, and verification clearly separated.

## When not to use

- The root diagnosis or review has not been performed; use the relevant domain skill first.
- The target is release notes or changelog; use `skills/documentation/changelog-writer`.
- The target is onboarding documentation; use `skills/documentation/developer-onboarding-docs`.

## Composition and handoff rules

- Use after another skill has produced evidence, changes, or decisions.
- Hand release-facing summaries to `skills/documentation/changelog-writer`.
- Do not invent findings; preserve evidence and pending work from the preceding workflow.
- This handoff is for the next worker or agent, not release consumers: it is internal, technical, and resumable, and it should keep failed attempts, pending work, risks, and next steps that a changelog would omit.

## Evidence access rule

Inspect the actual diff, notes, commands, logs, test output, issue/PR context, and files touched before writing the handoff. If evidence is missing, request exact artifacts or mark sections as pending rather than filling gaps from memory.

## Workflow

1. Identify the handoff audience, current status, objective, and resume point.
2. Inspect available evidence: git diff/status, commits, tests, logs, docs, decisions, open issues, and prior outputs.
3. Summarize what changed or was learned, separating facts, decisions, assumptions, and pending questions.
4. Record verification performed, verification not performed, risks, blockers, and the next concrete steps.
5. Keep the handoff concise and continuation-oriented.
6. Before final handoff, load `references/checklist.md`; consult `references/anti-patterns.md` when tempted to overstate completion, omit failed checks, or hide unknowns.

## Questions to ask (only when necessary)

- Who will consume the handoff if that changes the detail level or format?

## Quality bar

- The handoff distinguishes done, pending, blocked, and risky work.
- Verification includes commands/results or exact missing evidence.
- Another person can resume without re-deriving context.

## Expected output format

```markdown
## Handoff: <work item>

**Status**: <done/in progress/blocked>
**Context**: <why this work exists>
**Evidence and decisions**: <facts/decisions with sources>
**Files or areas touched**: <paths/areas>
**Verification**: <commands/results/not run>
**Risks and pending questions**: <items>
**Next steps**: <ordered continuation checklist>
```

## Failure modes to avoid

- **Completion theater**: implying work is done when verification is pending.
- **Context dump**: pasting raw notes without resume-ready ordering.
- **Lost failures**: omitting failed commands, rejected hypotheses, or known risks.

More in `references/anti-patterns.md`.
