---
name: ci-workflow-audit
description: Review CI/CD workflow files for security holes, wasted runtime, and checks that pass without actually verifying anything. Use when the user asks to review CI config, speed up the pipeline, or asks why CI is slow, expensive, or unreliable.
---

1. Find the workflow files (`.github/workflows/`, `.gitlab-ci.yml`, `Jenkinsfile`, CircleCI config, etc.) and read them together with the scripts they invoke — a workflow that calls `make ci` hides its real behavior in the Makefile.

2. **Security first.** These are the findings that matter most:
   - Secrets exposed to untrusted code. On GitHub Actions, `pull_request_target` combined with checking out the PR's head is the classic critical bug — it runs fork-authored code with access to repository secrets. Flag it as critical.
   - Third-party actions pinned to a mutable tag or branch (`@v3`, `@main`) rather than a commit SHA. A compromised or retagged action runs with your credentials.
   - Secrets echoed into logs, passed as command-line arguments (visible in process lists), or interpolated into shell strings where a crafted branch/PR title can inject commands.
   - Over-broad permissions: a default-write `GITHUB_TOKEN` where the job only needs read.

3. **Checks that don't check.** A green pipeline that verifies nothing is worse than no pipeline, because it manufactures false confidence. Look for: steps with `continue-on-error` or `|| true` on the actual test command, linters whose exit code is discarded, test commands that pass when zero tests run, and conditionals that silently skip the real verification on some branches.

4. **Wasted time and money.** Only after the above: no dependency caching, reinstalling toolchains every run, full matrix builds where one platform would catch the issue, jobs running sequentially that have no dependency between them, and workflows triggering on paths that can't affect the result (docs-only changes running the full suite).

5. Don't recommend caching or parallelism for a pipeline that's already fast — the added complexity has a real maintenance cost. Judge against actual runtime if the user can tell you what it is.

6. Output findings grouped: **critical security** → **checks that don't verify** → **efficiency**. Each with file:line, the concrete consequence, and the fix. Say plainly if you found nothing in a category rather than padding it.
