---
name: regression-bisect
description: Track down which commit introduced a regression using git bisect, including writing the automated test script bisect needs. Use when something used to work and now doesn't, or the user asks which commit broke a feature.
---

1. Pin down the failure precisely before bisecting: the exact command that reproduces it, the exact expected vs. actual behavior, and a known-good commit/tag. A vague repro wastes the entire bisect — bisect is only as reliable as the test you give it.
2. Establish the bounds. Verify the failure actually reproduces at `HEAD` and actually does *not* reproduce at the claimed-good commit. If the "good" commit also fails, the regression is older than assumed — widen the range before continuing.
3. Prefer `git bisect run` with a script over manual bisecting. The script must exit 0 for good, 1 for bad, and 125 for "cannot test" (build failure, missing dependency at that commit) so bisect skips rather than misclassifies those commits.
4. Watch for traps that produce a wrong answer:
   - Build artifacts or dependencies left over between commits — clean/reinstall inside the script if the project needs it.
   - A flaky test used as the bisect predicate, which will point at a random commit with total confidence. Confirm the predicate is deterministic first.
   - Merge commits where the regression came in via a branch — read the actual diff of the identified commit rather than trusting the hash alone.
5. Once bisect identifies a commit, read its diff and explain the specific mechanism by which it causes the failure. If the diff doesn't plausibly explain the symptom, treat that as a sign the bisect was misled — re-verify rather than reporting a suspicious result as settled.
6. Always run `git bisect reset` when finished, and report the commit hash, its author/date, the mechanism, and a suggested fix.
