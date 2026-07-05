# Task Breakdown — Checklist

## Before writing the plan
- [ ] Outcome restated in one observable sentence
- [ ] All touched areas located by search, key files opened
- [ ] Closest existing pattern in the repo identified
- [ ] Contract changes (schema, API, events) identified up front

## Plan quality
- [ ] 4–10 steps; each leaves the system in a working state
- [ ] Every step names real, inspected files
- [ ] Every step has a verification method (test / command / check)
- [ ] Tests embedded in behavior-changing steps, not appended at the end
- [ ] Steps ordered: contracts → core logic → wiring → cleanup
- [ ] Risks listed with mitigation or explicit acceptance
- [ ] Out-of-scope list present
- [ ] Open questions only where genuinely blocking, max 2–3, bundled
- [ ] Plan delivered somewhere durable (PR description, issue, plan file) — not only chat

## Before executing
- [ ] Irreversible or contract-breaking steps confirmed with the user
- [ ] Baseline tests pass before step 1
