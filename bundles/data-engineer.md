# Data Engineer Bundle

Optional role-based recommendations for engineers moving data from operational systems into warehouses and making it trustworthy through ingestion, modeling, and quality checks.

For task-based discovery, start from [../SKILL_CATALOG.md](../SKILL_CATALOG.md).

## Minimum Start

Start with shared workflow skills, then add one data skill that matches the work in front of you.

- [.claude/skills/repo-onboarding](../.claude/skills/repo-onboarding/SKILL.md) - map the repo, data tooling, scripts, and conventions.
- [.claude/skills/diagnose-bug](../.claude/skills/diagnose-bug/SKILL.md) - isolate a narrowed data bug from evidence.
- [.claude/skills/code-review](../.claude/skills/code-review/SKILL.md) - review implementation risk before merge.
- One of:
  - [.claude/skills/db-to-dwh-pipeline](../.claude/skills/db-to-dwh-pipeline/SKILL.md) - when the main work is operational-to-warehouse replication.
  - [.claude/skills/data-quality-contracts](../.claude/skills/data-quality-contracts/SKILL.md) - when the main work is dataset correctness or quality guarantees.

## Recommended Set

After the minimum start feels useful, add these based on the kind of data work you do most often.

- [.claude/skills/db-to-dwh-pipeline](../.claude/skills/db-to-dwh-pipeline/SKILL.md) - design or repair replication from operational databases into a warehouse, including incrementals, deletes, drift, reconciliation, and backfills.
- [.claude/skills/data-quality-contracts](../.claude/skills/data-quality-contracts/SKILL.md) - turn implicit dataset assumptions into enforced contracts for schema, grain, freshness, volume, and business rules.
- [.claude/skills/data-freshness-investigation](../.claude/skills/data-freshness-investigation/SKILL.md) - diagnose why a warehouse table or dashboard is stale by bisecting the pipeline to the broken stage.
- [.claude/skills/data-reconciliation-investigation](../.claude/skills/data-reconciliation-investigation/SKILL.md) - find where two datasets that should agree diverge, with row-level proof and a verdict on which is right.
- [.claude/skills/backfill-planning-and-verification](../.claude/skills/backfill-planning-and-verification/SKILL.md) - plan a safe, idempotent, cost-estimated backfill with validation and a rollback path.
- [.claude/skills/data-pipeline-cost-review](../.claude/skills/data-pipeline-cost-review/SKILL.md) - attribute warehouse spend to its top drivers and propose cost cuts that don't break correctness or freshness.
- [.claude/skills/task-breakdown](../.claude/skills/task-breakdown/SKILL.md) - turn a data request into a sequenced, verifiable plan.

## Situational Skills

- [.claude/skills/dataform-bigquery-development](../.claude/skills/dataform-bigquery-development/SKILL.md) - use when the repo is Dataform on BigQuery, especially for SQLX models, incrementals, assertions, partitioning, cost, and dependency impact.

## Related Skills

- [.claude/skills/service-debugging](../.claude/skills/service-debugging/SKILL.md) when a data symptom is traced to a live service or queue.
- [.claude/skills/ci-cd-debugging](../.claude/skills/ci-cd-debugging/SKILL.md) when model or pipeline failures are caused by CI/CD execution, secrets, artifacts, or environment drift.
- [.claude/skills/terraform-plan-review](../.claude/skills/terraform-plan-review/SKILL.md) when warehouse, scheduler, IAM, or stateful data infrastructure changes are planned through Terraform/OpenTofu.
