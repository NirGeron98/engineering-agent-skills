---
name: dependency-and-coupling-review
description: Review dependency direction, coupling, circular dependencies, package boundaries, shared utilities, framework leakage, and architectural erosion. Use when a dependency graph, high-risk dependencies, concrete examples, decoupling strategy, and verification plan are needed; defer module responsibility questions to module-boundary-review and migration planning to refactor-strategy.
---

# Dependency and Coupling Review

## Goal

Map coupling risk from real dependency evidence and propose a decoupling direction that reduces blast radius without hiding the same dependencies behind new names.

## When to use

- Circular imports, shared utilities, dependency direction, or package boundaries are causing risk.
- Build, test, deploy, or runtime coupling makes changes hard to isolate.
- The user needs a coupling map and decoupling strategy.

## When not to use

- The main question is module responsibility; use `module-boundary-review`.
- The user already chose the target structure and needs migration sequencing; use `refactor-strategy`.
- The concern is a PR-level implementation defect; use `.claude/skills/code-review`.

## Composition and handoff rules

- Hand broader module responsibility questions to `module-boundary-review`.
- Hand safe migration planning to `refactor-strategy`.
- Hand code-level PR findings to `.claude/skills/code-review`.

## Evidence access rule

Do not infer coupling from directory names. Inspect imports, package manifests, build configs, dependency graph tools, callers, tests, deploy units, and runtime/config edges. If graph tooling is unavailable, request exact commands or a file list and mark graph conclusions pending.

## Workflow

1. Define the coupling concern and scope: packages, layers, services, build targets, or deploy units.
2. Gather evidence from import searches, dependency graph commands, manifests, build/test configs, public APIs, and representative callers.
3. Map dependency direction, cycles, shared utilities, framework leakage, hidden runtime/config dependencies, and blast radius.
4. Rank high-risk edges by change frequency, instability, ownership mismatch, deploy/test coupling, and user impact.
5. Propose decoupling strategy: invert dependency, narrow interface, move ownership, split shared utility, introduce adapter, or defer with reasons.
6. Before final handoff, load `references/checklist.md`; consult `references/anti-patterns.md` when tempted to add interfaces, ban dependencies broadly, or ignore build/runtime coupling.

## Questions to ask (only when necessary)

- Which package or subsystem should be optimized for independent change if priorities conflict?

## Quality bar

- Coupling map cites concrete edges from code/config/tool output.
- Decoupling proposals include verification and migration risk.
- Deferred coupling is explicitly justified.

## Expected output format

```markdown
## Coupling review: <scope>

**Evidence reviewed**: <commands/files/graphs>
**Coupling map**: <key edges and direction>
**High-risk dependencies**
- <edge> - risk: <impact> - evidence: <source> - recommendation: <change/defer>

**Decoupling strategy**: <steps>
**Verification plan**: <graph/test/build checks>
**Pending evidence**: <items>
```

## Failure modes to avoid

- **Coupling hidden behind interfaces**: adding an interface while ownership, dependency direction, and blast radius remain unchanged.
- **Shared-utils gravity**: treating a shared utility as neutral when it pulls domains together.
- **Graph theater**: drawing a graph without using it to rank concrete risk.

More in `references/anti-patterns.md`.
