# Hot Path Profiling Review Checklist

- [ ] The latency target and actual measured latency were confirmed.
- [ ] A real profiling output (CPU/allocation/wall-clock) was obtained and read, not assumed.
- [ ] Hot functions are classified as CPU, IO, DB, or network-bound from evidence.
- [ ] Hot functions were mapped back to the actual request path code.
- [ ] Allocation hotspots were checked if GC/memory pressure appeared in the profile.
- [ ] The measurement environment (load level, hardware, build) is stated.
- [ ] Optimization candidates are ranked by evidence strength, not intuition.
- [ ] Rejected guesses are listed with reasons.
- [ ] A before/after verification plan uses the same measurement method.
- [ ] Missing profiling data or path code is marked pending.
