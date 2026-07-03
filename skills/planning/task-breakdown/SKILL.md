---
name: task-breakdown
description: Decompose a feature request or engineering task into a sequenced, verifiable implementation plan grounded in the actual codebase. Use when a task will touch more than 2-3 files, spans multiple layers (API + DB + UI), is vaguely specified, or the user asks "how would you approach this". Complements (does not replace) built-in planning modes by adding investigation receipts: every step names files actually inspected, carries its own verification, and scope exclusions are explicit. Produces a durable plan (plan file, issue, or PR description), not a generic to-do list.
---

# Task Breakdown

## Goal

Turn a fuzzy task into an ordered plan of small, independently verifiable steps — where every step carries an **investigation receipt** (it names only files, contracts, commands, or docs you actually inspected) — so execution becomes mechanical and reviewable. This skill is the investigation-and-verification discipline that generic planning lacks; if a built-in plan mode is active, apply these rules inside it rather than planning twice.

## When to use

- The task touches multiple files, layers, or systems.
- The request is underspecified ("add rate limiting", "support teams").
- Before starting work that will take more than ~30 minutes of changes.
- The user explicitly asks for a plan or approach.

## When not to use

- One-file, obvious changes — just do them.
- Pure investigation tasks (use `diagnose-bug` or `repo-onboarding`).
- The user has already provided an explicit step list they want followed — don't re-plan a task the user already scoped.

## Workflow

1. **Restate the task as an outcome, then investigate.** One sentence: what will be observably true when this is done. Then locate every area the task touches — search for the relevant routes, models, jobs, configs, and open the key files. **A plan step may only name a file you (or a prior onboarding pass) actually looked at.** If the outcome can't be stated, the task is underspecified — resolve by investigation first, questions last.
2. **Find the existing pattern.** Most tasks are "another one of these". Find the closest existing feature (a similar endpoint, a similar pipeline) and plan to mirror its structure. Note it in the plan.
3. **Sequence contracts first.** Schema migrations, API shapes, event formats, public interfaces — anything other code depends on gets designed and ordered before the implementation steps that consume it.
4. **Slice into steps that each leave the system working — with verification built in.** Each step: a scoped change, the real files it touches, and how it's verified (a named test, command, or check). Tests belong inside behavior-changing steps, never as a final "add tests" step. Order: contracts → core logic → wiring → cleanup.
5. **Set the boundaries.** Flag risks (data migrations, backward compatibility, performance, auth) with a mitigation or an explicit acceptance. List adjacent work you are deliberately **not** doing, so scope creep is a visible decision.
6. **Deliver the plan durably.** Write it in the output format to wherever it will survive — the PR description, the issue, or `docs/plans/<task>.md` — not only chat. Before delivering, load `references/checklist.md` and verify the plan against it; consult `references/anti-patterns.md` if any step feels hand-wavy. Wait for confirmation if the plan involves irreversible or contract-breaking steps; otherwise proceed.

## Questions to ask (only when necessary)

- Which behavior is desired when the spec is genuinely ambiguous and both options are plausible and expensive to reverse (e.g., "should existing users be migrated or grandfathered?").
- Priority between conflicting constraints the code can't reveal (speed vs. backward compatibility).

Do not ask about anything discoverable from the codebase, and never ask more than 2–3 questions — bundle them.

## Quality bar

- Every step names concrete files/modules that were actually inspected — investigation receipts, not guesses.
- Every step has a stated verification method.
- Contract/schema changes appear before the code that depends on them.
- The closest existing pattern in the repo is identified and reused.
- Risks and out-of-scope items are explicit.
- The plan lands somewhere durable (PR description, issue, plan file), not only in chat.

## Expected output format

```markdown
## Plan: <outcome statement>

**Pattern to mirror**: <existing feature + files>

**Steps**
1. <change> — files: <paths, all inspected> — verify: <test/command/check>
2. ...

**Risks**
- <risk> → <mitigation or "accepted because...">

**Out of scope**
- <deliberately excluded work>

**Open questions** (only if blocking)
- <question>
```

## Failure modes to avoid

- **Planning from imagination**: steps naming files or functions that were never opened (or don't exist).
- **"Write tests" as step N**: tests belong inside the steps whose behavior they verify.
- **Plan-mode déjà vu**: running a full breakdown on a task the user already scoped, or duplicating an active plan mode instead of enriching it with receipts.

More in `references/anti-patterns.md`.
