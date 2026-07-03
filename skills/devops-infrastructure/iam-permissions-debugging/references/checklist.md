# IAM Permissions Debugging - Checklist

## Error evidence
- [ ] Exact error code/message captured
- [ ] Timestamp, environment, and operation recorded
- [ ] API method/verb/action identified
- [ ] Resource id/name/scope identified
- [ ] Missing evidence has exact command/request and pending status

## Identity
- [ ] Effective principal identified
- [ ] Runtime identity distinguished from human identity
- [ ] CI token, service account, managed identity, assumed role, or pod service account checked where relevant
- [ ] Impersonation/federation/trust/audience checked where relevant
- [ ] Token scopes, expiration, and session context checked where relevant

## Policy
- [ ] Current bindings/roles/policies/RBAC checked at relevant scopes
- [ ] Inherited permissions and deny/conditional policies checked where relevant
- [ ] Cross-project/account/subscription/namespace boundary checked
- [ ] Least-privilege role or permission set identified
- [ ] Broad admin/editor/owner fix avoided or explicitly rejected

## Handoff
- [ ] Principal/action/resource matrix included
- [ ] Verification command is exact
- [ ] Blast radius and rollback/removal notes included
- [ ] CI, deployment, or Kubernetes follow-up routed when appropriate
