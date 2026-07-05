# engineering-agent-skills

Workflow-based Agent Skills for engineering work.

Use these skills when you want an AI agent to follow a proven workflow instead of guessing.

Do not install everything. Start with the workflow you need.

## Find a Skill by Task

| I need to... | Start here |
|---|---|
| Understand a repository | [.claude/skills/repo-onboarding](.claude/skills/repo-onboarding) |
| Break down technical work | [.claude/skills/task-breakdown](.claude/skills/task-breakdown) |
| Debug a bug | [.claude/skills/diagnose-bug](.claude/skills/diagnose-bug) |
| Review code | [.claude/skills/code-review](.claude/skills/code-review) |
| Design or review an API | [.claude/skills/api-design-review](.claude/skills/api-design-review) |
| Debug a deployed service | [.claude/skills/service-debugging](.claude/skills/service-debugging) |
| Review auth or authorization | [.claude/skills/auth-flow-review](.claude/skills/auth-flow-review) |
| Review a queue, webhook, or retry-safety concern | [.claude/skills/idempotency-and-retry-safety-review](.claude/skills/idempotency-and-retry-safety-review) |
| Review overload protection and rate limiting | [.claude/skills/rate-limiting-and-backpressure-review](.claude/skills/rate-limiting-and-backpressure-review) |
| Debug frontend/API integration | [.claude/skills/api-integration-debugging](.claude/skills/api-integration-debugging) |
| Review frontend state | [.claude/skills/frontend-state-management](.claude/skills/frontend-state-management) |
| Review data quality | [.claude/skills/data-quality-contracts](.claude/skills/data-quality-contracts) |
| Investigate stale or mismatched data | [.claude/skills/data-reconciliation-investigation](.claude/skills/data-reconciliation-investigation) |
| Plan a data backfill | [.claude/skills/backfill-planning-and-verification](.claude/skills/backfill-planning-and-verification) |
| Review a Terraform plan | [.claude/skills/terraform-plan-review](.claude/skills/terraform-plan-review) |
| Debug IAM permissions | [.claude/skills/iam-permissions-debugging](.claude/skills/iam-permissions-debugging) |
| Debug Kubernetes workloads | [.claude/skills/kubernetes-workload-debugging](.claude/skills/kubernetes-workload-debugging) |
| Review a system design or major technical change | [.claude/skills/architecture-review](.claude/skills/architecture-review) |
| Write or update an ADR | [.claude/skills/architecture-decision-record](.claude/skills/architecture-decision-record) |
| Review module boundaries or coupling | [.claude/skills/module-boundary-review](.claude/skills/module-boundary-review) |
| Plan a safe refactor | [.claude/skills/refactor-strategy](.claude/skills/refactor-strategy) |
| Plan test coverage | [.claude/skills/test-strategy](.claude/skills/test-strategy) |
| Debug a flaky test | [.claude/skills/flaky-test-debugging](.claude/skills/flaky-test-debugging) |
| Debug an E2E test | [.claude/skills/e2e-test-debugging](.claude/skills/e2e-test-debugging) |
| Review or improve a README | [.claude/skills/readme-review](.claude/skills/readme-review) |
| Write a runbook | [.claude/skills/runbook-writer](.claude/skills/runbook-writer) |
| Write a changelog | [.claude/skills/changelog-writer](.claude/skills/changelog-writer) |
| Review possible secrets exposure | [.claude/skills/secrets-exposure-review](.claude/skills/secrets-exposure-review) |
| Triage dependency vulnerabilities | [.claude/skills/dependency-vulnerability-triage](.claude/skills/dependency-vulnerability-triage) |
| Review alerts and metrics | [.claude/skills/metrics-and-alerts-review](.claude/skills/metrics-and-alerts-review) |
| Diagnose latency from traces | [.claude/skills/trace-based-latency-diagnosis](.claude/skills/trace-based-latency-diagnosis) |
| Review a slow database query | [.claude/skills/query-performance-review](.claude/skills/query-performance-review) |
| Review a risky schema migration | [.claude/skills/schema-migration-safety-review](.claude/skills/schema-migration-safety-review) |
| Debug DB locks, deadlocks, or pool exhaustion | [.claude/skills/connection-pool-and-locking-debugging](.claude/skills/connection-pool-and-locking-debugging) |
| Review profiling evidence for a slow backend path | [.claude/skills/hot-path-profiling-review](.claude/skills/hot-path-profiling-review) |
| Review load test results | [.claude/skills/load-test-result-review](.claude/skills/load-test-result-review) |

## Skill Catalog

See [SKILL_CATALOG.md](SKILL_CATALOG.md) for the full task-oriented catalog.

## Optional Role Bundles

Bundles are optional starting sets for people who prefer role-based recommendations.

- [Backend Developer](bundles/backend-developer.md)
- [Frontend Developer](bundles/frontend-developer.md)
- [Data Engineer](bundles/data-engineer.md)
- [DevOps / Cloud Engineer](bundles/devops-cloud-engineer.md)

## How to Install and Use

This repository already contains a Claude-compatible `.claude/skills/` directory. Do not copy the repository root into `.claude/skills`; copy the skill folders inside `.claude/skills/`.

### End-to-end: add all skills to a project

```bash
git clone https://github.com/NirGeron98/engineering-agent-skills.git /tmp/engineering-agent-skills
mkdir -p /path/to/project/.claude/skills
cp -R /tmp/engineering-agent-skills/.claude/skills/* /path/to/project/.claude/skills/
rm -rf /tmp/engineering-agent-skills
```

### End-to-end: add one skill to a project

```bash
git clone https://github.com/NirGeron98/engineering-agent-skills.git /tmp/engineering-agent-skills
mkdir -p /path/to/project/.claude/skills
cp -R /tmp/engineering-agent-skills/.claude/skills/diagnose-bug /path/to/project/.claude/skills/
rm -rf /tmp/engineering-agent-skills
```

### End-to-end: add all skills globally (all projects)

```bash
git clone https://github.com/NirGeron98/engineering-agent-skills.git /tmp/engineering-agent-skills
mkdir -p ~/.claude/skills
cp -R /tmp/engineering-agent-skills/.claude/skills/* ~/.claude/skills/
rm -rf /tmp/engineering-agent-skills
```

If you already have this repository cloned locally, skip the `git clone`/`rm -rf` steps and run the `mkdir` + `cp -R` commands from inside the repo root, using `.claude/skills/...` as the source instead of the `/tmp` clone path.

Claude loads a skill automatically when the task matches its description, or explicitly via `/skill-name`.

**Cursor / Codex / other agents**: reference the `SKILL.md` file in your prompt or rules file, for example: *"Follow the workflow in .claude/skills/diagnose-bug/SKILL.md."* The skills are plain Markdown and tool-agnostic; only the frontmatter convention is Claude-specific.

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
.claude/skills/            All 71 skill folders, installable as-is by Claude Code
  <skill-name>/
    SKILL.md               Workflow: goal, triggers, steps, output format, failure modes
    references/checklist.md
    references/anti-patterns.md
    examples/sample-task.md
bundles/                   Optional role-based starting sets
templates/                 Starting point for authoring new skills
SKILL_CATALOG.md           Exhaustive task-based catalog, grouped by domain
EVALUATION.md              Routing and behavior scenarios
ROADMAP.md                 Current scope and change policy
```

Skills are grouped by domain (planning, debugging, review, api-and-backend, frontend, data, devops-infrastructure, architecture, testing, documentation, security-review, observability, database, performance) for discovery purposes only, in [SKILL_CATALOG.md](SKILL_CATALOG.md) and [ROADMAP.md](ROADMAP.md). Domains are not physical folders — every skill lives directly under `.claude/skills/<skill-name>/`.

Each skill folder contains four files with strictly separated jobs:

| File | Job | When it's read |
|---|---|---|
| `SKILL.md` | The workflow: goal, triggers, numbered steps, output format, plus the 2-3 failure modes that must shape in-flight behavior | Loaded when the description matches the task |
| `references/checklist.md` | The binary quality gate: done/not-done items phrased as evidence, grouped by stage | Explicitly loaded by SKILL.md before the handoff |
| `references/anti-patterns.md` | Named failure modes: symptom, why it fails, correction | Explicitly loaded by SKILL.md when a step is ambiguous or risky |
| `examples/sample-task.md` | A worked example: prompt, abbreviated run, expected output, and at least one obstacle handled honestly | For humans evaluating or authoring; not loaded at runtime |

Skills that touch live systems carry an evidence access rule: when the agent cannot run a query, command, browser check, log search, cloud command, CI query, Terraform plan, or `kubectl` inspection itself, it outputs the exact command for the user to run and paste back, and marks dependent conclusions as pending.

## Contributing

Contributions should keep this repository workflow-first and evidence-first. Before proposing a new skill, check [SKILL_CATALOG.md](SKILL_CATALOG.md) for existing coverage and use [templates/skill-template/](templates/skill-template/) for the required structure.

See [CONTRIBUTING.md](CONTRIBUTING.md) for contribution rules and validation expectations.

## Evaluation

See [EVALUATION.md](EVALUATION.md): realistic scenarios with expected routing, acceptable secondary skills, expected behavior, bad behavior, and pass/fail criteria. The scenarios check both routing and behavior delta, including whether skills load `references/checklist.md` before handoff.

## Status

This is a workflow-based repository: 71 skills across fourteen task/domain categories, with four optional role bundles. See [ROADMAP.md](ROADMAP.md) for current scope and change policy.
