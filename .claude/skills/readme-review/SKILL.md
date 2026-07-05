---
name: readme-review
description: Review or improve a project README against actual repository evidence. Use when onboarding, purpose, quick start, install/run/test commands, environment variables, examples, architecture overview, troubleshooting, contribution guidance, and stale claims need a README review, missing-section list, and patch plan.
---

# README Review

## Goal

Evaluate whether a README helps a real user understand, run, test, and contribute to the project, while verifying claims against the repository instead of writing brochure copy.

## When to use

- A project README needs review, restructuring, or accuracy checks.
- Setup, run, test, environment, example, or contribution instructions may be stale.
- The user needs concrete README edits or a patch plan.

## When not to use

- The repo itself is not understood yet; use `.claude/skills/repo-onboarding`.
- The task is new developer onboarding across many docs; use `.claude/skills/developer-onboarding-docs`.
- API reference documentation is the target; use `.claude/skills/api-docs-review`.

## Composition and handoff rules

- Hand repo exploration to `.claude/skills/repo-onboarding` if the project structure is unclear.
- Hand broader onboarding documentation to `.claude/skills/developer-onboarding-docs`.
- Hand API documentation review to `.claude/skills/api-docs-review`.

## Evidence access rule

Verify README claims against actual files, package scripts, CI, examples, environment templates, docs, tests, and code entry points. If commands cannot be run, request exact command output and mark command accuracy pending.

## Workflow

1. Identify README audience, project purpose, install/run/test path, and maintenance or contribution expectations.
2. Inspect manifests, scripts, CI, env examples, docs, code entry points, examples, and tests that support or contradict the README.
3. Check for missing sections, stale commands, undocumented prerequisites, misleading architecture claims, and troubleshooting gaps.
4. Propose a concise structure and specific edits, separating verified facts from pending evidence.
5. If editing, keep the README task-oriented and avoid expanding it into full docs.
6. Before final handoff, load `references/checklist.md`; consult `references/anti-patterns.md` when tempted to write from memory, turn the README into a brochure, or copy stale commands.

## Questions to ask (only when necessary)

- Who is the primary README audience if the repository does not make it clear?

## Quality bar

- README claims cite repo evidence or are marked pending.
- Commands are verified or exact verification requests are emitted.
- The output includes specific edits, not generic advice.

## Expected output format

```markdown
## README review: <repo/project>

**Evidence reviewed**: <files/commands/docs>
**What works**: <clear useful sections>
**Missing or stale items**: <issue - evidence - impact>
**Suggested structure**: <section order>
**Specific edits or patch plan**: <changes>
**Pending evidence**: <commands/questions>
```

## Failure modes to avoid

- **README as brochure**: explaining value while hiding setup and usage.
- **Docs from memory**: inventing commands or architecture claims.
- **Stale command copy-paste**: repeating existing README commands without checking scripts or CI.

More in `references/anti-patterns.md`.
