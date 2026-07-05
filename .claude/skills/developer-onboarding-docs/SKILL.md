---
name: developer-onboarding-docs
description: Create or review documentation that helps new developers understand and contribute to a repository or system. Use when repo map, local setup, architecture overview, workflows, coding conventions, test commands, deployment basics, troubleshooting, ownership, and contribution path need an onboarding doc outline or draft.
---

# Developer Onboarding Docs

## Goal

Produce onboarding documentation that helps a new developer make a verified first contribution without overwhelming them or inventing repo conventions.

## When to use

- A repository or system needs first-day or first-week developer onboarding docs.
- Existing setup, architecture, workflow, or contribution docs are scattered or stale.
- The user needs a repo map, setup verification checklist, and contribution path.

## When not to use

- The repo has not been explored yet; use `.claude/skills/repo-onboarding`.
- The README alone is the target; use `.claude/skills/readme-review`.
- Architecture design needs review; use `.claude/skills/architecture-review`.

## Composition and handoff rules

- Hand repo understanding to `.claude/skills/repo-onboarding`.
- Hand README-specific review to `.claude/skills/readme-review`.
- Hand architecture-specific review to `.claude/skills/architecture-review`.
- `.claude/skills/repo-onboarding` produces a session/agent-facing technical repo map or orientation artifact focused on how the repo works; this skill produces a human-facing onboarding path for future developers, including setup, first contribution path, ownership, workflows, and contribution guidance.

## Evidence access rule

Inspect the repo, README, docs, manifests, scripts, tests, CI, architecture notes, ownership files, deployment docs, and contribution precedents before drafting. If commands cannot be run, request exact outputs and mark setup verification pending.

## Workflow

1. Identify the onboarding audience, expected first contribution, prerequisites, and scope.
2. Inspect existing docs, repo structure, scripts, CI, tests, setup commands, architecture notes, deployment basics, ownership, and contribution history.
3. Build a concise repo map and first-day path with verified setup/test commands or pending verification requests.
4. Document common workflows, coding conventions, troubleshooting, ownership/escalation, and safe contribution steps.
5. Keep advanced domain detail linked, not duplicated.
6. Before final handoff, load `references/checklist.md`; consult `references/anti-patterns.md` when tempted to copy stale docs, over-document everything, or omit verification.

## Questions to ask (only when necessary)

- What first contribution should the onboarding path optimize for if the repo does not suggest one?

## Quality bar

- Setup and test commands are verified or marked pending with exact requests.
- The doc has a clear first-day path and does not require reading everything.
- Ownership, troubleshooting, and contribution steps are discoverable.

## Expected output format

```markdown
## Developer onboarding docs: <repo/system>

**Evidence reviewed**: <docs/files/commands>
**Audience and first contribution**: <scope>
**Repo map**: <areas and purpose>
**First-day path**: <steps with commands/checks>
**Common workflows**: <develop/test/review/deploy basics>
**Troubleshooting and ownership**: <known issues/contacts>
**Gaps and pending evidence**: <items>
```

## Failure modes to avoid

- **Onboarding encyclopedia**: making newcomers read every subsystem first.
- **Unverified setup path**: documenting commands that may not run.
- **Invisible ownership**: omitting where help, review, or escalation comes from.

More in `references/anti-patterns.md`.
