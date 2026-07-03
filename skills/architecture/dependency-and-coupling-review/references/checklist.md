# Dependency and Coupling Review Checklist

Use this before the final handoff.

- [ ] Defined the scope: packages, layers, services, build targets, deploy units, or runtime/config edges.
- [ ] Gathered import, manifest, build/test, dependency graph, caller, and public API evidence where available.
- [ ] Mapped dependency direction and circular dependencies.
- [ ] Checked hidden coupling through shared utilities, framework leakage, config, runtime calls, tests, and deploy/build units.
- [ ] Ranked high-risk edges by blast radius, instability, ownership mismatch, change frequency, and user impact.
- [ ] Proposed concrete decoupling steps with verification.
- [ ] Justified any coupling that should be deferred.
- [ ] Avoided hiding coupling behind interfaces without ownership or direction changes.
- [ ] If graph tools, code, manifests, or build/runtime data were missing, emitted exact evidence requests and marked conclusions pending.
