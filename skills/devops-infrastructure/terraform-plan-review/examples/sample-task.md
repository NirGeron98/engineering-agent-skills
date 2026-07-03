# Sample task - Terraform Plan Review

## Prompt

"Review this Terraform plan before apply. It appears to replace a production database/network/service account resource."

## Abbreviated run

1. Requested actual plan output because code diff alone is insufficient.
2. Reviewed replacements, deletes, IAM, networking, and state context from the plan.
3. Classified the plan as do not apply until production replacement risk is explained.
4. Loaded `references/checklist.md` before handoff.

## Obstacle evidence request

```bash
terraform plan -out=tfplan
terraform show -no-color tfplan
terraform workspace show
terraform version
```

Include backend/workspace/account context and whether `-target` or `-refresh=false` was used.

## Expected output

```markdown
## Terraform plan risk review: prod

**Plan evidence**: pending actual plan output.
**Recommendation**: do not apply until replacement evidence is reviewed.

**High-risk changes**
- Pending - production database/network/service account replacement suspected - evidence: user summary only, not enough - required check: inspect `-/+` plan blocks and immutable attribute diffs.
```

## Why this passes the quality bar

It refuses to call a production plan safe without the plan, requests exact commands, and focuses on replacement, IAM, state, and rollback risk.
