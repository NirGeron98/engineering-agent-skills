# Sample task - Deployment Debugging

## Prompt

"The deployment succeeded, but the service returns 503 in staging. Logs and runtime configuration are not directly available."

## Abbreviated run

1. Recorded service, environment, deploy time, and 503 endpoint.
2. Requested deployment revision, runtime config, health, and log evidence.
3. Inspected deployment manifests and config templates for ports and health checks.
4. Classified cause as pending runtime evidence, with likely boundaries listed.
5. Loaded `references/checklist.md` before handoff.

## Obstacle evidence request

```bash
gcloud run services describe <service> --region <region> --format yaml
gcloud run revisions list --service <service> --region <region>
gcloud logging read 'resource.labels.service_name="<service>"' --limit 100 --format json
```

For Kubernetes/ECS/App Service, provide equivalent deploy history, health, logs, and runtime env/config screenshots or command output.

## Expected output

```markdown
## Deployment diagnosis: staging service

**Runtime evidence**: pending deploy revision, logs, and health checks.
**Boundary classification**: pending; likely health check, port, startup, routing, or runtime config.

**Mitigation or rollback**: identify previous healthy revision before changes.
**Verification**: request read-only platform status and log commands before redeploy/restart.
```

## Why this passes the quality bar

It does not infer cloud state from manifests, does not restart production/staging blindly, and gives exact evidence requests with pending conclusions.
