---
name: pr-description
description: Draft a pull request title and description from the current branch's commits and diff against its base branch. Use when the user asks to open a PR, write a PR description, or prepare a branch for review.
---

1. Determine the base branch (usually `main` or `master`; if ambiguous, ask) and compute the diff/log for the current branch against it via `git merge-base` and `git log base..HEAD`.
2. Title: concise, imperative, describes the net effect of the branch — never a vague placeholder like "various fixes."
3. Description:
   - `## Summary` — 2-4 bullets on what changed and why, grounded in the actual commits/diff, not speculation about intent.
   - `## Test plan` — a checklist of what was actually verified. Only include what's evidenced by the repo's real test setup or what the user has stated they tested. Never fabricate test claims to make the PR look more verified than it is.
4. If the branch mixes unrelated changes, flag it and suggest splitting before opening the PR rather than describing a grab-bag as one coherent change.
5. Show the draft to the user. Only run `gh pr create` if they confirm and a GitHub remote actually exists — don't assume one.
