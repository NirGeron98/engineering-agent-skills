# SLO Definition Review Checklist

- [ ] Critical user journey and "what good looks like" are named explicitly.
- [ ] SLIs are traced to that journey and to an actual available measurement source.
- [ ] SLO thresholds are backed by historical traffic/latency/error data or explicitly marked as an unvalidated starting hypothesis.
- [ ] Error-budget policy names an owner and a consequence for exhaustion.
- [ ] Alerting implications are noted at a policy level without duplicating alert-config mechanics.
- [ ] Missing traffic data, metrics, or journey documentation is marked pending.
- [ ] No threshold is copied from another service without a stated reason it applies here.
