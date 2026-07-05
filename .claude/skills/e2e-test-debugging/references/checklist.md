# E2E Test Debugging Checklist

- [ ] Test name, command, environment, browser, retry count, recent changes, and deterministic/flaky status are recorded.
- [ ] Screenshot, video, trace, console, network/HAR, test logs, app logs, and test data evidence were inspected or requested.
- [ ] Runtime claims avoid source-only certainty.
- [ ] Selector stability, waits/timing, auth/session, API responses, environment drift, and data setup/cleanup were checked.
- [ ] Root cause is classified as app defect, selector, timing, test data, auth/session, API/backend, environment drift, or true flake.
- [ ] Ruled-out causes include evidence.
- [ ] Fix or stabilization plan includes exact verification command.
- [ ] Missing browser artifacts, logs, screenshots, network traces, or command output are marked pending.

