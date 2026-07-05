# Release and Rollback Readiness - Checklist

## Scope
- [ ] Release scope and changed services/resources recorded
- [ ] Environment and user/customer impact recorded
- [ ] Owners and decision maker recorded
- [ ] Feature flags/config/env changes listed
- [ ] Database migration or data backfill impact listed
- [ ] Terraform/infrastructure changes listed or ruled out

## Pre-release checks
- [ ] CI/build/test status checked
- [ ] Deployment sequence and gates checked
- [ ] Artifact/image/version identified
- [ ] Smoke tests and health checks defined
- [ ] API/data/backward compatibility checked where relevant
- [ ] Terraform plan review linked for infra changes where relevant
- [ ] Kubernetes readiness checks linked where relevant

## Rollback and monitoring
- [ ] Rollback path exists or rollback-impossible reason documented
- [ ] Previous version/traffic rollback/flag disable/migration recovery documented
- [ ] Rollback owner and command/runbook identified
- [ ] Dashboards, alerts, logs, synthetics, or business metrics identified
- [ ] Observation window and no-go/abort triggers defined

## Decision
- [ ] Decision is go, go with conditions, or no-go
- [ ] Pending evidence blocks unconditional approval
- [ ] Post-release verification steps are exact
- [ ] Active incidents routed to `incident-triage-runbook`
