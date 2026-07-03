# README Review Anti-Patterns

## README as brochure

- **Symptom**: The README sells the project but does not help users run or use it.
- **Why it matters**: New users cannot complete their first task.
- **Correction**: Prioritize purpose, quick start, commands, examples, and troubleshooting.

## Docs from memory

- **Symptom**: The review invents setup steps, architecture, or scripts.
- **Why it matters**: Documentation becomes a source of failure.
- **Correction**: Verify against manifests, CI, entry points, and docs.

## Stale command copy-paste

- **Symptom**: Existing README commands are repeated without checking package scripts or CI.
- **Why it matters**: Broken commands waste onboarding time.
- **Correction**: Run commands or request exact output and mark pending.

## Everything in README

- **Symptom**: The README becomes a full manual.
- **Why it matters**: First-time users cannot scan the path.
- **Correction**: Keep README short and link deeper docs.

