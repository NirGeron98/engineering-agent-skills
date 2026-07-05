# Terraform Plan Review - Anti-patterns

## Apply without plan evidence
**Symptom**: approval is based on `.tf` files, PR description, or expected intent.
**Why it matters**: providers, state drift, modules, and computed attributes decide the real changes.
**Correction**: require actual plan output and mark all apply advice pending until reviewed.

## Replacement blindness
**Symptom**: `-/+` replacements are summarized as ordinary updates.
**Why it matters**: replacements can destroy databases, IPs, service accounts, or routing paths.
**Correction**: list each replacement, why it happens, downtime/data risk, and rollback path.

## Broad IAM acceptance
**Symptom**: wildcard permissions or owner/editor roles are accepted as routine plan noise.
**Why it matters**: infrastructure plans can silently expand blast radius.
**Correction**: require least-privilege justification or route root-cause permission work to `iam-permissions-debugging`.

## State surgery casualness
**Symptom**: import, state rm, taint, or manual state edits are suggested without backup.
**Why it matters**: state mistakes can orphan or destroy live resources.
**Correction**: require state backup, lock awareness, exact commands, and recovery notes.

## Destroy drift confusion
**Symptom**: deletes caused by wrong workspace/project/account are treated as intended cleanup.
**Why it matters**: an environment mix-up can remove production resources.
**Correction**: verify workspace, backend, provider account/project/subscription, and resource addresses.
