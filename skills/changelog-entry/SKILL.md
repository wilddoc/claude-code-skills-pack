---
name: changelog-entry
description: Generate a CHANGELOG.md entry from commits since the last tag or a given range. Use when the user asks to update the changelog, prepare release notes, or summarize recent changes for a release.
---

1. Find the last tag: `git describe --tags --abbrev=0`. If there is none, use the repo's first commit as the range start.
2. List commits since then: `git log <last_tag>..HEAD --oneline`.
3. Group commits by effect, not by commit-message prefix alone — read enough of each change to place it correctly:
   - `Added` — new features (`feat`)
   - `Fixed` — bug fixes (`fix`)
   - `Changed` — refactors, performance, behavior changes (`refactor`, `perf`, tweaks)
   - `Removed` — deletions/reverts
   Skip merge commits and pure CI/chore noise unless the range has nothing else worth listing.
4. Write each entry in user-facing language describing the effect on someone using the software — not commit-message language describing the diff.
5. Insert under a new `## [Unreleased]` heading (or a version number if the user gives one) at the top of `CHANGELOG.md`. If the file doesn't exist, create it with a standard Keep a Changelog header.
6. Show the diff to the user before writing the file.
