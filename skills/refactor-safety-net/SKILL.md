---
name: refactor-safety-net
description: Establish characterization tests around untested code before refactoring it, so behavior changes get caught. Use when the user wants to refactor, clean up, or restructure code that has little or no test coverage.
---

Refactoring untested code without a safety net is rewriting and hoping. Build the net first.

1. Identify the exact boundary being refactored — the function, class, or module whose external behavior must stay identical. Everything inside is fair game; everything crossing that boundary is a contract to preserve.
2. Write characterization tests that capture what the code *currently does*, including behavior that looks wrong. The goal is detecting change, not asserting correctness. If you find behavior that appears to be a bug, note it separately for the user to decide on — do not silently "fix" it while writing the net, or the tests will encode your assumption instead of reality.
3. Cover the boundary's real surface: the happy path, each distinct branch, edge inputs (empty, null/None, zero, negative, very large), and error paths — including the specific exception types and messages callers might depend on.
4. Run the tests against the unmodified code and confirm they all pass before touching anything. A characterization test that fails on the original code is a broken test, not a discovered bug — fix the test first.
5. Refactor in small steps, running the suite after each. When a test fails, treat it as the net working: determine whether the behavior change was intended before adjusting anything. Never edit a characterization test to match new behavior without explicitly flagging that you're changing the contract.
6. When done, report which behaviors are now locked down and which parts of the boundary remain untested — an honest map of the net's holes is more useful than an implied guarantee of full coverage.
