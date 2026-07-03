# my-agent-skills

Workflow-based Agent Skills for engineering work.

Use these skills when you want an AI agent to follow a proven workflow instead of guessing.

Do not install everything. Start with the workflow you need.

## Find a Skill by Task

| I need to... | Start here |
|---|---|
| Understand a repository | [skills/planning/repo-onboarding](skills/planning/repo-onboarding) |
| Break down technical work | [skills/planning/task-breakdown](skills/planning/task-breakdown) |
| Debug a bug | [skills/debugging/diagnose-bug](skills/debugging/diagnose-bug) |
| Review code | [skills/review/code-review](skills/review/code-review) |
| Design or review an API | [skills/api-and-backend/api-design-review](skills/api-and-backend/api-design-review) |
| Debug a deployed service | [skills/api-and-backend/service-debugging](skills/api-and-backend/service-debugging) |
| Review auth or authorization | [skills/api-and-backend/auth-flow-review](skills/api-and-backend/auth-flow-review) |
| Debug frontend/API integration | [skills/frontend/api-integration-debugging](skills/frontend/api-integration-debugging) |
| Review frontend state | [skills/frontend/frontend-state-management](skills/frontend/frontend-state-management) |
| Review data quality | [skills/data/data-quality-contracts](skills/data/data-quality-contracts) |
| Review a Terraform plan | [skills/devops-infrastructure/terraform-plan-review](skills/devops-infrastructure/terraform-plan-review) |
| Debug IAM permissions | [skills/devops-infrastructure/iam-permissions-debugging](skills/devops-infrastructure/iam-permissions-debugging) |
| Debug Kubernetes workloads | [skills/devops-infrastructure/kubernetes-workload-debugging](skills/devops-infrastructure/kubernetes-workload-debugging) |
| Review a system design or major technical change | [skills/architecture/architecture-review](skills/architecture/architecture-review) |
| Write or update an ADR | [skills/architecture/architecture-decision-record](skills/architecture/architecture-decision-record) |
| Review module boundaries or coupling | [skills/architecture/module-boundary-review](skills/architecture/module-boundary-review) |
| Plan a safe refactor | [skills/architecture/refactor-strategy](skills/architecture/refactor-strategy) |
| Plan test coverage | [skills/testing/test-strategy](skills/testing/test-strategy) |
| Debug a flaky test | [skills/testing/flaky-test-debugging](skills/testing/flaky-test-debugging) |
| Debug an E2E test | [skills/testing/e2e-test-debugging](skills/testing/e2e-test-debugging) |
| Review or improve a README | [skills/documentation/readme-review](skills/documentation/readme-review) |
| Write a runbook | [skills/documentation/runbook-writer](skills/documentation/runbook-writer) |
| Write a changelog | [skills/documentation/changelog-writer](skills/documentation/changelog-writer) |
| Review possible secrets exposure | [skills/security-review/secrets-exposure-review](skills/security-review/secrets-exposure-review) |
| Triage dependency vulnerabilities | [skills/security-review/dependency-vulnerability-triage](skills/security-review/dependency-vulnerability-triage) |
| Review alerts and metrics | [skills/observability/metrics-and-alerts-review](skills/observability/metrics-and-alerts-review) |
| Diagnose latency from traces | [skills/observability/trace-based-latency-diagnosis](skills/observability/trace-based-latency-diagnosis) |
| Review a slow database query | [skills/database/query-performance-review](skills/database/query-performance-review) |
| Review a risky schema migration | [skills/database/schema-migration-safety-review](skills/database/schema-migration-safety-review) |
| Debug DB locks, deadlocks, or pool exhaustion | [skills/database/connection-pool-and-locking-debugging](skills/database/connection-pool-and-locking-debugging) |
| Review profiling evidence for a slow backend path | [skills/performance/hot-path-profiling-review](skills/performance/hot-path-profiling-review) |
| Review load test results | [skills/performance/load-test-result-review](skills/performance/load-test-result-review) |

## Skill Catalog

See [SKILL_CATALOG.md](SKILL_CATALOG.md) for the full task-oriented catalog.

## Optional Role Bundles

Bundles are optional starting sets for people who prefer role-based recommendations.

- [Backend Developer](bundles/backend-developer.md)
- [Frontend Developer](bundles/frontend-developer.md)
- [Data Engineer](bundles/data-engineer.md)
- [DevOps / Cloud Engineer](bundles/devops-cloud-engineer.md)

## How to Install and Use

**Claude Code**: copy the skill folders you need into `.claude/skills/` in your project, or `~/.claude/skills/` for all projects. Claude loads a skill automatically when the task matches its description, or explicitly via `/skill-name`.

**Cursor / Codex / other agents**: reference the `SKILL.md` file in your prompt or rules file, for example: *"Follow the workflow in skills/debugging/diagnose-bug/SKILL.md."* The skills are plain Markdown and tool-agnostic; only the frontmatter convention is Claude-specific.

**Composing skills**: skills reference each other by name where handoffs are natural. For example, `api-integration-debugging` can hand a proven server-side issue to `service-debugging`, and `terraform-plan-review` can hand a permission root cause to `iam-permissions-debugging`.

## Quality Bar

Every skill should change what the agent does, not just how the answer reads:

1. **Routing precision**: the frontmatter description alone lets the agent decide when to load it.
2. **Evidence discipline**: claims cite files, commands, browser checks, logs, traces, plans, queries, or pasted evidence.
3. **Reality fallbacks**: when the agent lacks access, it asks for exact commands or artifacts and marks dependent conclusions as pending.
4. **Followable length**: the main workflow stays short enough to follow; detail lives in checklists and anti-patterns.
5. **Useful artifact**: the run ends with a handoff a reviewer, teammate, or future agent can use.

## Repository Structure

```text
skills/
  planning/                Repo orientation and technical planning
  debugging/               Reproducible bug diagnosis
  review/                  Code review workflows
  api-and-backend/         API contracts, service debugging, auth review
  frontend/                Components, state, forms, a11y, RTL, performance, API integration
  data/                    Pipelines, warehouse modeling, data quality
  devops-infrastructure/   CI/CD, deployments, Terraform, IAM, Kubernetes, incidents, releases
  architecture/            System design, ADRs, boundaries, coupling, debt triage, refactors
  testing/                 Test strategy, TDD, characterization, integration, E2E, flaky tests
  documentation/           READMEs, API docs, runbooks, handoffs, changelogs, onboarding docs
  security-review/         Secrets exposure, dependency triage, input validation, access boundaries, incident scoping
  observability/           Alerts, log signal quality, trace-based latency, SLOs, dashboard audits
  database/                Query performance, schema migration safety, indexing, consistency, locking, backup/restore
  performance/             Hot path profiling, memory leak diagnosis, throughput triage, caching strategy, load test review
bundles/                   Optional role-based starting sets
templates/                 Starting point for authoring new skills
EVALUATION.md              Routing and behavior scenarios
ROADMAP.md                 Current status and backlog
```

Each skill folder contains four files with strictly separated jobs:

| File | Job | When it's read |
|---|---|---|
| `SKILL.md` | The workflow: goal, triggers, numbered steps, output format, plus the 2-3 failure modes that must shape in-flight behavior | Loaded when the description matches the task |
| `references/checklist.md` | The binary quality gate: done/not-done items phrased as evidence, grouped by stage | Explicitly loaded by SKILL.md before the handoff |
| `references/anti-patterns.md` | Named failure modes: symptom, why it fails, correction | Explicitly loaded by SKILL.md when a step is ambiguous or risky |
| `examples/sample-task.md` | A worked example: prompt, abbreviated run, expected output, and at least one obstacle handled honestly | For humans evaluating or authoring; not loaded at runtime |

Skills that touch live systems carry an evidence access rule: when the agent cannot run a query, command, browser check, log search, cloud command, CI query, Terraform plan, or `kubectl` inspection itself, it outputs the exact command for the user to run and paste back, and marks dependent conclusions as pending.

## Contributing And Creating Skills

Start from [templates/skill-template/](templates/skill-template/). Keep new skills narrow, evidence-driven, and routed by the frontmatter description. Do not add a skill unless it changes behavior beyond a normal prompt.

## Evaluation

See [EVALUATION.md](EVALUATION.md): realistic scenarios with expected routing, acceptable secondary skills, expected behavior, bad behavior, and pass/fail criteria. The scenarios check both routing and behavior delta, including whether skills load `references/checklist.md` before handoff.

## Status

This is a workflow-based repository: 63 skills across fourteen task/domain categories, with four optional role bundles. See [ROADMAP.md](ROADMAP.md) for domain status and expansion plans.
