# Sample Task

## User prompt

"Write the changelog entry for everything since v0.8.2."

## Abbreviated run

- Inspect `git log v0.8.2..HEAD`, PR titles, changed files, migrations, docs updates, and tests.
- Group changes by feature, fix, breaking change, and internal.
- Obstacle: PR descriptions are unavailable locally.

## Evidence requests

- `git log --oneline v0.8.2..HEAD`
- `git diff --stat v0.8.2..HEAD`
- PR list or release issue for the same range.

## Expected output

A changelog draft with evidence source, user-visible changes, fixes, breaking changes/migrations, known issues, and pending PR context.

## Why this passes the quality bar

The changelog is based on actual change evidence and does not invent product impact.

