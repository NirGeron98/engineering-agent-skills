# Deployment Debugging - Checklist

## Evidence
- [ ] Service/app/job, environment, platform, and symptom recorded
- [ ] Deployment id, time, artifact/image version or digest recorded or requested
- [ ] Deploy history and previous healthy version checked
- [ ] Logs/startup errors captured or exact query supplied
- [ ] Health check status and endpoint behavior captured

## Runtime configuration
- [ ] Environment variables/config references checked without exposing secrets
- [ ] Ports/listeners/start command checked
- [ ] Runtime identity/service account checked
- [ ] Resource limits, scaling, concurrency, and cold start settings checked where relevant
- [ ] Drift against healthy environment or previous revision checked

## Network and routing
- [ ] Ingress/load balancer/DNS/TLS/path routing checked where relevant
- [ ] Egress/dependency connectivity checked where relevant
- [ ] Service URL and environment base URL checked
- [ ] CORS/browser-only symptoms handed to frontend/API integration when appropriate

## Safety and handoff
- [ ] Boundary classification states confirmed vs pending evidence
- [ ] Rollback/mitigation option identified before invasive changes
- [ ] Application defects handed to `service-debugging` or `diagnose-bug`
- [ ] IAM defects handed to `iam-permissions-debugging`
- [ ] Kubernetes workload defects handed to `kubernetes-workload-debugging`
