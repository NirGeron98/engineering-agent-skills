# Roadmap

This repository is currently focused on a small, workflow-based engineering skills library. The goal is to keep the entry path simple: choose the task you need, open the matching skill, and add optional bundles only when useful.

## Approved bundles

- Backend Developer
- Frontend Developer
- Data Engineer
- DevOps / Cloud Engineer

## Current user-facing model

- `README.md` is the starting point.
- `SKILL_CATALOG.md` is the task-based discovery surface.
- `skills/` contains workflow/domain skill folders.
- `bundles/` contains optional role-based recommendations.

## Domain Status

### Approved

- planning
- debugging
- review
- api-and-backend
- frontend
- data
- devops-infrastructure
- architecture
- testing
- documentation
- security-review
- observability
- database
- performance

These domains are considered stable review domains and should not be rewritten during expansion.

### Draft / Needs Audit

None currently

### Planned

- code-quality (postponed: heavy overlap with architecture/review/refactor/dependency skills)
- ai-engineering (postponed until after hardening/v1.0; useful but not launch-critical)

These domains are planned but not yet drafted, and are not the immediate next wave. Each must be added as a controlled wave and start as Draft / Needs Audit only after hardening is complete.

### Not Now / Revisit Later

- product
- collaboration
- delivery as a standalone domain
- platform-engineering
- cloud-architecture
- data-analysis

These are deferred because they are currently too broad, too role-oriented, or have high overlap risk. Revisit only if concrete, non-duplicate workflows are identified.

## Current domains (detail)

- Architecture domain: system design review, ADRs, module boundaries, coupling, technical debt triage, and safe refactor strategy.
- Testing domain: test strategy, TDD workflow, legacy characterization tests, integration test design, E2E test debugging, and flaky test debugging.
- Documentation domain: README review, API docs review, runbook writing, handoff documentation, changelog writing, and developer onboarding docs.
- Security Review domain (Approved): secrets exposure review, dependency vulnerability triage, input validation security review, access control boundary review, and security incident scoping. Defensive only.
- Observability domain (Approved): metrics and alerts review, log signal quality review, trace-based latency diagnosis, SLO definition review, and dashboard signal audit.
- Database domain (Approved): query performance review, schema migration safety review, index strategy review, data consistency incident diagnosis, connection pool and locking debugging, and backup/restore readiness.
- Performance domain (Approved): hot path profiling review, memory leak diagnosis, throughput bottleneck triage, caching strategy review, and load test result review.

## Expansion Waves

**Wave 1: Finish Testing + Documentation audit. (Completed)**

Included:

- critical audit
- patches if needed
- live smoke tests
- approval decision

**Wave 2: Security Review + Observability. (Completed)**

Included:

- security-review must not duplicate auth-flow-review
- observability must not duplicate incident-triage-runbook
- both are evidence-first and started as Draft / Needs Audit

**Wave 3: Database + Performance. (Completed)**

Included:

- database focuses on OLTP/query/schema/migration/operational database workflows
- performance avoids duplicating frontend-performance-review and is backend/systems scoped
- both are evidence-first and started as Draft / Needs Audit

**Wave 4: Hardening + Core Coverage.**

A strategic audit after Wave 3 recommended pausing broad domain expansion: the repository is already large enough to be credible, and the main risk is now imbalance, stale discovery surfaces, overlap, and older domains not being held to the current audit bar. This replaces Code Quality + AI Engineering as the immediate next wave.

Include:

- fix README drift and stale counts/statuses
- re-audit the oldest approved domains using the current audit standard: planning, debugging, review, api-and-backend, frontend, data, devops-infrastructure
- add only a small number of high-leverage missing workflows if needed
- do not add broad new domains before v1.0
- keep Code Quality and AI Engineering as post-hardening candidates, not the immediate next wave

Candidate high-leverage skills to consider later in this phase (not to be created yet):

- regression-bisection (debugging)
- race-condition-and-concurrency-debugging (debugging)
- environment-diff-debugging (debugging)
- background-jobs-and-queue-review (api-and-backend)
- spike-investigation (planning)
- pr-authoring-and-self-review (review)

**Wave 5: Cloud Architecture as a possible solo wave.**

Include:

- only add if it can be clearly separated from architecture-review, devops-infrastructure, and terraform-plan-review
- keep it small if added

## Expansion Rules

- one skill = one real workflow
- no generic topic skills
- no role-first expansion
- every skill must produce a concrete artifact
- every skill must be evidence-first
- every new domain starts as Draft / Needs Audit
- domains become Approved only after critical audit and smoke tests
- README.md should stay short
- SKILL_CATALOG.md is the exhaustive discovery surface
- bundles/ should not be updated for Draft domains

## Optional polish backlog

- Add `terraform show -json tfplan` as an optional structured-analysis command in `terraform-plan-review`.
- Add a GCP Eventarc / Cloud Run IAM example in `iam-permissions-debugging`.
- Add server normalization / canonicalization coverage in `form-validation-review`.
- Add component API compatibility / migration impact coverage in `component-design-review`.
- Add a stronger auth headers, cookies, and CORS example in `api-integration-debugging`.
- Add RTL autofill / cursor fixture wording in `rtl-ui-review`.

## Next candidate bundles

None planned before v1.0. Product Manager and Data Analyst bundles remain Not Now / Revisit Later — do not add them unless concrete, non-duplicate workflows justify them later.

## v1.0 Launch Checklist

1. Fix packaging/documentation drift (EVALUATION.md scenario counts and statuses, ROADMAP.md stale sections, missing template file, missing LICENSE).
2. Validate all markdown links and skill-folder file completeness (all 63 skills have `SKILL.md`, `references/checklist.md`, `references/anti-patterns.md`, `examples/sample-task.md`).
3. Add a LICENSE file.
4. Initialize git if the repository is not already one, and make an initial commit.
5. Tag `v1.0.0`.

## v1.0 Change Control

- After v1.0, `README.md`, `SKILL_CATALOG.md`, `ROADMAP.md`, `EVALUATION.md`, `templates/`, and approved skill bodies are stable surfaces.
- Do not modify these files unless a task explicitly asks for maintenance, a bug fix, or a targeted approved change.
- New domains or skills must start as Draft / Needs Audit.
- Approved skills should not be rewritten broadly; any edit to an approved skill must be targeted, evidence-backed, and followed by validation.
- `README.md` stays short. `SKILL_CATALOG.md` stays the exhaustive discovery surface. `ROADMAP.md` stays the single status and expansion planning surface.
