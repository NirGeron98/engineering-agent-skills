# Access Control Boundary Review Checklist

- [ ] Privilege model (roles, permissions, ownership, trust relationships) is mapped from actual code/config.
- [ ] Each expected boundary (user/admin, tenant/tenant, service/service) has its enforcement code inspected, not assumed.
- [ ] Confused-deputy risk (trusted service acting without re-checking caller permission) is explicitly assessed.
- [ ] Ownership-boundary checks (not just existence checks) are verified for resource access.
- [ ] Missing and overbroad permissions are both considered, not just missing ones.
- [ ] Remediation narrows scope rather than broadening access.
- [ ] Missing role definitions, enforcement code, or ownership model is marked pending.
- [ ] No exploitation of a found gap is demonstrated.
