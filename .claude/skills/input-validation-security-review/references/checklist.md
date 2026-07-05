# Input Validation Security Review Checklist

- [ ] Input surface (routes, uploads, headers, deserialized bodies, queues) is mapped from actual code.
- [ ] Each input is traced from entry point to its sink (query, filesystem, shell, template, outbound request, deserializer).
- [ ] Validation/normalization gaps are identified from code, not assumed framework behavior.
- [ ] Remediation plan names concrete validation strategy, library, or boundary check.
- [ ] Test recommendations cover boundary/malformed-input cases without exploit payloads.
- [ ] Missing code path, framework version, or deployment context is marked pending.
- [ ] No injection string, exploit payload, or evasion technique is present.
