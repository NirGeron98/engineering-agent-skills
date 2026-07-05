# Changelog Writer Anti-Patterns

## Changelog without diff

- **Symptom**: Release notes are written from memory or branch names.
- **Why it matters**: They miss changes or invent impact.
- **Correction**: Inspect diff, commits, PRs, issues, or release tags.

## Breaking-change burial

- **Symptom**: Required migrations hide under generic changed bullets.
- **Why it matters**: Users upgrade unsafely.
- **Correction**: Put breaking changes and migration steps in a dedicated section.

## User-facing inflation

- **Symptom**: Internal refactors are described as visible product improvements.
- **Why it matters**: Release notes lose credibility.
- **Correction**: Label internal changes or omit them from user-facing notes.

## Missing known issues

- **Symptom**: The changelog presents a clean release while unresolved issues remain.
- **Why it matters**: Users cannot plan around limitations.
- **Correction**: Include verified known issues and workarounds.

