# Sample Task

## User prompt

"Review whether our database backups and restore process are actually ready for production incidents."

## Abbreviated run

- Ask for the backup schedule/configuration, retention settings, and stated RPO/RTO targets.
- Ask when the last verified restore test was performed.
- Obstacle: no restore test history is available; only backup job success logs exist.

## Evidence requests

- Records of the last full restore test and its outcome/timing.
- Current RPO/RTO targets as agreed with stakeholders.
- Whether backups are stored cross-region/cross-account and how access is controlled.

## Expected output

A backup/restore readiness review flagging the absence of a verified restore test as a high-risk gap (backup job success is not the same as restore success), a risk rating reflecting that gap, a concrete restore test plan with a proposed schedule, and named ownership for executing it — with RPO/RTO capability marked pending until a real restore test provides timing evidence.

## Why this passes the quality bar

The agent does not treat successful backup jobs as proof of restore readiness and requires actual restore test evidence before rating the system as ready.
