# Data Engineer Bundle

Optional role-based recommendations for engineers moving data from operational systems into warehouses and making it trustworthy through ingestion, modeling, and quality checks.

For task-based discovery, start from [../SKILL_CATALOG.md](../SKILL_CATALOG.md).

## Minimum Start

Start with shared workflow skills, then add one data skill that matches the work in front of you.

- [skills/planning/repo-onboarding](../skills/planning/repo-onboarding/SKILL.md) - map the repo, data tooling, scripts, and conventions.
- [skills/debugging/diagnose-bug](../skills/debugging/diagnose-bug/SKILL.md) - isolate a narrowed data bug from evidence.
- [skills/review/code-review](../skills/review/code-review/SKILL.md) - review implementation risk before merge.
- One of:
  - [skills/data/db-to-dwh-pipeline](../skills/data/db-to-dwh-pipeline/SKILL.md) - when the main work is operational-to-warehouse replication.
  - [skills/data/data-quality-contracts](../skills/data/data-quality-contracts/SKILL.md) - when the main work is dataset correctness or quality guarantees.

## Recommended Set

After the minimum start feels useful, add these based on the kind of data work you do most often.

- [skills/data/db-to-dwh-pipeline](../skills/data/db-to-dwh-pipeline/SKILL.md) - design or repair replication from operational databases into a warehouse, including incrementals, deletes, drift, reconciliation, and backfills.
- [skills/data/data-quality-contracts](../skills/data/data-quality-contracts/SKILL.md) - turn implicit dataset assumptions into enforced contracts for schema, grain, freshness, volume, and business rules.
- [skills/planning/task-breakdown](../skills/planning/task-breakdown/SKILL.md) - turn a data request into a sequenced, verifiable plan.

## Situational Skills

- [skills/data/dataform-bigquery-development](../skills/data/dataform-bigquery-development/SKILL.md) - use when the repo is Dataform on BigQuery, especially for SQLX models, incrementals, assertions, partitioning, cost, and dependency impact.

## Related Skills

- [skills/api-and-backend/service-debugging](../skills/api-and-backend/service-debugging/SKILL.md) when a data symptom is traced to a live service or queue.
- [skills/devops-infrastructure/ci-cd-debugging](../skills/devops-infrastructure/ci-cd-debugging/SKILL.md) when model or pipeline failures are caused by CI/CD execution, secrets, artifacts, or environment drift.
- [skills/devops-infrastructure/terraform-plan-review](../skills/devops-infrastructure/terraform-plan-review/SKILL.md) when warehouse, scheduler, IAM, or stateful data infrastructure changes are planned through Terraform/OpenTofu.
