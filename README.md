# Claude Code Skills Pack

Free, ready-to-use [Claude Code](https://claude.com/claude-code) skills — real, working `SKILL.md` files you can drop into a project and use immediately. No filler, no "coming soon" stubs: every skill here does one specific, useful thing well.

If you've been looking for **Claude Code skills examples**, a **SKILL.md template**, or just want your AI agent to stop re-learning the same task every session, start here.

## What's a Claude Code skill?

A skill is a packaged set of instructions Claude Code loads when a task matches its description — a reusable playbook for a recurring task (drafting a commit message, triaging a flaky test, sweeping for TODOs) that you don't want to re-explain every time.

Claude decides whether to use a skill based almost entirely on its `description` field, so a good skill names the concrete situation it applies to rather than a vague category. Each file here is written that way — read one to see the pattern.

## Install

**As a plugin (all ten skills at once):**

```bash
/plugin marketplace add wilddoc/claude-code-skills-pack
```

Then install the `skills-pack` plugin from the marketplace. Updates come through `/plugin` rather than re-copying files.

**Or copy individual skills** if you only want one or two:

```bash
# Project-scoped (this repo/project only)
cp -r skills/commit-message .claude/skills/

# User-scoped (all your projects)
cp -r skills/commit-message ~/.claude/skills/
```

Claude Code picks it up automatically — invoke it by name (`/commit-message`) or let Claude trigger it when the task matches the skill's description.

> **New to writing skills?** [`SKILL-REFERENCE.md`](SKILL-REFERENCE.md) is a full reference for the format: every frontmatter field (including `allowed-tools` scoping), where files go, how Claude decides which skill to trigger, and how to debug one that won't fire.

## What's included

### Git & code review

| Skill | What it does |
|---|---|
| [`commit-message`](skills/commit-message/SKILL.md) | Drafts a conventional-commit message from your staged diff — reads the actual change, doesn't guess from filenames. |
| [`changelog-entry`](skills/changelog-entry/SKILL.md) | Generates a `CHANGELOG.md` entry from commits since your last tag, grouped by effect (Added/Fixed/Changed/Removed). |
| [`pr-description`](skills/pr-description/SKILL.md) | Drafts a PR title and description from your branch's diff against its base — flags mixed-concern branches instead of describing a grab-bag as one clean change. |
| [`regression-bisect`](skills/regression-bisect/SKILL.md) | Finds the commit that broke something via `git bisect`, including the automated predicate script — and catches the traps (flaky predicate, stale build artifacts) that make bisect confidently wrong. |

### Testing

| Skill | What it does |
|---|---|
| [`test-coverage-gaps`](skills/test-coverage-gaps/SKILL.md) | Finds critical code paths with no test coverage, prioritized by risk — not a raw coverage-percentage chase. |
| [`flaky-test-triage`](skills/flaky-test-triage/SKILL.md) | Diagnoses *why* a test is flaky (real race condition vs. order dependence vs. timing vs. environment) instead of reaching for a retry wrapper that hides a real bug. |
| [`refactor-safety-net`](skills/refactor-safety-net/SKILL.md) | Builds characterization tests around untested code *before* you refactor it, capturing what the code actually does — including the parts that look wrong. |

### Code quality & maintenance

| Skill | What it does |
|---|---|
| [`todo-sweep`](skills/todo-sweep/SKILL.md) | Finds TODO/FIXME/XXX/HACK comments and triages them into stale / low-risk / risky, as a single table. Read-only — never edits your code. |
| [`error-message-audit`](skills/error-message-audit/SKILL.md) | Flags user-facing error messages that leave people stuck, with concrete rewrites. |
| [`log-noise-audit`](skills/log-noise-audit/SKILL.md) | Audits logging for leaked secrets, wrong levels that destroy signal, and messages missing the context you'd need at 3am. |

### Infrastructure & performance

| Skill | What it does |
|---|---|
| [`ci-workflow-audit`](skills/ci-workflow-audit/SKILL.md) | Reviews CI config for secret exposure (`pull_request_target`, unpinned actions), checks that pass without verifying anything, and wasted runtime. |
| [`dockerfile-review`](skills/dockerfile-review/SKILL.md) | Finds secrets baked into layers, cache-busting instruction order, and avoidable image bloat — without reflexively telling you to switch to Alpine. |
| [`n-plus-one-queries`](skills/n-plus-one-queries/SKILL.md) | Finds N+1 query patterns in ORM code, including the ones hidden behind serializers and computed properties — and verifies the relation isn't already eager-loaded before flagging it. |

Each skill is a single self-contained `SKILL.md` — read it before installing if you want to see exactly what it will and won't do.

## Why these are written the way they are

Most skill files are just happy-path instructions. The useful part is what happens when reality doesn't cooperate, so each skill here specifies the judgment calls explicitly: when to split a commit instead of writing one message, when a "flaky" test is actually catching a real race condition, when a TODO is safe to delete. Failure modes are named and banned directly rather than left implied.

## Pro pack

There's a paid companion pack with 6 more advanced skills — security triage, dependency audit, DB migration safety checks, env drift detection, API contract diffing, onboarding doc generation — plus a guide on writing your own skills well: [Claude Code Pro Skills Pack](https://claudedesign.gumroad.com/l/mqggk).

The skills in *this* repo are complete and free forever; the pro pack is additional, not a crippled version of these.

## License

MIT — use these however you like, in personal or commercial projects, no attribution required.

## Contributing

Found a bug in one of these, or have an idea for a skill that's genuinely useful (not just a wrapper around an existing CLI command)? Open an issue or PR.
