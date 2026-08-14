# Claude Code Skills Pack

15 skills for [Claude Code](https://claude.com/claude-code). MIT licensed.

These are markdown instruction files. That's all a skill is. The value, if there is any, is in what they tell Claude to do when the situation isn't clean: when a diff covers three unrelated things, when a "flaky" test is catching a real race condition, when a TODO is load-bearing. Read one before installing it and decide for yourself.

## Install

```bash
/plugin marketplace add wilddoc/claude-code-skills-pack
```

Then install `skills-pack`.

Or copy a single skill if that's all you want:

```bash
cp -r skills/commit-message ~/.claude/skills/
```

`SKILL-REFERENCE.md` covers the file format: frontmatter fields, `allowed-tools` scoping, how triggering works off the description, and why a skill sometimes won't fire.

## Skills

**Git**

| | |
|---|---|
| [`commit-message`](skills/commit-message/SKILL.md) | Conventional commit from your staged diff. Tells you when a diff should be split instead. |
| [`changelog-entry`](skills/changelog-entry/SKILL.md) | CHANGELOG entry from commits since the last tag, grouped by user-facing effect. |
| [`pr-description`](skills/pr-description/SKILL.md) | PR title and body from the branch diff. Flags mixed-concern branches. |
| [`regression-bisect`](skills/regression-bisect/SKILL.md) | `git bisect` with an automated predicate, including exit 125 for commits that won't build. |

**Testing**

| | |
|---|---|
| [`test-coverage-gaps`](skills/test-coverage-gaps/SKILL.md) | Untested paths ranked by risk, not by coverage percentage. |
| [`flaky-test-triage`](skills/flaky-test-triage/SKILL.md) | Whether it's leaked state, ordering, timing, or an actual race condition. Says when a retry would hide a bug. |
| [`refactor-safety-net`](skills/refactor-safety-net/SKILL.md) | Characterization tests before a refactor, capturing current behavior including the wrong parts. |

**Code quality**

| | |
|---|---|
| [`todo-sweep`](skills/todo-sweep/SKILL.md) | TODO/FIXME/HACK triaged stale vs risky. Read-only. |
| [`error-message-audit`](skills/error-message-audit/SKILL.md) | Error messages that leave users stuck, with rewrites. |
| [`log-noise-audit`](skills/log-noise-audit/SKILL.md) | Logged secrets, `error` level on expected conditions, failures with no request id. |

**Infrastructure**

| | |
|---|---|
| [`ci-workflow-audit`](skills/ci-workflow-audit/SKILL.md) | `pull_request_target` with untrusted checkout, unpinned actions, `\|\| true` on the test command. |
| [`dockerfile-review`](skills/dockerfile-review/SKILL.md) | Secrets in layers, `COPY . .` before dependency install, build tooling in the runtime image. |
| [`n-plus-one-queries`](skills/n-plus-one-queries/SKILL.md) | N+1s including ones behind serializers and computed properties. Checks the relation isn't already eager-loaded. |

**Frontend**

| | |
|---|---|
| [`accessibility-audit`](skills/accessibility-audit/SKILL.md) | Keyboard traps, controls with no accessible name, killed focus outlines. Won't assert contrast it can't verify. |
| [`bundle-size-audit`](skills/bundle-size-audit/SKILL.md) | Imports that defeat tree shaking, missing code splitting. Ordered by bytes, honest about measured vs inferred. |

## Paid pack

Six more, aimed at things that cost money when they go wrong: migrations that lock production tables, breaking API changes, secrets in config, env vars undefined in fresh environments. [$7 on Gumroad](https://claudedesign.gumroad.com/l/mqggk). Everything above stays free.

## Contributing

Issues and PRs welcome, especially if a skill gives you a bad result — that's more useful than a star.
