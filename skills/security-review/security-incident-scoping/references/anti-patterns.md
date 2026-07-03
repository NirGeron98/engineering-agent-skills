# Security Incident Scoping Anti-Patterns

## Suspicion treated as fact

- **Symptom**: An anomaly or alert is described as a confirmed compromise before evidence supports it.
- **Why it matters**: Overclaiming can trigger unnecessary panic or wrong containment actions; underclaiming risk is also possible if evidence is dismissed.
- **Correction**: Clearly separate known facts from suspected facts and state confidence level.

## Containment without evidence preservation

- **Symptom**: Accounts are disabled, sessions killed, or systems restarted before logs/state are captured.
- **Why it matters**: This destroys the evidence needed to understand scope and root cause.
- **Correction**: Preserve evidence (logs, snapshots, session state) before destructive containment.

## Remediation without ownership

- **Symptom**: Containment steps are listed with no named approver or incident commander.
- **Why it matters**: Containment actions can have wide blast radius and need accountable sign-off.
- **Correction**: Require named ownership/approval before executing containment steps.

## Attacker profiling without evidence

- **Symptom**: The scoping speculates about attacker identity, motive, or sophistication beyond what evidence shows.
- **Why it matters**: Speculation can misdirect investigation and is not defensible.
- **Correction**: Stick to observed evidence; mark attribution as out of scope or pending forensics.

## Scope creep without evidence

- **Symptom**: Every system is declared "possibly affected" without a basis in logs or access records.
- **Why it matters**: This dilutes focus and delays real containment.
- **Correction**: Base affected-asset claims on actual evidence; mark unconfirmed systems separately.
