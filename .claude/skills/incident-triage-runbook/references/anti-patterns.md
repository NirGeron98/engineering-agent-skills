# Incident Triage Runbook - Anti-patterns

## Root cause theater
**Symptom**: a confident cause is announced before evidence and timeline exist.
**Why it matters**: teams optimize around the wrong fix while impact continues.
**Correction**: keep hypotheses separate from confirmed facts and attach next checks.

## Invented telemetry
**Symptom**: metrics, logs, traces, or user counts are described without access.
**Why it matters**: false precision misleads decisions and communications.
**Correction**: request exact dashboard panels, queries, screenshots, or pasted output.

## Mitigation delay
**Symptom**: deep debugging continues while a known rollback or flag disable could reduce impact.
**Why it matters**: incident work prioritizes customer recovery over intellectual closure.
**Correction**: evaluate safe mitigation early, then continue root-cause tracks.

## Production mutation before read-only evidence
**Symptom**: restarts, scaling, traffic shifts, or config changes happen before preserving evidence.
**Why it matters**: mutations can erase the root cause and make recovery harder.
**Correction**: capture minimum read-only evidence and pair any mutation with rollback.

## Blameful record
**Symptom**: timeline and updates focus on who caused the issue.
**Why it matters**: it damages trust and misses system fixes.
**Correction**: write neutral event/evidence language and save accountability discussion for process owners.
