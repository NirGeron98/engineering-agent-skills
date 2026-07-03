# Input Validation Security Review Anti-Patterns

## Exploitability theater

- **Symptom**: The review includes a working injection payload or exploit string to "prove" the gap.
- **Why it matters**: This crosses into offensive content and isn't needed to justify a fix.
- **Correction**: Describe the gap (missing validation, unsafe sink) and its risk without a working payload.

## Framework-trust assumption

- **Symptom**: The review assumes a framework or ORM automatically sanitizes input without checking its actual configuration or version.
- **Why it matters**: Defaults change across versions and configurations; assuming safety can miss real gaps.
- **Correction**: Verify the actual validation/escaping behavior in the version and config used.

## Generic injection lecture

- **Symptom**: The review names an injection class (SQLi, SSRF, XXE) without tracing it to a specific code path.
- **Why it matters**: Unfounded findings waste remediation effort and erode trust in the review.
- **Correction**: Trace every finding to actual entry point, parsing, and sink code.

## Remediation without ownership

- **Symptom**: A fix is proposed with no owner or priority.
- **Why it matters**: Security gaps without an owner don't get fixed before shipping.
- **Correction**: Assign an owner and priority based on exposure and reachability.

## Allowlist theater

- **Symptom**: A "validation added" claim turns out to be a denylist of a few known-bad characters.
- **Why it matters**: Denylists are easy to bypass and give false confidence.
- **Correction**: Prefer allowlist/positive validation and canonicalization over denylists.
