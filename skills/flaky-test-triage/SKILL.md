---
name: flaky-test-triage
description: Diagnose why a test is flaky and classify the root cause (timing, shared state, ordering, external dependency, real race condition). Use when a test passes locally but fails in CI, fails intermittently, or the user says a test is "flaky."
---

Flaky tests usually have one of a handful of causes. Identify which before proposing a fix — retrying a test that fails for a real reason hides a real bug.

## Process

1. Read the test and everything it touches: setup/teardown, fixtures, module-level state, and any shared resource (database, temp files, ports, clock, global config).
2. Check each cause below against the evidence. Check **real race condition** first — it's the most important case and the easiest to misdiagnose as "just flaky."
3. Confirm the diagnosis before fixing where you can: run the test in isolation vs. in suite, run it repeatedly, or run with a randomized seed/order if the framework supports it. Report what you actually observed — don't assert a cause you only inferred from reading.
4. Propose the fix that addresses the cause, not the symptom. Never suggest a retry wrapper or `skip` as the primary fix for a suspected real race condition — say plainly that it would be hiding a bug.
5. If the root cause genuinely can't be determined from available evidence, say so and list the specific diagnostic that would settle it, rather than guessing.

## Causes to check

These aren't sequential — they're a classification, not a checklist to complete in order. Check the ones the evidence points to.

### Real race condition in the code under test
The test is correctly catching a genuine concurrency bug. This is not a flaky test — it's a real one that happens to fail intermittently. Treat it accordingly.

### Test-order dependence
Passes alone, fails in a suite (or vice versa). Look for state leaking across tests: module-level mutables, un-reset mocks, a database not rolled back, singletons.

### Timing/async
Fixed `sleep` values, waiting on a duration rather than a condition, assertions that assume an async operation completed.

### Time and locale
Tests using real `now()`, timezone-dependent formatting, DST boundaries, or locale-dependent sorting/parsing.

### External dependency
Real network calls, unpinned container images, third-party API rate limits.

### Resource contention
Hardcoded ports, shared temp paths, parallel workers writing the same file.
