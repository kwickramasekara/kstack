# KStack

Personal engineering workflows for any repo: take an issue from ticket to a pushed commit, with skills to explore, decide, review and write. The principles come from [pstack](https://github.com/cursor/plugins/tree/main/pstack) by poteto (MIT). Most skills and the review agents are adapted from pstack by way of pwstack.

## Install

Install kstack in each repo that uses it (`--scope local`), not for your user. Run from the repo:

```bash
claude plugin marketplace add <path to kstack>
claude plugin install kstack@kstack --scope local
```

`--scope local` writes `enabledPlugins` to that repo's `.claude/settings.local.json`, so only that repo gets kstack. A Claude Code worktree under `<repo>/.claude/worktrees/` has its own local install.

Then set the repo up once:

```text
/kstack:setup-kstack
```

It writes `.claude/kstack/config.md`: the issue tracker, the checks command, how to verify the app, the commit branch, the decision rules and the review model. It detects what it can and asks for the rest. Commit the file, so the repo works the same on every computer. Re-run it to update the file.

## Update

A marketplace added from a local path loads kstack from that folder, not from a cached copy ([plugin loading](https://code.claude.com/docs/en/plugins/loading.md#in-place-and-copied-plugins)). Every repo and worktree that uses kstack picks up changes, uncommitted edits included, at its next session start or after `/reload-plugins`. You do not need to bump `version` or run `claude plugin update` for changes to load.

## Cruise control

`/kstack:cruise-control <goal>` is the front door. It reads the repo's config, matches the goal to a playbook or a skill, and applies the engineering principles on the way. With no config it stops and points to `/kstack:setup-kstack`.

```text
/kstack:cruise-control ABC-123
/kstack:cruise-control ship ABC-123
/kstack:cruise-control how does <feature> work?
```

| Playbook | Use when |
|---|---|
| Ticket | Start a ticket. Research, decisions, a plan reviewed by agents, then the build. No plan mode: the build starts when the decisions are answered and the plan review has no blocking or major finding left. |
| Ship | Checks, cleanup and `review-code`, then a test-it-yourself list. On "ship": commit to the configured branch, push, and close the issue. |

Playbooks live in `skills/cruise-control/playbooks/`. Bugs go to the Fix Root Causes principle and `tdd` until a debugging playbook exists.

Start ticket work with `/kstack:cruise-control <KEY>`. A plain "work on <KEY>" does not load the Ticket playbook, so the agent skips the research, decisions and plan review.

The principles live in `principles/`, one file each, with their triggers in `principles/INDEX.md`. Steer the agent with a principle's name: "apply prove it works, run the real import and show me the rows". `make-the-change-easy-first` and `use-libraries-as-documented` are ours; the rest are pstack's.

## Other skills

| Skill | Use when |
|---|---|
| `setup-kstack` | Once per repo, and when its tracker, checks or workflow change. |
| `decide` | Design choices. Sorts them into big, small and detail. Asks the user only about the big ones. |
| `review-plan` | Adversarial review of a plan. Ticket step 5 runs it. |
| `review-code` | Adversarial review of local changes. Ship runs it. |
| `how` | "How does X work?" Parallel explorers plus an explainer. Critique mode on request. |
| `why` | "Why is it built this way?" Cited evidence from git, GitHub, the tracker, in-repo docs and the sources the config names. |
| `blast-radius` | A change could break something beyond the diff. Proves safety by running code. |
| `code-cleanup` | Once, before you push a finished change. |
| `tdd` | A failing test first. |
| `typescript-best-practices` | Reading or editing `.ts` or `.tsx` files. It loads by itself on those files (`paths`). |
| `technical-writing` | Docs, RFCs, commit messages. |
| `unslop` | Cut AI tells from any writing. |
| `eli5` | Explain like I'm five. No argument: your last message. With one: that thing (`/kstack:eli5 the race condition`). Only you can run it. |

## Review

`review-plan` and `review-code` start the `review-lead` agent. It runs the reviewers in parallel, merges duplicate findings, tries to disprove each one, and returns only what survives. The main agent then decides each fix.

| Reviewer | Model | Plan | Code | Job |
|---|---|---|---|---|
| `rules-checker` | Haiku | yes | yes | `AGENTS.md` rules of the touched folders; code that rebuilds something that exists; names that ignore the codebase's own words |
| `gap-adversary` | Sonnet | yes | yes | Acceptance criteria, missing cases, wrong assumptions, tests that prove nothing |
| `principles-checker` | Sonnet | yes | yes | The principles in `principles/` whose trigger fits the work |
| `gpt-adversary` | GPT (opencode) | yes | yes | Tries to break the work. Code: correctness, what breaks outside the diff, data, races, performance, auth and injection, temporary code, failure handling. Plans: premise, assumptions, failure modes, contracts and operations, step order |
| `code-smells` | Sonnet | no | yes | Fowler's smells and the refactorings that remove them, plus the repo's own smells the config names |

The GPT reviewer runs the opencode `kstack-adversary` agent from `opencode/agents/kstack-adversary.md`, with the model from the repo's config. Its config denies edits, and its prompt keeps the rest read-only. It is a hidden primary agent: `opencode run --agent` can use it, and opencode's agent switcher does not show it. Do not make it a subagent: `opencode run` falls back to the default agent for those. Link it into opencode once per computer:

```bash
ln -s <path to kstack>/opencode/agents/kstack-adversary.md ~/.config/opencode/agents/kstack-adversary.md
```

## Needs

`gh`, and `opencode` with the `kstack-adversary` agent linked (see Review). The tracker's tool (for Linear, the Linear MCP), when the repo uses one.

## Copies

- `principles/` are pstack's `principle-*` skills in pwstack's folder format, plus the two of ours. `typescript-best-practices` is pstack's skill with kstack links, and its "Structured telemetry" row is rewritten as "Useful logs" (console logging is allowed). Update them from pstack by hand.
- `how`, `blast-radius`, `code-cleanup`, `tdd`, `technical-writing`, `unslop`, `eli5`, the review agents and the review skills are copies of the pwstack ones, made general. A fix in one copy must be made in the other by hand.
