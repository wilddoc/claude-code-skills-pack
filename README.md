# Claude Code Skills Pack

A small collection of free, ready-to-use [Claude Code](https://claude.com/claude-code) skills — real, working `SKILL.md` files you can drop into a project and use immediately. No filler, no "coming soon" stubs: every skill here does one specific, useful thing well.

## What's a Claude Code skill?

A skill is a packaged set of instructions Claude Code loads when a task matches its description — think of it as a reusable playbook for a recurring task (drafting a commit message, generating a changelog, sweeping for TODOs) that you don't want to re-explain every time.

## Install

Copy any skill folder into your skills directory:

```bash
# Project-scoped (this repo/project only)
cp -r skills/commit-message .claude/skills/

# User-scoped (all your projects)
cp -r skills/commit-message ~/.claude/skills/
```

Claude Code picks it up automatically — invoke it by name (`/commit-message`) or let Claude trigger it when the task matches the skill's description.

## What's included

| Skill | What it does |
|---|---|
| [`commit-message`](skills/commit-message/SKILL.md) | Drafts a conventional-commit message from your staged diff — reads the actual change, doesn't guess from filenames. |
| [`changelog-entry`](skills/changelog-entry/SKILL.md) | Generates a `CHANGELOG.md` entry from commits since your last tag, grouped by effect (Added/Fixed/Changed/Removed). |
| [`pr-description`](skills/pr-description/SKILL.md) | Drafts a PR title and description from your branch's diff against its base — flags mixed-concern branches instead of describing a grab-bag as one clean change. |
| [`todo-sweep`](skills/todo-sweep/SKILL.md) | Finds TODO/FIXME/XXX/HACK comments across the codebase and triages them into stale / low-risk / risky, as a single table. Read-only — never edits your code. |

Each skill is a single self-contained `SKILL.md` — read it before installing if you want to see exactly what it will and won't do.

## License

MIT — use these however you like, in personal or commercial projects, no attribution required.

## Contributing

Found a bug in one of these, or have an idea for a skill that's genuinely useful (not just a wrapper around an existing CLI command)? Open an issue or PR.
