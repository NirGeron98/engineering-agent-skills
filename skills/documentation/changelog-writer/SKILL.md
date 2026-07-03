---
name: changelog-writer
description: Write changelog entries, release notes, or user/developer-facing change summaries from actual diffs, commits, PRs, or release evidence. Use when user-visible changes, fixes, breaking changes, migrations, deprecations, internal changes, upgrade notes, and known issues need a verified changelog draft.
---

# Changelog Writer

## Goal

Produce a release note or changelog entry that reflects actual changes, separates user-visible impact from internal work, and calls out breaking changes, migrations, and known issues.

## When to use

- A release, PR, or set of commits needs a changelog or release-note draft.
- User-visible changes and developer-facing changes need to be summarized accurately.
- Breaking changes, deprecations, migrations, or known issues must be called out.

## When not to use

- Release go/no-go readiness is the task; use `skills/devops-infrastructure/release-and-rollback-readiness`.
- PR correctness needs review; use `skills/review/code-review`.
- Product marketing release communication is needed; defer to future product skills if added.

## Composition and handoff rules

- Hand readiness decisions to `skills/devops-infrastructure/release-and-rollback-readiness`.
- Hand PR review to `skills/review/code-review`.
- Hand implementation context summaries to `skills/documentation/handoff-documentation`.
- This changelog is for release consumers, not the next worker: it is release-facing and diff/commit/PR-sourced, and it should exclude internal process detail unless it affects upgrade, migration, or breaking changes.

## Evidence access rule

Inspect actual diffs, commits, PRs, issues, tags, migration notes, docs, and tests before writing. If evidence is unavailable, request exact `git diff`, commit range, PR links, issue list, or release scope and mark the changelog pending.

## Workflow

1. Identify audience, release scope, version/date, source range, and changelog format.
2. Inspect diff/commits/PRs/issues and group changes by user-visible features, fixes, breaking changes, migrations, deprecations, internal changes, and known issues.
3. Verify claims against changed files, tests, docs, or issue evidence; exclude speculative or unreleased work.
4. Draft concise entries with upgrade notes and migration actions where needed.
5. Flag missing evidence, unclear impact, or readiness questions as pending.
6. Before final handoff, load `references/checklist.md`; consult `references/anti-patterns.md` when tempted to write from memory, omit breaking changes, or turn internal refactors into user claims.

## Questions to ask (only when necessary)

- Which audience and format should the changelog target if the repository has no precedent?

## Quality bar

- Each entry traces to diff, commit, PR, issue, or release evidence.
- Breaking changes, migrations, deprecations, and known issues are explicit.
- Internal-only changes are labeled as such or omitted from user-facing notes.

## Expected output format

```markdown
## Changelog draft: <version/range>

**Evidence reviewed**: <diff/commits/PRs/issues>
**Added**: <entries>
**Changed**: <entries>
**Fixed**: <entries>
**Breaking changes and migrations**: <actions>
**Known issues**: <items>
**Pending evidence**: <missing source or impact>
```

## Failure modes to avoid

- **Changelog without diff**: writing from memory or branch names.
- **Breaking-change burial**: hiding migrations inside generic changed items.
- **User-facing inflation**: presenting internal refactors as product value.

More in `references/anti-patterns.md`.
