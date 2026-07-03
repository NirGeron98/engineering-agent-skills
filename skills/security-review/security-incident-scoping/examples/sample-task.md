# Sample Task

## User prompt

"We suspect a token was used unexpectedly from an unfamiliar location. Scope the incident."

## Abbreviated run

- Inspect auth logs for the token's recent usage, IP/location, timestamps, and the resources it granted access to.
- Separate the confirmed fact (login from unfamiliar location/IP) from the unconfirmed assumption (that this is malicious rather than travel/VPN).
- Obstacle: the logging system doesn't show what actions were taken after the login, only the login event.

## Evidence requests

- Full auth log entries for the token/session, including subsequent API calls if logged elsewhere.
- Whether the associated user has travel, VPN, or new-device context that could explain the location.
- Access/audit log for resources the token could reach.

## Expected output

An incident scope summary marking the login-location anomaly as a known fact and "malicious use" as unconfirmed, an evidence preservation checklist (freeze/export logs, do not revoke session yet without capturing state) before proposing containment (revoke token, force re-auth) with approval and rollback framing, and a follow-up investigation plan for the missing action-level logs.

## Why this passes the quality bar

The agent does not declare a compromise confirmed from a single anomalous login and preserves evidence before recommending containment.
