---
name: todo-sweep
description: Find TODO/FIXME/XXX/HACK comments across the codebase and triage them into a prioritized list. Use when the user asks to find outstanding TODOs, clean up tech-debt markers, or audit the codebase for unfinished work.
---

1. Search the repo for `TODO`, `FIXME`, `XXX`, and `HACK` markers, respecting `.gitignore`.
2. For each hit, read enough surrounding context to judge whether it's still relevant, already resolved, or stale (references code/behavior that's since been removed or changed).
3. Group findings into:
   - **Stale** — safe to delete; the concern is already handled or the referenced code no longer exists.
   - **Real, low-risk** — genuinely outstanding but not urgent.
   - **Real, risky** — touches auth, payments, data integrity, or security; flag for priority attention.
4. Output a single markdown table: `file:line | marker text | category | one-line reasoning`. This skill only reports — it does not edit code or delete comments itself.
5. On large repos, sample/paginate sensibly rather than reading every file exhaustively, and tell the user explicitly if the sweep was partial rather than presenting it as complete.
