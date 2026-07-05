# Code Review — Checklist

## Setup
- [ ] Change intent understood from PR description / commits / issue
- [ ] Diff taken against the PR's base branch, not stale local state
- [ ] Generated files and lockfiles excluded from review attention
- [ ] Full diff read once for shape (APIs, schemas, deletions, error paths)
- [ ] Large diff: partitioned by risk; lighter-coverage areas named in the report
- [ ] Significant hunks read in surrounding-file context, including callers

## High-yield hunting
- [ ] Contract changes: all consumers of changed APIs/schemas/events located and checked
- [ ] Boundaries: null/empty/zero, off-by-one, timezone, pagination edges
- [ ] Error paths: failures not swallowed; no half-committed state; retries safe
- [ ] State/concurrency: shared mutation, transaction scope, idempotency
- [ ] Security: input validation, authz on new/changed endpoints, secrets, injection
- [ ] Absences: missing migration rollback, missing config, stale docs/OpenAPI

## Findings discipline
- [ ] Every finding has file:line + concrete failure scenario
- [ ] Every finding verified by trace or execution before reporting
- [ ] Severity ranked; preferences labeled as preferences
- [ ] No style comments a formatter/linter owns

## Tests
- [ ] Added tests fail without the change (spot-check)
- [ ] Risky paths above have coverage; gaps reported as findings

## Report
- [ ] Verdict stated
- [ ] "Checked and clean" section lists inspected risk areas
