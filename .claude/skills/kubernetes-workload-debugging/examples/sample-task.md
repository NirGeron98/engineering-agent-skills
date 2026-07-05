# Sample task - Kubernetes Workload Debugging

## Prompt

"A Kubernetes deployment is stuck in CrashLoopBackOff or Pending after the latest rollout."

## Abbreviated run

1. Requested namespace, workload name, context, and rollout time.
2. Collected or requested read-only get/describe/logs/events output.
3. Checked scheduling, image pull, probes, config refs, resources, and service selectors.
4. Classified likely cause only where evidence supported it.
5. Loaded `references/checklist.md` before handoff.

## Obstacle evidence request

```bash
kubectl -n <namespace> get deploy,rs,pod,svc,ingress
kubectl -n <namespace> describe deploy/<name>
kubectl -n <namespace> describe pod <pod>
kubectl -n <namespace> logs <pod> --all-containers --previous
kubectl -n <namespace> get events --sort-by=.lastTimestamp
```

## Expected output

```markdown
## Kubernetes workload diagnosis: <namespace>/<deployment>

**Likely cause**: pending cluster evidence.
**Ruled out**: none yet; source manifests alone cannot prove runtime state.
**Minimal safe fix**: pending; do not delete pods or change probes before describe/log/event evidence.
```

## Why this passes the quality bar

It starts with read-only Kubernetes evidence, avoids destructive diagnostic commands, and separates cluster health from application defects.
