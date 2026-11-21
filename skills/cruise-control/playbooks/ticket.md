# Ticket

Take a ticket from the issue to a reviewed plan, then build it. The ticket is the key in the user's prompt, in the config's tracker pattern. With tracker `none`, the goal in the prompt is the ticket.

Apply [Make the Change Easy First](../../../principles/make-the-change-easy-first.md) at every step. Step 2 finds what makes the change hard, step 3 raises it, step 7 stops on shortcuts.

Follow the steps in order.

**No code edits before the gate.** Until the step 6 gate passes, write no file in the repo. The only files you may write are the plan file and scratch scripts under `$TMPDIR` (for example a `blast-radius` proof).

The user owns two gates in this playbook: big decisions (`decide`) and contested review findings that need a design choice. The plan has no approval step. Its readers are the review agents.

## 1. Read and restate

1. Read the issue the way the config's Tracker "Read" line says, with its comments, parent and related issues. Note its acceptance criteria (the "Criteria" the config names). With tracker `none`: take the goal from the prompt, and write the acceptance criteria yourself.
2. Read the root `AGENTS.md` and the `AGENTS.md` of each folder the issue touches, and the files they link for this kind of work.
3. Tell the user, in 3–5 plain lines: the problem, what "done" looks like, and what is out of scope. With tracker `none`, list the acceptance criteria you wrote. Ask only if the issue is unclear. Otherwise continue. The user interrupts if you got it wrong.

## 2. Ground

Read [Guard the Context Window](../../../principles/guard-the-context-window.md) first, in full.

Run these 5 in parallel subagents. The first 3 are kstack skills. Start each of those 3 as a `general-purpose` subagent (it can start its own subagents; `Explore` cannot) and tell it: "Read `<kstack>/skills/<name>/SKILL.md` and follow it. The repo's kstack config is `<absolute path>`. Return the skill's own output in full, with `file:line` evidence. Do not return raw file contents or command output." `<kstack>` is two folders above `<cruise-control>`.

- `how` skill: how the code that the issue touches works now.
- `why` skill: the reasons behind that code, from the sources the config names, and any design doc or plan for this work.
- `blast-radius` skill: what else reads or writes the data, contracts or files this change will touch.
- The `code-smells` agent (`kstack:code-smells`), with the repo folder and the files this change will touch (there is no diff yet): what makes this change harder than it should be? Name each smell, the refactoring that removes it, and how much of the change it makes simpler.
- Ticket check (a `general-purpose` subagent). Issues are often written by a model or in a hurry, so check this one. Facts: each number, limit or claim about how something works, in this repo or in an outside tool. Check each against its source (the code, the repo's design docs and decisions log, a measurement in the repo, or the tool's own docs). Choices: each scope, approach, limit or UI choice. Flag a choice only with evidence: it breaks a rule in `AGENTS.md` or the repo's design docs, a reference project had trouble with it, or a clearly simpler or cheaper way gives the same result. Return only the wrong facts and flagged choices, each with its evidence, or "none". Skip this one with tracker `none`.

## 3. Decisions

Read these principles first, in full: [Make the Change Easy First](../../../principles/make-the-change-easy-first.md), [Redesign From First Principles](../../../principles/redesign-from-first-principles.md), [Model the Domain](../../../principles/model-the-domain.md), [Foundational Thinking](../../../principles/foundational-thinking.md), [Experience First](../../../principles/experience-first.md). They apply to steps 3 and 4.

List all the decisions this ticket needs. Settle details yourself.

Always include "prepare first?" when step 2 found code that makes this change harder: refactor first, or build on the code as it is. Give your honest pick. "Build as it is" is the right pick only when the refactor does not make this change or the next likely ones simpler.

- The issue's choices are strong defaults, not settled decisions. Keep each one that step 2 did not flag, and say nothing about it. A mock-up is weaker: it sets behavior and layout, not pixels. Take colors, spacing and components from the repo and its UI library. Where they say nothing, research the common design practice.
- Each wrong fact or flagged choice: sort its fix like any decision. Small: one line in the defaults list, "The ticket says X; using Y (reason)". Big: a card that starts with "The ticket chose X".
- No decisions: write "No decisions needed:" and one line for the reason. Go to step 4.
- Otherwise, run `/kstack:decide` once, with all of them. It sorts them, researches them in one pass, and asks the user in rounds.

## 4. Plan

Write the plan to `~/.claude/plans/<KEY>-<short-name>.md` (with tracker `none`, a short name only). The opencode reviewer can read that folder.

Before you write **Approach** and **Changes**, read the docs for each library, framework, SDK or CLI the plan uses, under [Use Libraries as Documented](../../../principles/use-libraries-as-documented.md).

The readers are the review agents, not the user. Write for a reviewer who checks each claim against the code. Sections:

- **Problem**: 1–2 lines. What is wrong or missing, and why it matters.
- **Approach**: 2–3 lines. The chosen solution and the main idea behind it.
- **Out of scope**: one line each.
- **Acceptance criteria**: a table, one row per criterion: the criterion → the change that meets it → its test and the regression it catches, or "no test: <reason>".
- **Assumptions**: every fact the plan relies on, each with its evidence (`path:line`, a query, a doc). The ticket is not evidence: it may be agent-written and wrong. When the evidence contradicts the ticket, ask the user before you go on.
- **Decisions**: each one with the user's answer from `/kstack:decide`, including "prepare first?".
- **Changes**: per file or module, what changes.
- **New names**: each new type, function, field or route, and the existing codebase term it follows.
- **Steps**: the build order, per [Sequence Verifiable Units](../../../principles/sequence-verifiable-units.md). Each step ends in a check that passes. A "prepare first" refactor is the first step and keeps behavior.
- **Docs**: the docs and decisions log rows to update (the config's "Decisions" line).
- **Review**: filled in by `/kstack:review-plan`.

No diagrams, and no user test steps: the Ship playbook writes those.

**Too big:** more than about 15 files, or more than 5 steps that each change behavior. Stop and propose a split, each part with a title and its steps. The user creates the tickets or says "go on".

## 5. Review the plan

Run `/kstack:review-plan <KEY>: <title>` on the plan. It fixes the plan and fills in the **Review** section.

## 6. Gate

Build when all hold:

- `/kstack:decide` is answered, or step 3 needed no decisions.
- `/kstack:review-plan` left no blocking or major finding open.
- The plan is not too big, or the user said go on.

A contested finding stops for the user only when it needs a design choice: run `/kstack:decide`. Otherwise settle it yourself with evidence (read the code, run a command) and continue.

A blocking or major finding still open after the second review round: stop and tell the user in two lines.

When the gate passes, post a 3-line summary in chat, then go straight to step 7. It is for skimming, not approval. Do not wait for a reply.

```markdown
**Problem:** <one line>
**Approach:** <one line>
**Steps:** <step names, in order>
```

## 7. Build

Read these principles first, in full: [Test Behavior, Not Implementation](../../../principles/test-behavior-not-implementation.md), [Laziness Protocol](../../../principles/laziness-protocol.md), [Sequence Verifiable Units](../../../principles/sequence-verifiable-units.md), [Use Libraries as Documented](../../../principles/use-libraries-as-documented.md), [Fix Root Causes](../../../principles/fix-root-causes.md), [Attack the Premise](../../../principles/attack-the-premise.md). In TypeScript code, also follow the `typescript-best-practices` skill.

1. Before any code, do what the config's Tracker "Start" line says. Read the status back, and report only a status you read. If it fails, tell the user the error and go on.
2. Build one step at a time, to the plan, test first where the repo's `AGENTS.md` says so (Red → Green → Refactor). After each step, run the config's checks. Fix a failure before the next step.
3. Do not commit. The Ship playbook commits on "ship".
4. A new decision comes up: a small one gets a clear default, added to a "Defaults I used" list. A big one: stop and run `/kstack:decide`.
5. The plan turns out wrong (the same workaround in many places, `any`, forced casts, a type that lies): stop and tell the user in two lines. Do not patch around it. A shortcut that makes the codebase worse is a decision for the user, not a detail.
6. Something breaks that the plan does not cover (a test or feature outside this ticket, data, the build): stop and tell the user in two lines. Keep a list of everything you find on the way. The Ship playbook puts it under "Heads up".
7. When the last step passes its check, run the [Ship](ship.md) playbook.
