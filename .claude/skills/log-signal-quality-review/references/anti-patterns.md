# Log Signal Quality Review Anti-Patterns

## Logs from memory

- **Symptom**: The review describes typical logging issues without inspecting actual log samples or code.
- **Why it matters**: Generic assumptions may not match this system's real logging behavior.
- **Correction**: Sample actual logs across normal and failure conditions before writing findings.

## Log-everything advice

- **Symptom**: The recommendation is to "add more logging" broadly instead of naming specific points.
- **Why it matters**: Unfocused logging increases noise and cost without improving signal.
- **Correction**: Name exact functions/boundaries where a log point should be added or removed.

## Dashboard as wallpaper (log edition) / noise blindness

- **Symptom**: Extremely repetitive or low-value log lines are not flagged as noise.
- **Why it matters**: High-volume noise buries the signal needed during an incident.
- **Correction**: Explicitly flag repetitive/low-value log lines as candidates for removal or level change.

## Sensitive-data conflation

- **Symptom**: A sensitive-data-in-logs finding is treated as a full incident response task instead of a review finding with a handoff note.
- **Why it matters**: This blurs scope between a quality review and an active exposure incident.
- **Correction**: Note the risk and recommend `.claude/skills/secrets-exposure-review` if exposure appears confirmed.

## Correlation blindness

- **Symptom**: Missing request/trace IDs across service boundaries are not flagged.
- **Why it matters**: Without correlation IDs, cross-service debugging becomes guesswork.
- **Correction**: Explicitly check for and recommend consistent correlation ID propagation.
