# KStack

Personal engineering skills for sandbox repos. Some skills are adapted from [pstack](https://github.com/cursor/plugins/tree/main/pstack) by poteto (MIT), by way of pwstack.

Repo workflows (ticket, decide, ship) stay in each repo's own `.claude/skills/`.

## Install

```bash
claude plugin marketplace add ~/code/sandbox/kstack
claude plugin install kstack@kstack --scope local   # run inside the repo that should use it
```

`--scope local` writes `enabledPlugins` to that repo's `.claude/settings.local.json`, so only that repo gets kstack.

## Update

The install is a cache pinned to a git commit and a version. Uncommitted edits do not reach it. To pick up changes, bump `version` in `.claude-plugin/plugin.json`, commit, then:

```bash
claude plugin marketplace update kstack
claude plugin update kstack@kstack
```

## Skills

| Skill | Use when |
|---|---|
| `how` | "How does X work?" Parallel explorers plus an explainer. Critique mode on request. |
| `why` | "Why is it built this way?" Cited evidence from git, GitHub, Linear, in-repo docs, and sources the repo's AGENTS.md names. |
| `blast-radius` | A change could break something beyond the diff. Proves safety by running code. |
| `code-cleanup` | Once, before you push a finished change. |
| `tdd` | A failing test first. |
| `technical-writing` | Docs, RFCs, commit messages. |
| `unslop` | Cut AI tells from any writing. |

`how`, `blast-radius`, `code-cleanup`, `tdd`, `technical-writing` and `unslop` are copies of the pwstack skills. A fix in one copy must be made in the other by hand.
