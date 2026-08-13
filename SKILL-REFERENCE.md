# Claude Code Skill Reference

A practical reference for the mechanics of Claude Code skills: file format, frontmatter fields, where files go, how triggering works, and how to debug a skill that isn't firing.

Everything here is verified against real skills shipped by Anthropic and third parties, not guessed from documentation.

## File format

A skill is a directory containing a `SKILL.md` file. The directory name and the `name` field should match.

```
my-skill/
└── SKILL.md
```

`SKILL.md` is YAML frontmatter followed by Markdown instructions:

```markdown
---
name: my-skill
description: What it does, and the concrete situation where it applies.
---

Instructions for Claude go here, in plain Markdown.
```

Skills can ship supporting files alongside `SKILL.md` (reference docs, templates, scripts) and reference them by relative path from the instructions — useful when a skill is large enough that loading everything up front would be wasteful.

## Frontmatter fields

### Required

| Field | Purpose |
|---|---|
| `name` | Identifier for the skill. Kebab-case. Should match the directory name. |
| `description` | What the skill does **and when to use it**. This is the field Claude matches against — see below. |

### Optional

| Field | Purpose |
|---|---|
| `user-invocable` | `true` makes the skill available as a slash command (`/my-skill`). |
| `allowed-tools` | Restricts the skill to a specific tool set. Accepts tool names and scoped patterns. |
| `version` | Free-form version string. Useful for skills you distribute. |
| `author` | Attribution for distributed skills. |

`allowed-tools` supports per-command scoping, not just tool names:

```yaml
allowed-tools:
  - Read
  - Write
  - Bash(ls *)
  - Bash(mkdir *)
```

This grants `Read`, `Write`, and only the `ls`/`mkdir` forms of `Bash` — useful for skills that should be able to inspect and create files but not run arbitrary commands.

## Where skills go

| Location | Scope |
|---|---|
| `.claude/skills/<name>/SKILL.md` | Project — available only in that repo. Commit it to share with your team. |
| `~/.claude/skills/<name>/SKILL.md` | User — available across all your projects. |

Skills are picked up automatically; there is no registration step or config file to edit. Plugins can also bundle skills, which is how marketplace-installed skill collections appear.

Project scope is the right default for anything encoding repo-specific conventions. User scope is right for personal workflow preferences you want everywhere.

## How triggering actually works

**Claude selects skills primarily on the `description` field.** The body is read once the skill is invoked; the description is what determines whether that happens at all.

This has a direct consequence: a description that names a *category* triggers unreliably, while one that names the *situation* triggers well.

```yaml
# Weak — Claude can't tell when this applies
description: Helps with git commits.

# Strong — names the trigger conditions
description: Draft a conventional-commit message from the staged diff. Use when the
  user asks to write or suggest a commit message, or says "commit this" without
  providing one.
```

A useful pattern from Anthropic's own skills is to write the description as a direct instruction about *when* to use it — "Use when…" — rather than only what it does. Some skills go further and state urgency explicitly ("You MUST use this before any creative work…") when the skill needs to fire reliably ahead of other behavior.

Both halves matter: what it does helps Claude confirm relevance, when to use it helps Claude notice the moment.

## Writing the body

The body is instructions to Claude, not user documentation. A few things that consistently matter:

**Specify the branches, not just the happy path.** The valuable part of a skill is what it says about the messy case: what to do when a diff spans unrelated concerns, when a "flaky" test is actually catching a real race, when a TODO can't be classified. Anyone can write the clean-path steps.

**Ban failure modes explicitly.** If a task invites a shortcut — fabricating a test result, inventing a CVE number, asserting a convention the codebase doesn't demonstrate — say so directly. Stating the failure mode works better than assuming good behavior is implied.

**Keep scope narrow.** One skill doing one thing has an unambiguous trigger condition. If you're writing "and also handle the case where…" for the third time, that's a second skill.

**Say what not to touch.** Skills that only report (audits, sweeps) should say so explicitly, or Claude may helpfully start editing.

## Debugging a skill that won't fire

Work through these in order:

1. **Check the file location and name.** `SKILL.md` must be uppercase, inside a directory, inside `.claude/skills/` or `~/.claude/skills/`.
2. **Check the frontmatter parses.** Malformed YAML — an unquoted colon inside a description is the usual culprit — can make the skill invisible. Quote descriptions containing `:` or other YAML-significant characters.
3. **Check the description names a situation.** If it only names a category, this is almost always the reason. Rewrite it with explicit trigger phrasing and test again.
4. **Check for overlap.** If two skills have similar descriptions, selection between them is ambiguous. Narrow both.
5. **Invoke it explicitly.** If `/my-skill` works but automatic triggering doesn't, the file is fine and the description is the problem — that isolates the fault cleanly.

## Distributing skills

Skills are plain files, so a git repo is a complete distribution mechanism — `cp -r` or a clone into `.claude/skills/` is a full install. For wider distribution, the plugin marketplace system supports bundling multiple skills together with other capabilities.

If you distribute skills, `version` and `author` in the frontmatter are worth setting, and it's worth writing the body so a reader can audit exactly what the skill will and won't do before installing it. Skills are instructions to an agent operating in someone's repo; that deserves the same scrutiny as any dependency.

---

The skills in this repo are written to these conventions — read any of them as a worked example. A [paid pack](https://claudedesign.gumroad.com/l/mqggk) adds six advanced skills (security triage, dependency audit, migration safety, env drift, API contract diffing, onboarding docs) and a longer guide on skill craft.
