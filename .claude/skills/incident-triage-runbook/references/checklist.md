# Incident Triage Runbook - Checklist

## Incident frame
- [ ] Severity/status recorded
- [ ] Customer/user/business impact recorded or marked pending
- [ ] Affected systems and environments listed
- [ ] Owner/incident commander recorded or absence flagged
- [ ] Next update time stated for communications

## Evidence
- [ ] Timeline includes onset and detection
- [ ] Recent deploy/config/infrastructure/feature flag changes checked or requested
- [ ] Metrics/logs/traces/alerts captured or exact queries requested
- [ ] Customer/support reports included where available
- [ ] Known unknowns are explicit
- [ ] No telemetry, owner, or impact claim is invented

## Stabilization
- [ ] Mitigation options listed with risk
- [ ] Rollback candidate considered
- [ ] Evidence preservation considered before mutation
- [ ] Production-changing action has safety check and rollback
- [ ] Specialist handoffs are named for narrowed work

## Handoff
- [ ] Timeline is clear enough for postmortem
- [ ] Hypotheses include evidence for/against and next check
- [ ] Communication update is neutral and non-blameful
- [ ] Follow-up items separate immediate mitigation from root cause
