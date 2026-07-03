# ADR Anti-patterns

## ADR theater

- **Symptom**: The ADR is polished but hides unresolved questions or missing evidence.
- **Why it matters**: Future readers trust a record that was never actually grounded.
- **Correction**: Mark pending evidence and open questions directly in the ADR.

## Decision laundering

- **Symptom**: The chosen option is presented as obvious because alternatives are omitted.
- **Why it matters**: The ADR loses the tradeoff history that makes it useful later.
- **Correction**: Include rejected alternatives and why they lost.

## Eternal decision

- **Symptom**: No revisit trigger, owner, or invalidating condition exists.
- **Why it matters**: Temporary constraints become permanent architecture.
- **Correction**: Add a specific trigger such as scale threshold, migration milestone, incident pattern, or date.

## Backfilled certainty

- **Symptom**: The ADR claims "we evaluated" options without links, notes, or examples.
- **Why it matters**: It rewrites history and weakens accountability.
- **Correction**: Link evidence or label the evaluation as reconstructed.

## ADR as implementation plan

- **Symptom**: The ADR becomes a task list instead of a decision record.
- **Why it matters**: It mixes durable rationale with change sequencing.
- **Correction**: Keep follow-up actions short and hand detailed planning to `skills/planning/task-breakdown`.
