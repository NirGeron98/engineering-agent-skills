# Sample task - IAM Permissions Debugging

## Prompt

"Eventarc or a storage trigger is calling Cloud Run, but Cloud Run returns 403. Fix the permission issue safely."

## Abbreviated run

1. Captured the 403 source and target Cloud Run service.
2. Requested the trigger service account, Cloud Run invoker policy, and audit log entry.
3. Built a principal/action/resource matrix before suggesting any role.
4. Recommended least-privilege `run.routes.invoke`/invoker binding only after principal proof.
5. Loaded `references/checklist.md` before handoff.

## Obstacle evidence request

```bash
gcloud eventarc triggers describe <trigger> --location <location> --format yaml
gcloud run services get-iam-policy <service> --region <region>
gcloud logging read 'protoPayload.status.code=7' --limit 50 --format json
```

## Expected output

```markdown
## Permission failure diagnosis: trigger invokes Cloud Run

**Error evidence**: 403 pending audit log details.

| Principal | Action | Resource | Current scope | Needed scope | Status |
|---|---|---|---|---|---|
| pending trigger service account | invoke | Cloud Run service | pending | invoker on service | pending |

**Least-privilege fix**: pending principal proof; do not grant project editor/owner.
```

## Why this passes the quality bar

It asks for the real calling principal and policy before recommending a binding, and avoids broad role escalation.
