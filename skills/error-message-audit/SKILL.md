---
name: error-message-audit
description: Review user-facing error messages in a codebase for clarity and actionability — flag ones that leave the user stuck. Use when the user asks to audit error messages, improve error handling UX, or "why do our errors suck."
---

1. Find user-facing error strings: thrown exceptions with messages shown to end users, form validation messages, API error responses, CLI error output — not internal log lines meant only for developers.
2. For each one, judge whether a user hitting it would know what to do next. Flag as weak: generic messages ("Something went wrong," "Invalid input," "Error 500") with no specifics, messages that expose raw stack traces or internal identifiers to end users, messages that state the problem but not the fix ("Invalid date" without saying the expected format).
3. Don't flag intentionally generic messages that exist for good reason — e.g. deliberately vague auth failures that avoid leaking whether a username exists. Note the security tradeoff instead of "fixing" it into a leak.
4. For each flagged message, suggest a concrete rewrite: state what went wrong in plain terms and what the user can do about it, without exposing internals (stack traces, SQL, file paths) that don't help them.
5. Output a table: location, current message, issue, suggested rewrite. Group by severity (blocks the user with no path forward vs. just unclear wording).
6. Don't rewrite messages in code directly unless asked — default to a report the user can review first.
