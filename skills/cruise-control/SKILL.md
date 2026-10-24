---
name: cruise-control
description: Route a task to its playbook (Ticket, Ship) or skill, and apply the engineering principles. Needs the repo's kstack config. Use for /cruise-control <goal>, a ticket key, or "ship".
argument-hint: <goal, ticket key, or "ship <key>">
disable-model-invocation: true
---

# Cruise control

Task: $ARGUMENTS

This skill's directory is `${CLAUDE_SKILL_DIR}` (the folder that holds this file). Playbooks call it `<cruise-control>`. Paths below are relative to it. `<kstack>` is the plugin root, two folders above it.

## 0. Config

Read `.claude/kstack/config.md` at the repo root (`git rev-parse --show-toplevel`). It holds the repo's facts: tracker, checks, verify, commit branch, decision rules, review model. The playbooks say "the config" for this file.

No config, or an empty one: stop. Say in one line that this repo has no kstack config and that `/kstack:setup-kstack` writes it. Do nothing else.

## Route

1. Match the task to one route in the table. When two routes fit, ask the user which one.
2. For a playbook: tell the user which one you use, in one line, before anything else: `**Playbook: <name>** (<what in the task matched its route>)`. Then read it in full. Open the task list with the playbook's steps as the first items, before any task-specific items. A step you skip stays in the list with `skip: <reason>`. Name the playbook again each time you start one, including when one playbook hands off to another (Ticket to Ship).
3. For a skill: run it.
4. No route fits: say so in one line, then do the work under the principles in `<kstack>/principles/INDEX.md`. Do not invent a playbook.

| The task | Route |
|---|---|
| A ticket to plan and build: a key in the config's tracker pattern ("work on ABC-N"), or, with tracker `none`, a feature goal | **Ticket** playbook: `playbooks/ticket.md` |
| The code is complete, or the user says "ship" | **Ship** playbook: `playbooks/ship.md` |
| "How does X work", "where should this live" | `how` skill |
| "Why is it built this way", a regression's history | `why` skill |
| "What could this break" | `blast-radius` skill |
| One or more design decisions outside a ticket | `decide` skill |
| A bug to fix (no playbook yet) | [Fix Root Causes](../../principles/fix-root-causes.md) and the `tdd` skill. Ship with the Ship playbook. |
| A code review of local changes | `review-code` skill |
| A review of a plan outside a ticket | `review-plan` skill |

## Non-negotiables

- Each playbook step lists the principles it needs. Read them when you reach that step. Outside a playbook, read the principles in `<kstack>/principles/INDEX.md` whose trigger fits the task before you start.
- Every code change: [Make the Change Easy First](../../principles/make-the-change-easy-first.md).
- Before you say "done": [Prove It Works](../../principles/prove-it-works.md).
- The user owns these gates: big decisions (`decide`), contested review findings that need a design choice (`decide`), and "ship". Take a clear default for small decisions, list it, and continue.
- The repo's `AGENTS.md` wins over this skill and its playbooks when they disagree.
- Prose for the user: the `unslop` skill. Docs and commit messages: the `technical-writing` skill.

## Principles

The principles and their triggers are in `<kstack>/principles/INDEX.md`. Read `<kstack>/principles/<name>.md` in full before you apply a principle.
