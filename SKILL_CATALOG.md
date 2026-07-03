# Skill Catalog

Use this when you know the task but not the skill name.

## Planning

- `repo-onboarding`
  - Use when: you need to understand an unfamiliar repository before nontrivial work.
  - Produces: a durable repo map or onboarding summary.
  - Path: `skills/planning/repo-onboarding`

- `task-breakdown`
  - Use when: you need to turn a vague or multi-step technical request into an evidence-backed plan.
  - Produces: an implementation plan with verification steps.
  - Path: `skills/planning/task-breakdown`

## Debugging

- `diagnose-bug`
  - Use when: you have a reproducible bug, failing test, or broken behavior that needs root-cause analysis.
  - Produces: a diagnosis, rejected hypotheses, minimal fix plan, and regression proof.
  - Path: `skills/debugging/diagnose-bug`

## Review

- `code-review`
  - Use when: you need to review a PR, branch, or diff.
  - Produces: verified findings, checked-clean areas, and review verdict.
  - Path: `skills/review/code-review`

## API and Backend

- `api-design-review`
  - Use when: you need to review an API contract, route design, compatibility, pagination, idempotency, or error semantics.
  - Produces: an API design review with compatibility risks, convention checks, and required changes.
  - Path: `skills/api-and-backend/api-design-review`

- `service-debugging`
  - Use when: a deployed service, worker, job, or endpoint is failing and needs evidence-driven diagnosis.
  - Produces: a service failure diagnosis, evidence requests, likely causes, and safe next actions.
  - Path: `skills/api-and-backend/service-debugging`

- `auth-flow-review`
  - Use when: you need to verify authentication, authorization, route guards, object-level access, or IDOR risk.
  - Produces: an access-control review with route inventory, findings, and regression tests.
  - Path: `skills/api-and-backend/auth-flow-review`

- `background-job-queue-review`
  - Use when: a background job or message-queue workflow needs reliability review, or jobs are lost, duplicated, stuck, or reprocessed.
  - Produces: a reliability findings report covering delivery semantics, retries, dead-lettering, idempotent consumers, ordering, and backlog behavior.
  - Path: `skills/api-and-backend/background-job-queue-review`

- `webhook-reliability-review`
  - Use when: webhook sending or receiving needs review, or webhooks are missed, duplicated, out of order, or failing signature checks.
  - Produces: a webhook reliability report covering signature verification, replay protection, retries, idempotent receipt, and delivery visibility.
  - Path: `skills/api-and-backend/webhook-reliability-review`

- `idempotency-and-retry-safety-review`
  - Use when: you need to know which write operations are safe to retry or receive twice, or duplicates (double charges, double emails, duplicate rows) are reported.
  - Produces: a per-operation retry-safety matrix with the exact idempotency mechanism or gap for each.
  - Path: `skills/api-and-backend/idempotency-and-retry-safety-review`

- `rate-limiting-and-backpressure-review`
  - Use when: a service needs overload protection review, or it falls over under spikes or a slow dependency.
  - Produces: an overload-protection report covering inbound limits, outbound backpressure, load shedding, and graceful degradation.
  - Path: `skills/api-and-backend/rate-limiting-and-backpressure-review`

## Frontend

- `component-design-review`
  - Use when: you need to review a reusable UI component's API, state ownership, styling hooks, accessibility hooks, or callers.
  - Produces: a component design review with caller evidence, contract risks, and required changes.
  - Path: `skills/frontend/component-design-review`

- `frontend-state-management`
  - Use when: UI state is duplicated, stale, hard to reason about, or split across URL, local, global, cache, form, or derived state.
  - Produces: a state inventory, source-of-truth plan, and verification steps.
  - Path: `skills/frontend/frontend-state-management`

- `form-validation-review`
  - Use when: form validation, server errors, async checks, submission UX, or reset behavior needs review.
  - Produces: a validation review covering client/server rules, error mapping, races, and user-visible states.
  - Path: `skills/frontend/form-validation-review`

- `accessibility-review`
  - Use when: keyboard, focus, semantics, labels, ARIA, announcements, contrast, or screen-reader behavior needs audit.
  - Produces: an accessibility findings report backed by rendered or requested evidence.
  - Path: `skills/frontend/accessibility-review`

- `rtl-ui-review`
  - Use when: an interface supports RTL locales or mixed-direction text, numbers, URLs, forms, charts, or tables.
  - Produces: a prioritized RTL findings report with visual evidence requirements.
  - Path: `skills/frontend/rtl-ui-review`

- `frontend-performance-review`
  - Use when: a route is slow, janky, repeatedly fetching, hydration-heavy, render-heavy, or bundle-sensitive.
  - Produces: a performance diagnosis with measured bottleneck classification and re-measurement steps.
  - Path: `skills/frontend/frontend-performance-review`

- `api-integration-debugging`
  - Use when: frontend behavior disagrees with backend/API evidence and the boundary needs diagnosis.
  - Produces: an integration diagnosis covering request, response, auth, CORS, parsing, cache, and UI state.
  - Path: `skills/frontend/api-integration-debugging`

## Data

- `db-to-dwh-pipeline`
  - Use when: operational database data must be replicated, reconciled, backfilled, or repaired in a warehouse.
  - Produces: a pipeline diagnosis or design with watermark, delete, drift, reconciliation, and backfill checks.
  - Path: `skills/data/db-to-dwh-pipeline`

- `dataform-bigquery-development`
  - Use when: Dataform on BigQuery models, SQLX, incrementals, assertions, dependencies, partitioning, or cost need work.
  - Produces: a Dataform/BigQuery implementation plan or fix with idempotency and cost verification.
  - Path: `skills/data/dataform-bigquery-development`

- `data-quality-contracts`
  - Use when: dataset schema, grain, freshness, volume, reconciliation, or business-rule expectations need enforcement.
  - Produces: data quality contracts, checks, alerting expectations, and ownership notes.
  - Path: `skills/data/data-quality-contracts`

- `data-freshness-investigation`
  - Use when: a warehouse table, model, or dashboard is stale, lagging its SLA, or stopped updating.
  - Produces: a freshness diagnosis pinning the broken pipeline stage with evidence, plus remediation and backfill recommendations.
  - Path: `skills/data/data-freshness-investigation`

- `data-reconciliation-investigation`
  - Use when: two datasets that should agree don't — a warehouse metric vs. the source, two reports, or a model output vs. a hand count.
  - Produces: a reconciliation diagnosis pinning the divergence stage with row-level evidence and a correction plan.
  - Path: `skills/data/data-reconciliation-investigation`

- `backfill-planning-and-verification`
  - Use when: historical rows or partitions must be recomputed or reloaded after a logic fix, new column, late data, or corrected source.
  - Produces: a backfill plan with scope, batching, idempotency proof, cost/load estimate, validation strategy, and rollback path.
  - Path: `skills/data/backfill-planning-and-verification`

- `data-pipeline-cost-review`
  - Use when: a warehouse bill spikes or grows and you need to find and cut the cost drivers without losing correctness or freshness.
  - Produces: a cost-attribution report with top drivers, per-fix savings estimates, and correctness/freshness risk notes.
  - Path: `skills/data/data-pipeline-cost-review`

## DevOps and Infrastructure

- `ci-cd-debugging`
  - Use when: a CI/CD workflow, job, cache, dependency, artifact, secret, token, or deploy trigger is failing.
  - Produces: a CI/CD failure diagnosis, evidence table, minimal fix plan, and verification plan.
  - Path: `skills/devops-infrastructure/ci-cd-debugging`

- `deployment-debugging`
  - Use when: a deployment succeeded or partly succeeded but the runtime service, job, worker, or endpoint is broken.
  - Produces: a deployment diagnosis with release evidence, environment checks, and safe next actions.
  - Path: `skills/devops-infrastructure/deployment-debugging`

- `terraform-plan-review`
  - Use when: Terraform or OpenTofu plan output needs review before apply, especially for production/shared infrastructure.
  - Produces: a plan risk review with high-risk changes, pre-apply checks, and rollback/state notes.
  - Path: `skills/devops-infrastructure/terraform-plan-review`

- `iam-permissions-debugging`
  - Use when: access denied, forbidden, token scope, service account, impersonation, RBAC, or permission failures need diagnosis.
  - Produces: a principal/action/resource matrix, least-privilege fix, verification command, and risk notes.
  - Path: `skills/devops-infrastructure/iam-permissions-debugging`

- `kubernetes-workload-debugging`
  - Use when: Kubernetes workloads are Pending, CrashLoopBackOff, not Ready, misrouted, or behaving differently inside the cluster.
  - Produces: a Kubernetes workload diagnosis, evidence table, safe fix, verification commands, and rollback notes.
  - Path: `skills/devops-infrastructure/kubernetes-workload-debugging`

- `incident-triage-runbook`
  - Use when: an active or recent incident needs severity, impact, ownership, communication, mitigation, and follow-up structure.
  - Produces: an incident triage runbook with current facts, unknowns, mitigation, owners, and follow-up items.
  - Path: `skills/devops-infrastructure/incident-triage-runbook`

- `release-and-rollback-readiness`
  - Use when: a release needs go/no-go readiness, rollback checks, monitoring coverage, ownership, and post-release verification.
  - Produces: a release readiness report with risks, required checks, rollback notes, and recommendation.
  - Path: `skills/devops-infrastructure/release-and-rollback-readiness`

## Architecture

- `architecture-review`
  - Use when: a system design, major technical change, subsystem structure, or architecture proposal needs review.
  - Produces: an architecture review report with strengths, risks, open questions, recommended changes, and tradeoff summary.
  - Path: `skills/architecture/architecture-review`

- `architecture-decision-record`
  - Use when: a meaningful technical decision needs to be documented or updated as an ADR.
  - Produces: an ADR draft or update with context, options, decision, consequences, revisit trigger, and evidence links.
  - Path: `skills/architecture/architecture-decision-record`

- `module-boundary-review`
  - Use when: modules, packages, services, layers, or components need responsibility and boundary review.
  - Produces: a boundary review with unclear responsibilities, violations, proposed changes, and migration steps.
  - Path: `skills/architecture/module-boundary-review`

- `dependency-and-coupling-review`
  - Use when: dependency direction, circular dependencies, shared utilities, package boundaries, or architectural erosion need review.
  - Produces: a coupling map, high-risk dependencies, decoupling strategy, and verification plan.
  - Path: `skills/architecture/dependency-and-coupling-review`

- `technical-debt-triage`
  - Use when: many technical debt items need evidence-backed prioritization.
  - Produces: a prioritized debt list, impact/risk scoring, defer/ignore list, and suggested tracking issues.
  - Path: `skills/architecture/technical-debt-triage`

- `refactor-strategy`
  - Use when: a nontrivial refactor needs behavior-preserving migration planning.
  - Produces: a safe refactor plan with characterization tests, migration slices, compatibility, rollback, and cleanup plan.
  - Path: `skills/architecture/refactor-strategy`

## Testing

- `test-strategy`
  - Use when: a feature, service, migration, repo, or subsystem needs risk-based testing coverage.
  - Produces: a test strategy, risk matrix, recommended layers, cases to add, tests to defer, and verification plan.
  - Path: `skills/testing/test-strategy`

- `tdd-workflow`
  - Use when: a change should be implemented through a test-first red/green/refactor loop.
  - Produces: a TDD implementation plan, first failing test, minimal implementation step, refactor notes, and verification summary.
  - Path: `skills/testing/tdd-workflow`

- `legacy-characterization-tests`
  - Use when: legacy or poorly tested code needs current behavior captured before refactor or risky change.
  - Produces: a characterization test plan, behavior inventory, safest seams, pending unknowns, and recommended first tests.
  - Path: `skills/testing/legacy-characterization-tests`

- `integration-test-design`
  - Use when: tests need to cross boundaries such as API+DB, service+queue, frontend+API mock, or data pipeline stages.
  - Produces: an integration test design, dependency strategy, fixture/data plan, reliability risks, and verification commands.
  - Path: `skills/testing/integration-test-design`

- `e2e-test-debugging`
  - Use when: an end-to-end test fails, flakes, or gives unclear browser/runtime evidence.
  - Produces: an E2E failure diagnosis, evidence table, root-cause classification, ruled-out causes, fix or stabilization plan, and verification command.
  - Path: `skills/testing/e2e-test-debugging`

- `flaky-test-debugging`
  - Use when: a test passes sometimes and fails sometimes locally or in CI.
  - Produces: a flaky test diagnosis, evidence table, suspected and rejected causes, stabilization plan, and verification loop.
  - Path: `skills/testing/flaky-test-debugging`

## Documentation

- `readme-review`
  - Use when: a project README needs onboarding, setup, usage, contribution, maintenance, or stale-claim review.
  - Produces: a README review, missing sections, stale or unverified claims, suggested structure, and patch plan.
  - Path: `skills/documentation/readme-review`

- `api-docs-review`
  - Use when: API docs, OpenAPI specs, endpoint references, request/response examples, or SDK docs need accuracy review.
  - Produces: an API documentation review, missing or incorrect docs, example fixes, and contract/documentation mismatch report.
  - Path: `skills/documentation/api-docs-review`

- `runbook-writer`
  - Use when: an operational runbook for incidents, alerts, deployments, jobs, or recurring procedures needs drafting or review.
  - Produces: a runbook draft or review with evidence commands, step-by-step procedure, escalation path, validation, and rollback steps.
  - Path: `skills/documentation/runbook-writer`

- `handoff-documentation`
  - Use when: completed or paused work needs to be summarized so another person or agent can continue.
  - Produces: a handoff note, continuation checklist, unresolved questions, and next-step recommendation.
  - Path: `skills/documentation/handoff-documentation`

- `changelog-writer`
  - Use when: release notes, changelog entries, or user/developer-facing summaries need to be written from actual changes.
  - Produces: a changelog entry, release note draft, breaking-change section, migration notes, and evidence summary.
  - Path: `skills/documentation/changelog-writer`

- `developer-onboarding-docs`
  - Use when: documentation should help new developers understand and contribute to a repo or system.
  - Produces: an onboarding doc outline or draft, repo map, first-day path, setup verification checklist, and gap list.
  - Path: `skills/documentation/developer-onboarding-docs`

## Security Review

- `secrets-exposure-review`
  - Use when: code, config, logs, commits, CI output, containers, or docs may expose credentials, tokens, API keys, certificates, or private material.
  - Produces: exposure findings, severity assessment, containment checklist, and rotation/prevention plan.
  - Path: `skills/security-review/secrets-exposure-review`

- `dependency-vulnerability-triage`
  - Use when: a dependency scanner, CVE, advisory, or audit report flags a vulnerable package in use.
  - Produces: a risk-ranked triage with reachability assessment, upgrade/mitigation plan, and verification steps.
  - Path: `skills/security-review/dependency-vulnerability-triage`

- `input-validation-security-review`
  - Use when: untrusted input handling needs review for injection, unsafe parsing, path traversal, SSRF-style forwarding, or upload risks.
  - Produces: an input surface map, trust boundary findings, and safe remediation plan.
  - Path: `skills/security-review/input-validation-security-review`

- `access-control-boundary-review`
  - Use when: service-to-service permissions, resource authorization, IAM boundaries, or tenant isolation need review.
  - Produces: a privilege map, boundary findings, and remediation plan.
  - Path: `skills/security-review/access-control-boundary-review`

- `security-incident-scoping`
  - Use when: a suspected compromise, credential exposure, or suspicious activity needs initial scoping and containment planning.
  - Produces: an incident scope summary, evidence preservation checklist, containment options, and escalation path.
  - Path: `skills/security-review/security-incident-scoping`

## Observability

- `metrics-and-alerts-review`
  - Use when: alert coverage, noise, missing alerts, or paging quality need review.
  - Produces: an alert inventory, signal/noise findings, missing coverage, and threshold recommendations.
  - Path: `skills/observability/metrics-and-alerts-review`

- `log-signal-quality-review`
  - Use when: logs are too noisy, sparse, inconsistent, or missing correlation context.
  - Produces: a logging quality review, correlation/context gaps, and recommended log changes.
  - Path: `skills/observability/log-signal-quality-review`

- `trace-based-latency-diagnosis`
  - Use when: distributed traces or timing breakdowns exist for a slow request or workflow.
  - Produces: a trace evidence table, latency breakdown, and bottleneck hypothesis.
  - Path: `skills/observability/trace-based-latency-diagnosis`

- `slo-definition-review`
  - Use when: SLIs, SLOs, error budgets, or reliability targets need to be defined or reviewed.
  - Produces: proposed SLIs/SLOs, threshold rationale, and error-budget policy notes.
  - Path: `skills/observability/slo-definition-review`

- `dashboard-signal-audit`
  - Use when: an operational dashboard needs review for actionable signal or incident usefulness.
  - Produces: a dashboard signal audit, actionable-vs-vanity findings, and recommended dashboard changes.
  - Path: `skills/observability/dashboard-signal-audit`

## Database

- `query-performance-review`
  - Use when: a database query is slow, expensive, unstable, or timing out.
  - Produces: a query performance review, evidence table, likely bottleneck, and recommended query/index/schema changes.
  - Path: `skills/database/query-performance-review`

- `schema-migration-safety-review`
  - Use when: a schema migration touches live tables, columns, constraints, indexes, or backfills.
  - Produces: a migration safety review, risk list, safe rollout plan, and rollback/roll-forward plan.
  - Path: `skills/database/schema-migration-safety-review`

- `index-strategy-review`
  - Use when: indexes need to be checked against query patterns, cardinality, or write cost.
  - Produces: an index strategy review, recommended and rejected indexes, and a verification plan.
  - Path: `skills/database/index-strategy-review`

- `data-consistency-incident-diagnosis`
  - Use when: data is duplicated, missing, stale, or contradictory across tables, services, caches, or queues.
  - Produces: a data consistency incident diagnosis, suspected cause, affected scope, and safe reconciliation plan.
  - Path: `skills/database/data-consistency-incident-diagnosis`

- `connection-pool-and-locking-debugging`
  - Use when: there are DB timeouts, deadlocks, pool exhaustion, or lock contention.
  - Produces: a connection/locking diagnosis, likely root cause, and safe mitigation options.
  - Path: `skills/database/connection-pool-and-locking-debugging`

- `backup-restore-readiness`
  - Use when: backup coverage, restore procedures, or disaster-recovery readiness need review.
  - Produces: a backup/restore readiness review, gaps, risk rating, and restore test plan.
  - Path: `skills/database/backup-restore-readiness`

## Performance

- `hot-path-profiling-review`
  - Use when: a backend hot path is measurably slow and profiling evidence exists or is needed.
  - Produces: a hot path profiling review, bottleneck evidence, optimization candidates, and verification plan.
  - Path: `skills/performance/hot-path-profiling-review`

- `memory-leak-diagnosis`
  - Use when: memory grows unexpectedly, GC pressure is high, or a process is OOM-killed.
  - Produces: a memory leak diagnosis, suspected retention path, ruled-out causes, and mitigation plan.
  - Path: `skills/performance/memory-leak-diagnosis`

- `throughput-bottleneck-triage`
  - Use when: a service can't meet target QPS/throughput even after scaling workers.
  - Produces: a throughput bottleneck triage, bottleneck hypothesis, evidence table, and safe experiments.
  - Path: `skills/performance/throughput-bottleneck-triage`

- `caching-strategy-review`
  - Use when: deciding whether to add, remove, or change caching for performance or consistency reasons.
  - Produces: a caching strategy review, cache fit assessment, risks/tradeoffs, and invalidation plan.
  - Path: `skills/performance/caching-strategy-review`

- `load-test-result-review`
  - Use when: load test results need review before a launch or scaling decision.
  - Produces: a load test result review, validity concerns, bottleneck findings, and go/no-go recommendation.
  - Path: `skills/performance/load-test-result-review`

## Optional Bundles

If you prefer role-based recommendations, start here:

- Backend Developer: [bundles/backend-developer.md](bundles/backend-developer.md)
- Frontend Developer: [bundles/frontend-developer.md](bundles/frontend-developer.md)
- Data Engineer: [bundles/data-engineer.md](bundles/data-engineer.md)
- DevOps / Cloud Engineer: [bundles/devops-cloud-engineer.md](bundles/devops-cloud-engineer.md)
