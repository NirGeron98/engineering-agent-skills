# Sample task: staging checkout timeouts

## Prompt

> Checkout requests in staging started timing out this afternoon. Prod is fine. Can you figure out what's going on?

## Abbreviated agent run

1. The agent has the repo but **no access to staging logs, dashboards, or deploy history**. Instead of guessing, it issues an evidence request:
   > I can't reach your observability stack. Please run and paste back:
   > 1. `kubectl rollout history deploy/payments-service -n staging` (and the same for checkout-service)
   > 2. Timestamp of the first timeout from your gateway dashboard
   > 3. `kubectl logs deploy/payments-service -n staging --since=3h | grep -iE "timeout|pool|error" | head -50`
   >
   > Until these arrive, everything below the timeline is pending.
2. User pastes results. Timeline: first timeout log 13:42. Deploy history: `payments-service` deployed to staging 13:37 (prod not yet). Also visible: nightly data refresh finished 13:10.
3. Characterized from the pasted gateway data: only `POST /checkout`, 100% timeout after exactly 30s (gateway limit), all instances, p50 itself is >30s — not a tail problem; every request slow.
4. Traced request `req_8f3a…` (user pasted the trace): gateway → checkout-service (fast) → payments-service `POST /authorize` — no response within 30s. Failing hop: payments-service.
5. Pasted payments-service logs: `pg pool: connection acquire timeout` repeating since 13:39. Layer: resource saturation, downstream of the deploy.
6. Read the deployed diff (repo access — no request needed): new fraud-check runs a query per line item **inside** the payment transaction, holding connections; staging pool size is 5 (prod: 50) — verified from the config files in the repo.
7. No state changed during diagnosis; nothing was claimed that wasn't in a pasted artifact or the repo. Remediation options assessed.

## Expected output

```markdown
## Service diagnosis: payments-service — checkout timeouts in staging

**Timeline**: timeouts since 13:42; correlated change: payments-service deploy
13:37 (commit 4be91, staging only — explains prod being fine)
**Blast pattern**: POST /checkout only, ~100% of requests, all instances,
uniform 30s gateway timeout (p50 > 30s, not a tail effect)

**Traced request**: req_8f3a… — fails at payments-service POST /authorize,
no response in 30s
**Failure layer**: resource saturation (pg connection pool), caused by deployed code
**Evidence**: "pool acquire timeout" logs start 13:39; new fraud-check issues
per-line-item queries inside the payment transaction (fraud.ts:52), holding
connections; staging pool=5 vs prod pool=50 (config diff). Nightly data refresh
ruled out: completed 13:10, symptom-free until 13:39.

**Remediation proposal**
- Roll back staging deploy — risk: low, staging only — reversible: yes —
  success: checkout p50 back under 800ms baseline
- Real fix before prod: batch fraud queries and move them outside the payment
  transaction — without it, prod pool (50) survives longer but degrades under load
- Replay concern: none — gateway timeouts returned errors to clients; no queue

**Confirmed vs hypothesis**: pool exhaustion by in-transaction queries is
confirmed (logs + code + config); exact prod headroom is estimated, not load-tested
**Observability gaps**: payments-service doesn't propagate trace IDs from
checkout-service; correlation was done by timestamp
```

## Why this passes the quality bar

The agent lacked observability access and **requested exact evidence instead of pretending** — every log line and timestamp in the diagnosis traces to a pasted artifact or a repo file; the deploy correlation was verified by reading the actual diff, not assumed; the alternative change (data refresh) was explicitly ruled out; the environment config delta explains why staging broke and prod didn't; nothing was restarted or mutated; the prod risk of merging as-is is called out with a concrete mechanism.
