---
name: commit-message
description: Draft a conventional-commit-style message from the currently staged git diff. Use when the user asks to write, draft, or suggest a commit message, or says "commit this" without providing a message.
---

1. Run `git diff --staged`. If nothing is staged, fall back to `git diff` and tell the user nothing is staged yet — don't silently guess.
2. Read the diff and identify the single dominant change. If it spans multiple unrelated concerns, say so and suggest splitting into separate commits rather than writing one message for everything.
3. Draft the message:
   - First line: `<type>(<scope>): <summary>`, imperative mood, ≤72 characters. Types: `feat`, `fix`, `refactor`, `docs`, `test`, `chore`, `perf`, `style`.
   - Blank line, then 1-3 bullets on *why* — only if that adds information beyond what the diff already shows. Skip the body entirely for self-explanatory diffs.
4. Don't infer type/scope from filenames alone — base it on what the diff actually does.
5. Show the drafted message and wait for confirmation before running `git commit`. Never commit without the user approving the message first.
