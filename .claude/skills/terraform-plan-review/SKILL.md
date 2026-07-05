---
name: terraform-plan-review
description: Review Terraform or OpenTofu plan output before apply, especially for production or shared infrastructure. Use when create/update/delete/replace actions, IAM, networking, provider/module changes, drift, state, lifecycle rules, secrets, cost, quota, rollback, or import safety must be evaluated; produces a Terraform plan risk review, high-risk changes, safe/safe-with-conditions/do-not-apply recommendation, pre-apply checks, and rollback/state notes. Hands permission debugging to iam-permissions-debugging, deployment symptoms to deployment-debugging, and Kubernetes workload symptoms to kubernetes-workload-debugging.
---

# Terraform Plan Review

## Goal

Decide whether a Terraform/OpenTofu plan is safe to apply by reviewing the actual plan output, not repository intent, and by surfacing destructive, identity, networking, state, cost, and rollback risks.

## When to use

- A Terraform/OpenTofu plan is ready for review before apply.
- Production/shared resources may be created, updated, deleted, or replaced.
- IAM, network, database, state, provider, module, or lifecycle changes need operational review.

## When not to use

- No plan output exists yet; first request plan commands and keep the review pending.
- The problem is a runtime deployment failure after infrastructure changed; use `deployment-debugging`.
- The root issue is a permission failure, not plan risk; use `iam-permissions-debugging`.

## Composition and handoff rules

- Hand permission root-cause work to `iam-permissions-debugging`.
- Hand post-apply service/runtime failures to `deployment-debugging`.
- Hand pod/workload symptoms to `kubernetes-workload-debugging`.

## Evidence access rule

Never say a plan is safe without the actual plan output. If unavailable, request exact output such as `terraform plan -out=tfplan` and `terraform show -no-color tfplan`, then mark recommendations pending. Do not recommend `terraform apply` for production without reviewing replacements, destroys, IAM changes, and rollback/state implications.

## Workflow

1. Record workspace, environment, backend/state context, command used, Terraform/OpenTofu version, provider lockfile, and target flags.
2. Summarize planned creates, updates, deletes, replacements, and data-source reads from the actual plan.
3. Review high-risk resources: databases, networks, load balancers, IAM, service accounts, KMS/secrets, stateful storage, DNS, Kubernetes clusters, and production services.
4. Inspect why replacements occur: immutable attributes, provider/module version changes, lifecycle rules, moved/import blocks, taint, drift, or name changes.
5. Check state safety: workspace/env separation, remote backend, locks, imports, moved blocks, drift, and targeted applies.
6. Evaluate blast radius: downtime, data loss, permissions, network exposure, cost/quota, dependency ordering, and rollback feasibility.
7. Produce a recommendation: safe, safe with conditions, or do not apply, with pre-apply checks and rollback/state recovery notes.
8. Before final handoff, load `references/checklist.md`; consult `references/anti-patterns.md` when tempted to trust code, approve apply, ignore drift, or accept broad IAM changes.

## Output format

```markdown
## Terraform plan risk review: <workspace/env>

**Plan evidence**: <command/output source>
**Action summary**: <create/update/delete/replace counts>
**Recommendation**: <safe / safe with conditions / do not apply>

**High-risk changes**
- <resource> - <action> - risk: <impact> - evidence: <plan excerpt> - required check: <check>

**Pre-apply checks**: <exact checks>
**Rollback/state notes**: <rollback, import, backup, moved block, recovery notes>
```

## Final handoff requirements

- Load `references/checklist.md` and verify the review against it.
- Consult `references/anti-patterns.md` when ambiguity, risk, or shortcut temptation appears.
- Mark any safety recommendation that depends on missing plan, state, provider, environment, or rollback evidence as pending.

## Failure modes to avoid

- **Apply without plan evidence**: approving changes from `.tf` files alone.
- **Replacement blindness**: missing destroy/create semantics hidden under a small diff.
- **State surgery casualness**: recommending import, taint, or state edits without backup and recovery notes.

More in `references/anti-patterns.md`.
