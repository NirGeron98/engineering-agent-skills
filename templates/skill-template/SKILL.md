---
name: <kebab-case-skill-name>
description: <THE MOST IMPORTANT FIELD. One or two sentences an agent uses to decide whether to load this skill, without opening the file. Formula: [verb + the one workflow this solves] + "Use when" [concrete triggers: task phrasings, file types, symptoms] + [what it produces] + optionally [what it is NOT for]. Be specific: "Review API contract changes for compatibility; use when an endpoint or OpenAPI schema changes" — not "Helps with APIs".>
---

# <Skill Title>

## Goal

<One paragraph: the outcome this skill produces and the standard it must meet.
One workflow only — if you need "and", split the skill.>

## When to use

- <Concrete trigger: a task phrasing, artifact, or symptom>
- <Another trigger>

## When not to use

- <Adjacent situation that looks similar but belongs to a different skill — name that skill>
- <Situation where the skill's overhead isn't worth it>

## Workflow

1. **<Investigate first.>** <Every skill starts by gathering evidence from the
   actual system — files read, queries run, routes enumerated. No claims
   without evidence.>
2. **<Find the existing convention/pattern.>** <Most work is "another one of
   these" — locate the precedent and mirror it.>
3. **<Core steps of the workflow.>** <Each step should be verifiable: an
   observer can tell whether it was done.>
4. **<Verify.>** <Run the test, double-run the load, trace the request —
   prove the result rather than assert it.>
5. **<Hand off.>** <Produce the output-format summary another person or agent
   can act on without re-deriving context. End this step with the reference
   wiring — e.g. "Before writing the handoff, load references/checklist.md and
   verify every item; consult references/anti-patterns.md if any step felt
   ambiguous." Without this line the reference files are never read at runtime.>

<If the skill touches live systems (queries, logs, deploys), open the workflow
with an evidence access rule: "When you cannot run a query/command yourself,
output the exact command for the user to run and paste back; mark dependent
conclusions as pending; never substitute an assumption for a missing result.">

## Questions to ask (only when necessary)

- <A question only the user can answer — intent, SLA, authorization. Not
  anything discoverable from the code or data.>

<State the rule: everything discoverable must be discovered, not asked; bundle
the rest, max 2–3.>

## Quality bar

- <Evidence standard: every claim cites what was read/run>
- <Verification standard: what must have been demonstrated, not asserted>
- <Scope standard: minimal diff / findings ranked / no drive-bys>

## Expected output format

```markdown
## <Artifact title>: <subject>

**<Key facts section>**
**<Findings/steps section with locations and evidence>**
**<Verification section: what was run and the results>**
**<Handoff section: follow-ups, impacts, open questions>**
```

## Failure modes to avoid

- **<Named failure mode>**: <the tempting shortcut and why it fails>
- **<Named failure mode>**: <symptom → what to do instead>
- <Keep only the 2–3 that must shape in-flight behavior; the full catalog lives
  in references/anti-patterns.md. Do not duplicate content between the two.>

More in `references/anti-patterns.md`.
