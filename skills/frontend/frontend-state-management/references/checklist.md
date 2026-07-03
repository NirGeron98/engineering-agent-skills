# Frontend State Management — Checklist

## Evidence
- [ ] Feature entry point and relevant child components opened
- [ ] Stores/composables/services/query hooks/cache setup inspected
- [ ] At least one failing or representative interaction traced
- [ ] Browser/test/network evidence captured or exact pending request provided

## State inventory
- [ ] Local UI state listed
- [ ] Derived state listed and checked for unnecessary storage
- [ ] URL/search param state listed
- [ ] Server/cache state listed with cache keys
- [ ] Global app state listed with read/write sites
- [ ] Form state and validation state separated from persisted domain state
- [ ] Optimistic/transient state listed with rollback path

## Ownership & flow
- [ ] One source of truth chosen per value
- [ ] Duplicated values either removed or justified
- [ ] Mutation flow traced from user event to cache/store/UI update
- [ ] Navigation/filter/pagination flow traced
- [ ] Race handling checked: cancellation, stale responses, unmounts, retries

## Fix & verification
- [ ] Fix is the smallest ownership/invalidation change that addresses the evidence
- [ ] Cache invalidation/update keys named exactly
- [ ] Optimistic update rollback or failure state handled
- [ ] Verification covers stale data after mutation and route/filter changes

## Handoff
- [ ] Confirmed facts separated from pending runtime checks
- [ ] New state model summarized in a durable way
