# CI/CD Debugging - Anti-patterns

## Rerun roulette
**Symptom**: a flaky job passes after rerun and the incident is closed.
**Why it matters**: cache, network, race, and runner issues return under load.
**Correction**: compare failed and passing runs, isolate the flaky boundary, and document what a rerun did or did not prove.

## Log-free root cause
**Symptom**: the agent diagnoses from workflow YAML without the failed log section.
**Why it matters**: CI failures often happen inside called scripts, tools, or remote actions.
**Correction**: request or inspect the first failing log block and mark source-only theories pending.

## Cache clear cure
**Symptom**: the fix is "clear CI cache" with no cache key or dependency explanation.
**Why it matters**: it hides nondeterminism and can make future builds slower or still broken.
**Correction**: inspect cache keys, restore behavior, lockfiles, and dependency install commands.

## Broad token fix
**Symptom**: `write-all`, admin, owner, or broad cloud credentials are added to make a job pass.
**Why it matters**: CI tokens are high-value production paths.
**Correction**: identify principal, action, and resource; hand permission root cause to `iam-permissions-debugging`.

## Artifact assumption
**Symptom**: deploy is debugged while the artifact was never built, named, uploaded, or downloaded correctly.
**Why it matters**: runtime work wastes time if the pipeline boundary failed earlier.
**Correction**: verify artifact path, version, digest, and handoff between jobs.
