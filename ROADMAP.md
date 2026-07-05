# Roadmap

This roadmap describes the public scope, stability policy, and expansion rules for `engineering-agent-skills`.

The repository is intentionally workflow-first: each skill should map to a real engineering task, produce a useful artifact, and make an agent gather evidence before drawing conclusions.

## Current Stable Scope

The current public scope is stable:

- 71 skills
- 14 approved domains
- 4 optional role bundles
- one reusable skill template

`README.md` is the entry point, `SKILL_CATALOG.md` is the exhaustive task-based discovery surface, `.claude/skills/` contains the workflows, and `bundles/` contains optional role-based starting sets. Domain grouping (below) is conceptual and represented in `SKILL_CATALOG.md` and this file, not by nested folders under `.claude/skills/`.

## Approved Domains

These domains are approved and stable:

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

Approved domains may still receive bug fixes, clarity improvements, routing fixes, and narrowly scoped quality improvements. They should not be broadly rewritten without a concrete reason.

## Optional Bundles

Bundles are optional recommendations for users who prefer a role-based starting point:

- Backend Developer
- Frontend Developer
- Data Engineer
- DevOps / Cloud Engineer

Bundles should stay small and practical. They should point users to existing skills rather than becoming separate role manuals.

## Deferred Areas

Some areas may be revisited later, but they are not part of the current stable scope:

- code-quality
- ai-engineering
- product
- collaboration
- standalone delivery workflows
- platform-engineering
- cloud-architecture
- data-analysis

Deferred areas should only be added when they can be expressed as concrete, non-duplicate workflows that fit the existing domain model.

## Expansion Principles

- One skill solves one real workflow.
- Skills are task-first, not role-first.
- Every skill must produce a concrete artifact.
- Every skill must require evidence from files, commands, logs, traces, tests, plans, queries, browser checks, or user-provided artifacts.
- New skills must avoid duplicating existing skills; use explicit handoffs when workflows touch.
- `SKILL_CATALOG.md` remains the exhaustive discovery surface.
- `README.md` remains short and task-oriented.
- `ROADMAP.md` remains the public status and change-policy document.

## Contribution And Change Rules

Before adding or changing a skill:

1. Check `SKILL_CATALOG.md` for overlap.
2. Confirm the proposed change is a workflow, not a broad topic.
3. Keep the main `SKILL.md` short enough to follow at runtime.
4. Put detailed quality gates in `references/checklist.md`.
5. Put recurring failure modes in `references/anti-patterns.md`.
6. Include or update `examples/sample-task.md` when behavior changes.
7. Validate routing and expected behavior with `EVALUATION.md` where relevant.

New domains or large expansions should start conservatively and should not update role bundles until their workflows are stable.

## Stability Policy

The public repository surfaces are stable:

- `README.md`
- `SKILL_CATALOG.md`
- `ROADMAP.md`
- `EVALUATION.md`
- `templates/`
- approved skill bodies

Stable does not mean frozen. Maintenance is welcome when it fixes stale information, improves routing clarity, corrects behavior, strengthens validation, or keeps the repository easier to use.

Approved skill bodies should only change for targeted, evidence-backed reasons. Documentation-only improvements should preserve the workflow/domain-first structure.
