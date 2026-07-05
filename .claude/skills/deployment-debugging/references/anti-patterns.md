# Deployment Debugging - Anti-patterns

## Repository-state mirage
**Symptom**: the agent assumes code in the repo is what is deployed.
**Why it matters**: deployed revisions, tags, digests, and config can drift.
**Correction**: verify deployment id, artifact digest, release time, and runtime config.

## Restart as diagnosis
**Symptom**: restarting or redeploying is proposed before logs and health evidence.
**Why it matters**: it destroys evidence and can widen an outage.
**Correction**: collect read-only logs, status, config, and routing data first.

## Blaming code for config drift
**Symptom**: a source patch is proposed for an environment-specific failure.
**Why it matters**: the fix may not affect the deployed runtime and can introduce new risk.
**Correction**: compare runtime config against healthy environment and previous revision.

## Rollback-free release
**Symptom**: production changes proceed without a known rollback candidate.
**Why it matters**: recovery becomes improvisation under pressure.
**Correction**: identify previous good revision, rollback command, data compatibility, and verification.

## Log-free root cause
**Symptom**: service health is explained without runtime logs or platform events.
**Why it matters**: startup, probe, identity, and dependency failures look similar externally.
**Correction**: request exact log query and mark conclusions pending until logs arrive.
