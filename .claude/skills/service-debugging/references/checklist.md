# Service Debugging — Checklist

## Evidence access
- [ ] For every signal you couldn't fetch yourself: exact query/command handed to the user, result pasted back
- [ ] Conclusions depending on missing evidence marked as pending, not assumed
- [ ] Evidence captured before it rotates (log retention window noted if tight)

## Timeline & characterization
- [ ] Symptom onset time pinned from dashboards/logs
- [ ] Changes near onset checked: deploys, config/flags, migrations, dependency deploys, cert/credential expiry
- [ ] Symptom quantified: endpoints, error rate vs baseline, p50/p99, instances, tenants
- [ ] Segmented: all traffic vs one tenant/endpoint/instance

## Tracing
- [ ] One concrete failing request followed across all hops
- [ ] Exact failing hop and verbatim error captured
- [ ] If untraceable: observability gap recorded as a finding

## Classification
- [ ] Failure layer identified: code / config-env / dependency / saturation / data poison
- [ ] Evidence stated that excludes the neighboring layers
- [ ] Failing env config diffed against a healthy env
- [ ] Code path at the failing hop read (timeouts, retries, pools, error handling)

## Safety
- [ ] All diagnostic steps read-only
- [ ] No restarts / purges / flag flips without explicit authorization
- [ ] Remediation includes blast radius, reversibility, success metric
- [ ] Retry/queue replay behavior after the fix considered

## Handoff
- [ ] Confirmed facts separated from hypotheses
- [ ] Post-remediation metric verified back to baseline
- [ ] Observability gaps listed as follow-ups
