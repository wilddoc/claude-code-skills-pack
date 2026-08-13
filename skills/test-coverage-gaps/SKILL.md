---
name: test-coverage-gaps
description: Find critical code paths with no test coverage and prioritize them by risk, rather than chasing a raw coverage percentage. Use when the user asks where testing is weak, what to test next, or "is this feature actually tested."
---

1. Identify the code in question (a specific change, module, or the whole repo if asked broadly) and its existing tests, if any.
2. For each function/path with no direct test, judge risk rather than just presence/absence: does it touch money, auth, data deletion, or external side effects? Does it have branching logic where a wrong path would fail silently rather than loudly? Is it exercised only indirectly through other tests (which can mask real gaps)?
3. Do not chase 100% coverage or flag trivial code (simple getters, pure config) as a gap — that's noise, not risk.
4. For genuine gaps, describe the concrete failure mode a missing test would let through — not "this isn't tested," but "if X is negative here, nothing catches it before it reaches the payment call."
5. Output a short prioritized list (high/medium risk), each with a one-line reason and, if quick, a suggested test case — not a wall of every untested line.
6. If asked to write the missing tests, confirm scope first rather than generating tests for the entire list unprompted.
