# Terraform Plan Review - Checklist

## Plan evidence
- [ ] Actual `terraform show -no-color` or equivalent plan output reviewed
- [ ] Workspace/environment/backend recorded
- [ ] Terraform/OpenTofu and provider versions checked where relevant
- [ ] Target flags, refresh behavior, and plan command recorded
- [ ] Plan output unavailable items are marked pending with exact command request

## Action risk
- [ ] Create/update/delete/replace counts summarized
- [ ] Every delete and replacement reviewed
- [ ] Database/storage/stateful resources checked
- [ ] Network, DNS, load balancer, firewall, and routing changes checked
- [ ] IAM/service account/policy changes checked
- [ ] Secret/KMS/sensitive output exposure checked
- [ ] Cost and quota risk considered for new or scaled resources

## State and lifecycle
- [ ] Drift and refresh-only implications checked
- [ ] Provider/module input changes checked
- [ ] Lifecycle rules, moved blocks, import blocks, taint, and dependencies checked
- [ ] Environment separation and remote state safety checked
- [ ] Rollback or state recovery notes included

## Decision
- [ ] Recommendation is safe, safe with conditions, or do not apply
- [ ] Production apply is not approved without replacements/destroys/IAM reviewed
- [ ] Pre-apply checks are exact
- [ ] Permission, deployment, or Kubernetes follow-up is routed to the right skill
