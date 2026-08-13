---
name: log-noise-audit
description: Audit logging across a codebase for wrong levels, missing context, leaked secrets, and noise that buries real signal. Use when the user asks to clean up logging, says logs are too noisy, or is debugging something that "should have been logged."
---

1. Find the logging calls and identify the library/config in use (level thresholds, structured vs. plain, where output goes).
2. Flag **leaked sensitive data** first — this is the highest-severity category. Look for logged passwords, tokens, API keys, full auth headers, cookies, request bodies on auth endpoints, PII, and full database rows containing user data. Also flag logged exception objects that may carry credentials in their message or attributes.
3. Flag **wrong levels**, which is what actually destroys signal:
   - `error` used for expected, handled conditions (a user typing a bad password is not an error) — this trains people to ignore the error channel.
   - `info`/`warn` inside hot loops or per-request paths that will flood output at volume.
   - Real failures logged at `debug` where nobody will see them.
4. Flag **missing context** — messages that can't be acted on when they fire at 3am: no identifier (request/user/job id), no relevant values, or a caught exception logged without its stack trace. "Failed to process" with no id is nearly useless.
5. Flag **redundancy** — the same event logged at multiple layers as it propagates, or a log line immediately followed by raising the same information as an exception.
6. Don't flag deliberate audit/compliance logging as noise, and don't recommend removing logs whose purpose you can't determine — mark those as "verify intent" rather than guessing.
7. Output a table grouped by severity (leaked-data → wrong-level → missing-context → redundant), with file:line and a concrete suggested replacement for each. Report only — don't rewrite logging calls unless asked.
