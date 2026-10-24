---
name: setup-kstack
description: Write or update the repo's kstack config (.claude/kstack/config.md) - issue tracker, checks, verify skill, commit branch, decision rules, why sources and review model - by detecting what the repo has and asking only for what it cannot detect. Use for /setup-kstack, "set up kstack", or when cruise-control says the config is missing.
disable-model-invocation: true
---

# Setup kstack

Write `.claude/kstack/config.md` at the repo root (`git rev-parse --show-toplevel`). The cruise-control playbooks, `decide`, `why`, `review-plan` and `review-code` read it. The file is committed with the repo, so it works on every computer.

The config holds facts and pointers, never copies of rules. When the repo's `AGENTS.md` already says something (commit format, decision sizes, reference projects), the config line names that section: `AGENTS.md "Git"`. One source per fact.

## 1. Load

If the config exists, read it. Its values are the defaults, and a re-run keeps every line the user does not change.

## 2. Detect

Run these checks. Use reads only.

| Fact | How to find it |
|---|---|
| `AGENTS.md` sections | `grep -n '^##' AGENTS.md`, and the `AGENTS.md` files under it. Note sections about git, commands, testing, decisions, references, reviews and anti-patterns. |
| Tracker | In this order, the first that names one wins: a tracker named in `AGENTS.md`; issue keys in `git log --oneline -200` (for example `ABC-12`); the Linear MCP (a `list_teams` call; write the team key, not its display name); GitHub Issues (`gh issue list --limit 1` with a GitHub remote). |
| Checks | The command `AGENTS.md` says to run before a task is done, else `package.json` scripts (`check`, `test`, `lint`, `typecheck`), a `Makefile`, `justfile`, `pyproject.toml` or `Cargo.toml`. A version manager step (`.nvmrc`, `.tool-versions`) goes first. |
| Verify | `.claude/skills/verify-*`. Else the dev command and how to drive the app. |
| Branch | What `AGENTS.md` says about branches, else `git symbolic-ref --short refs/remotes/origin/HEAD`. Never a worktree's branch. |
| Docs | `VISION.md`, `ARCHITECTURE.md`, `DECISIONS.md`, `docs/**`: the decisions log, and the docs that change with it. |
| Why sources | A source of truth that `AGENTS.md` names for a design (a gist, a spec, a reference project). |
| Review | `command -v opencode`, and whether `~/.config/opencode/agents/kstack-adversary.md` exists. |

## 3. Draft

Fill in this shape. Leave out a line that does not apply. Use `none` when the repo has no such thing.

```markdown
# kstack config
Written by /kstack:setup-kstack. Re-run it to update.

## Tracker
- Kind: <linear | github | none | other>
- Key: <pattern, such as ABC-N> · <team, project or repo>
- Read: <the tool or command that reads an issue, and what to read with it>. Criteria: <the issue's acceptance list, such as "Done when">
- Start: <what to do when the build starts, such as status "In Progress">
- Finish: <what to do on ship, such as a comment, then status "Done">

## Checks
- `<the command that must pass before a task is done>`

## Verify
- Skill: <verify-* skill>. Fallback: <how to run and drive the app>

## Commit
- Branch: <branch> · Format: <AGENTS.md section, or the format>

## Decisions
- Extra sizes: <AGENTS.md section that adds big-decision rules, or none>
- References: <AGENTS.md section with reference projects, or none>
- Log: <the decisions log to update, and docs that change with it, or none>

## Why sources
- <sources of truth beyond git, the tracker and in-repo docs, or none>

## Review
- opencode: `-m <provider/model> --variant <effort>`
- Extra smells: <AGENTS.md section with the repo's own smells, or none>
```

For a tracker of kind `other` or `github`, the Read, Start and Finish lines hold the exact commands (for example `gh issue view <N> --comments`). The playbooks run what these lines say.

## 4. Confirm

Show the draft. Mark each line you could not detect and that the existing config does not hold. Ask about those with `AskUserQuestion`, one question per fact, with the detected candidates as options, or the template's example when there are none (the review model, the tracker's Start and Finish). Do not ask about the other lines, and the user can change any line in the same answer.

## 5. Write

Write the whole file (`mkdir -p .claude/kstack` first). Overwrite it, so re-runs stay idempotent. Do not commit it: the user commits it with their next change.

Then report in a few lines:
- the path, and each line that changed on a re-run;
- `opencode` missing: the GPT reviewer will fail until it is installed;
- the `kstack-adversary` link missing: give the user this command to run (do not run it, because it writes to their opencode config):

  ```bash
  ln -s <kstack>/opencode/agents/kstack-adversary.md ~/.config/opencode/agents/kstack-adversary.md
  ```

  `<kstack>` is the plugin root, two folders above `${CLAUDE_SKILL_DIR}`. Use the marketplace source folder when you know it, because the plugin cache moves on each update.
