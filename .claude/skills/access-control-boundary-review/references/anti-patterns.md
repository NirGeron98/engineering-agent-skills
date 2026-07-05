# Access Control Boundary Review Anti-Patterns

## Broad permission fix

- **Symptom**: An access error is resolved by widening a role or adding a wildcard scope.
- **Why it matters**: This trades a functional bug for a larger security exposure.
- **Correction**: Narrow the fix to the specific missing permission needed.

## Naming-convention trust

- **Symptom**: A function named `checkPermission` or `isAuthorized` is assumed correct without reading its logic.
- **Why it matters**: Names don't guarantee correct enforcement; the function may check existence, not ownership.
- **Correction**: Read the actual enforcement logic before trusting the boundary.

## Existence-only check

- **Symptom**: Code checks that a resource ID exists but not that the requesting user/tenant owns it.
- **Why it matters**: This is a common confused-deputy / IDOR-class gap.
- **Correction**: Verify ownership or tenant match, not just existence.

## Remediation without ownership

- **Symptom**: A boundary fix is recommended with no owner or review process.
- **Why it matters**: Permission model changes without review can introduce new gaps.
- **Correction**: Require review/approval ownership for permission model changes.

## Confused-deputy blindness

- **Symptom**: A service-to-service call is trusted to carry the original caller's permission without re-verification.
- **Why it matters**: This lets a lower-privileged caller gain elevated access through an intermediary service.
- **Correction**: Re-check the original caller's permission at each trust boundary, not just at the edge.
