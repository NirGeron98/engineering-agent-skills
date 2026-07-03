---
name: ci-cd-debugging
description: Debug CI/CD pipeline failures, flakes, or branch/environment differences using job logs, stage boundaries, cache/dependency behavior, artifacts, secrets, permissions, and runner context. Use when a build/test/deploy workflow fails in GitHub Actions, GitLab CI, CircleCI, Buildkite, Jenkins, or similar; produces a pipeline failure diagnosis, evidence table, minimal fix plan, and rerun/verification plan. Hands reproducible code defects to diagnose-bug, deployed runtime failures to deployment-debugging or service-debugging, and token/IAM scope failures to iam-permissions-debugging.
---

# CI/CD Debugging

## Goal

Find the failing pipeline boundary and fix or route it with log evidence, without treating reruns, cache clears, or secret changes as root-cause analysis.

## When to use

- A CI/CD stage, job, matrix entry, deploy step, or release workflow fails or flakes.
- The pipeline behaves differently by branch, path, runner, environment, or dependency/cache state.
- Build passes locally but fails in CI, or CI passes but deploy does not start as expected.

## When not to use

- A failing test is reproducible locally; use `diagnose-bug`.
- The deployed service is unhealthy after a successful deployment; use `deployment-debugging` or `service-debugging`.
- The root issue is a missing cloud role, service account, token scope, or permission binding; use `iam-permissions-debugging`.

## Composition and handoff rules

- Use `diagnose-bug` only after the failing command is reproducible outside CI.
- Use `deployment-debugging` when CI produced an artifact/deploy but runtime state is wrong.
- Use `iam-permissions-debugging` when the principal, action, and resource point to permission scope.

## Evidence access rule

When you cannot inspect CI logs, workflow config, runner context, artifacts, or secrets metadata yourself, output exact log links, commands, or fields for the user to provide and mark CI conclusions pending. Do not invent log lines, runner state, secret values, or deploy outcomes.

## Workflow

1. Identify the failing workflow, run, stage, job, matrix entry, branch, commit, runner, and environment.
2. Capture the first failing log section, not just the final red line; request logs if unavailable.
3. Compare recent changes in workflow files, lockfiles, dependency manifests, build scripts, environment config, cache keys, and deploy config.
4. Inspect conditions: branch/path filters, matrix exclusions, needs/dependencies, artifact paths, environment gates, concurrency, and rerun behavior.
5. Check secrets/tokens without exposing values: presence, scope, environment mapping, expiration, fork restrictions, and protected branch rules.
6. Classify the boundary: dependency/cache, test/build, artifact, condition, secret/permission, runner/toolchain, deploy trigger, or code defect.
7. Propose the smallest fix and a verification plan: targeted rerun, cache-safe rerun, local reproduction, or read-only command.
8. Before final handoff, load `references/checklist.md`; consult `references/anti-patterns.md` if tempted to rerun, clear caches, widen permissions, or blame code without evidence.

## Output format

```markdown
## Pipeline failure diagnosis: <workflow/job>

**Run evidence**: <run URL/id, commit, branch, job, failing command/log excerpt>
**Boundary classification**: <cache/dependency/test/build/artifact/condition/secrets/permissions/runner/deploy/code>

| Evidence | Observed | Meaning | Status |
|---|---|---|---|
| <log/config/artifact> | <fact> | <implication> | <confirmed/pending> |

**Findings**
- <severity> - <issue> - evidence: <log/config path> - fix: <minimal action>

**Verification/rerun plan**: <exact command, CI rerun scope, or pending evidence>
**Handoff**: <owner or next skill>
```

## Final handoff requirements

- Load `references/checklist.md` and verify the diagnosis against it.
- Consult `references/anti-patterns.md` when ambiguity, risk, or shortcut temptation appears.
- Mark any CI log, runner, secret, token, artifact, or deploy conclusion that depends on missing evidence as pending.

## Failure modes to avoid

- **Rerun roulette**: treating a green rerun as a fix without explaining the original failure.
- **Log-free root cause**: diagnosing CI from repository files without the failing job log.
- **Permission broadening**: making a token admin to prove a CI step can pass.

More in `references/anti-patterns.md`.
