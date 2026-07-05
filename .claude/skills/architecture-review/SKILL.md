---
name: architecture-review
description: Review a system design, major technical change, subsystem structure, or architecture proposal using repo, documentation, diagram, and runtime evidence. Use when components, responsibilities, data flow, dependencies, scalability, operability, testability, high-level security/privacy, risks, alternatives, and tradeoffs need an architecture review report; defer code-level PR review, API contract review, Terraform review, release readiness, and data-pipeline design to their specialist skills.
---

# Architecture Review

## Goal

Produce an evidence-backed architecture review that explains whether a proposed or existing system structure is coherent, operable, testable, and fit for its constraints.

## When to use

- A system design, major technical change, subsystem split, or architecture proposal needs review.
- The task spans components, boundaries, data flow, runtime dependencies, failure modes, tradeoffs, and alternatives.
- The user needs durable risks and open questions, not implementation nitpicks.

## When not to use

- A PR or diff needs implementation-level review; use `.claude/skills/code-review`.
- API endpoint shape or compatibility is the focus; use `.claude/skills/api-design-review`.
- Terraform, release, deployment, or data-pipeline mechanics are the focus; use the relevant specialist skill.

## Composition and handoff rules

- Hand implementation-level PR findings to `.claude/skills/code-review`.
- Hand API shape details to `.claude/skills/api-design-review`.
- Hand infrastructure rollout risk to `.claude/skills/release-and-rollback-readiness`.
- Hand Terraform-specific risk to `.claude/skills/terraform-plan-review`.
- Hand data pipeline design to `.claude/skills/db-to-dwh-pipeline`.

## Evidence access rule

Inspect actual docs, diagrams, repo structure, interfaces, dependency edges, data schemas, deployment/runtime notes, or observable behavior before making architectural claims. If evidence is unavailable, request exact artifacts or commands and mark dependent conclusions pending.

## Workflow

1. Capture the proposal, decision context, constraints, owners, target users, and success criteria.
2. Inspect evidence: design docs, diagrams, entry points, module/service boundaries, public interfaces, schemas/events, deployment notes, tests, and representative callers.
3. Map components, responsibilities, data flow, control flow, runtime dependencies, ownership, and failure domains.
4. Review qualities: scalability, operability, testability, deployability, compatibility, security/privacy implications, and data consistency.
5. Compare alternatives and tradeoffs against constraints; separate confirmed facts from assumptions and open questions.
6. Produce strengths, risks, required changes, and a recommendation with confidence level and pending evidence.
7. Before final handoff, load `references/checklist.md`; consult `references/anti-patterns.md` when tempted to trust diagrams, propose rewrites, or infer structure without repo evidence.

## Questions to ask (only when necessary)

- What constraints, non-goals, or decision deadline are not documented?
- Which production or user impact matters most if the design fails?

## Quality bar

- Every architectural claim cites a document, file, diagram, interface, command output, or explicitly pending evidence request.
- Tradeoffs are tied to constraints, not preference.
- Handoffs to specialist skills are named instead of absorbed.

## Expected output format

```markdown
## Architecture review: <system/change>

**Evidence reviewed**: <docs/files/diagrams/runtime evidence>
**Context and constraints**: <summary>
**Component map**: <components, responsibilities, data flow, dependencies>

**Strengths**
- <strength> - evidence: <source>

**Risks and required changes**
- <severity> - <risk> - evidence: <source> - recommendation: <change>

**Open questions / pending evidence**: <items>
**Tradeoff summary**: <chosen approach vs alternatives>
**Handoffs**: <specialist skills if needed>
```

## Failure modes to avoid

- **Architecture astronomy**: discussing ideal structures without evidence from the actual system.
- **Diagram-driven certainty**: treating a diagram as truth when code, dependencies, or runtime behavior have not been checked.
- **Rewrite as strategy**: recommending a rewrite before proving incremental options are insufficient.

More in `references/anti-patterns.md`.
