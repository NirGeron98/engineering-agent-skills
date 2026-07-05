# Contributing

Thanks for helping improve `engineering-agent-skills`.

This repository is a workflow-first Agent Skills library for practical software engineering work. Contributions should make an agent follow a clearer, more reliable engineering workflow. They should not add persona prompts, role manuals, broad advice, or generic prompt collections.

## Contribution Principles

- One skill represents one concrete engineering workflow.
- Skills are workflow-first, not role-first.
- Every skill must be evidence-first: the agent should gather files, commands, logs, traces, tests, plans, queries, browser checks, or user-provided artifacts before drawing conclusions.
- Every skill must produce a useful artifact that another person or agent can act on.
- New skills must avoid duplicating existing workflows. Use explicit handoffs when workflows are adjacent.
- Approved skills should not be broadly rewritten. Keep changes targeted and evidence-backed.

## Skill Structure

Each skill must use the required four-file structure:

```text
.claude/skills/<skill-name>/
  SKILL.md
  references/checklist.md
  references/anti-patterns.md
  examples/sample-task.md
```

- `SKILL.md`: the runtime workflow, routing description, steps, quality bar, expected output, and key failure modes.
- `references/checklist.md`: observable done/not-done quality gates the agent loads before handoff.
- `references/anti-patterns.md`: named failure modes with symptoms and corrections.
- `examples/sample-task.md`: a worked example for humans evaluating or authoring the skill.

Start from [templates/skill-template/](templates/skill-template/) when proposing or drafting a skill.

## New Skills And Domains

Before proposing a new skill:

1. Search [SKILL_CATALOG.md](SKILL_CATALOG.md) for similar workflows.
2. Confirm the proposal is a concrete workflow, not a broad topic.
3. Identify the domain it belongs to.
4. Describe the required evidence and final artifact.
5. Explain why an existing skill cannot cover it with a handoff or small update.

New domains or skills should start as Draft / Needs Audit unless explicitly approved. Role bundles should not be updated for draft workflows.

## Public Documentation

- `README.md` should remain short and task-oriented.
- `SKILL_CATALOG.md` should remain the exhaustive discovery surface.
- `ROADMAP.md` should describe public scope, stability, and change policy.
- `EVALUATION.md` should remain useful for validating routing and behavior.

If a change affects routing, public scope, counts, or validation expectations, update the relevant public document in the same pull request.

## Validation Checklist

Before opening a pull request, check:

- [ ] The change is workflow-first and evidence-first.
- [ ] No existing skill already covers the same workflow.
- [ ] New or changed skills keep the required four-file structure.
- [ ] Approved skill bodies were not broadly rewritten.
- [ ] `README.md`, `SKILL_CATALOG.md`, `ROADMAP.md`, and `EVALUATION.md` are still accurate where relevant.
- [ ] Skill counts, domain counts, and bundle counts remain correct where mentioned.
- [ ] Links and paths in changed Markdown files are valid.
- [ ] Any validation performed is listed in the pull request.
