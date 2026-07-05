# Sample task - CI/CD Debugging

## Prompt

"GitHub Actions started failing on the deploy job after a dependency/cache change. The build passes locally."

## Abbreviated run

1. Opened workflow YAML and identified the failing deploy job depends on the build artifact.
2. Could not inspect the CI run, so requested the exact failed log block and cache metadata.
3. Compared dependency and lockfile changes with cache key construction.
4. Classified the boundary as pending: cache/artifact until logs confirm.
5. Loaded `references/checklist.md` before handoff.

## Obstacle evidence request

```bash
gh run view <run-id> --log-failed
gh run view <run-id> --json headSha,event,conclusion,jobs
```

Ask for the deploy job cache-hit lines, artifact download lines, and first command that exits non-zero.

## Expected output

```markdown
## Pipeline failure diagnosis: deploy

**Run evidence**: pending failed deploy log.
**Boundary classification**: dependency/cache or artifact handoff, pending logs.

**Findings**
- Pending - backend build passes locally, but CI deploy may be restoring an old dependency cache because cache key does not include the lockfile hash - evidence: workflow cache key - verification: inspect failed run cache-hit line.

**Verification/rerun plan**: rerun only the deploy workflow after updating cache key; compare artifact digest.
```

## Why this passes the quality bar

It does not treat local success or a green rerun as proof. It requests exact CI evidence, marks dependent claims pending, and avoids widening permissions or mutating production.
