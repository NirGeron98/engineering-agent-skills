---
name: repo-onboarding
description: Build an evidence-based orientation map of an unfamiliar repository before a nontrivial change — and persist it so future sessions don't re-pay the exploration cost. Use before multi-file work in an unmapped codebase, when asked "how does this project work", or when CLAUDE.md is missing or stale. Produces a durable repo map (stack from manifests, entry points, conventions, verified test commands, docs-vs-reality gaps) written to the repo, citing files actually read. Not for single-file or trivial edits, and not for repos with a current, accurate CLAUDE.md.
---

# Repo Onboarding

## Goal

Produce an accurate, evidence-backed orientation map of a repository — enough to plan and execute work without misreading the architecture — and **persist it in the repo** so the next session (or the next agent) starts oriented instead of re-exploring. Every claim cites a real file.

## When to use

- Before the first **multi-file or architecturally risky** task in a repository this session.
- The user asks "how is this project structured" or "where does X happen".
- Before `task-breakdown` on a repo you haven't mapped.
- `CLAUDE.md` / repo-map docs exist but are stale or contradict the code.

## When not to use

- Single-file or trivially scoped edits ("fix this typo", "rename this variable") — just do them.
- You already mapped this repo in the current session — don't re-onboard.
- The repo has a current, trustworthy `CLAUDE.md` that answers the question — read it, verify the parts you'll rely on, and only fill gaps.

## Workflow

1. **Read what already exists, skeptically.** `README.md`, `CLAUDE.md`, `CONTRIBUTING.md`, root `ls`, and the manifests (`package.json`, `pyproject.toml`, `go.mod`, `Cargo.toml`, Dockerfile, CI config). Record stack, package manager, build/test tooling **from manifests, not folder names**. Note doc claims as unverified until checked.
2. **Scope to the target.** In a monorepo/workspace, identify which package(s) the task touches (workspace config, `pnpm-workspace.yaml`, `turbo.json`, bazel targets) and map **that package**, not the whole tree. Note only the cross-package dependencies that matter.
3. **Trace entry points and map the structure.** Open the actual boot/main/CLI/job files — don't infer from names. For each major source directory in scope, open 1–2 representative files and state what it actually contains; flag directories whose name and contents disagree.
4. **Extract conventions and the verification loop.** Read 2–3 recently modified files (`git log --stat`) for naming, error handling, test placement, and import style — these bind all later work here. Find the exact install/build/lint/test commands in scripts or CI; run the test suite (or a cheap subset) when safe and record the baseline. Note risk areas: migrations, generated/vendored code, feature flags, "do not edit" zones.
5. **Persist the map.** Write the orientation summary (format below) to the repo: update `CLAUDE.md` if the project uses one, otherwise create `docs/REPO-MAP.md`. Don't assume either location is acceptable — if the target location is ambiguous or the repo forbids new files, ask where to place it, or present the map as a handoff-ready draft instead of silently dropping the persistence requirement. Mark it with the date and the commit it was verified against.
6. **Hand off.** Present the map, list what you deliberately didn't cover, and confirm the persisted file path. Before finishing, load `references/checklist.md` and verify every item; if any step felt ambiguous, consult `references/anti-patterns.md`.

## Questions to ask (only when necessary)

- Which of multiple apps/packages in a monorepo is the target? (Only if the task doesn't make it obvious.)
- Where should the persisted map live, if the repo has no `CLAUDE.md` and a `docs/` convention isn't evident?

Everything else — stack, structure, conventions — must come from investigation, not from asking.

## Quality bar

- Every architectural claim cites at least one file you actually opened.
- Stack identification comes from manifests/lockfiles, not directory names.
- Test/build commands are copied from config or scripts, and verified runnable when safe.
- Discrepancies between docs and code are called out explicitly, not silently resolved.
- The map is **written to a file in the repo**, dated, and scoped honestly (uncovered areas named).

## Expected output format

Persisted to `CLAUDE.md` or `docs/REPO-MAP.md`:

```markdown
## Repo orientation: <repo name> (verified <date> @ <commit>)

**Scope**: <whole repo | package X of monorepo>
**Stack**: <languages, frameworks, versions> (from <manifest files>)
**Run/test**: <exact commands> (verified: yes/no + result)

**Entry points**
- <path> — <what starts here>

**Structure** (verified by reading, not inferred)
- <dir>/ — <actual contents> (<representative file read>)

**Conventions observed**
- <convention> (seen in <file>)

**Risk areas / surprises**
- <finding> (<file>)

**Docs vs. reality**
- <any place README/docs disagree with code, or "docs match code">

**Not covered**
- <areas skipped in this pass>
```

## Failure modes to avoid

- **The disposable map**: a perfect orientation summary printed to chat and lost when the session ends — persisting it is the point of this skill.
- **Folder-name archaeology**: describing `services/` as "the service layer" without opening a file in it.
- **Doc trust**: repeating README claims (commands, architecture) without verifying against code or config.

More in `references/anti-patterns.md`.
