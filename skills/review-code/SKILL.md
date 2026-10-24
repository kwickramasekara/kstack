---
name: review-code
description: Adversarial review of a code change by five reviewers in parallel (Haiku rules and rebuilt-code check, Sonnet gap hunt, Sonnet principles check, GPT via opencode, Sonnet code smells), triaged by a review-lead agent that also proves the change's main safety fact. Then fix what survives. Use from the cruise-control Ship playbook, or when asked to review or attack local changes.
argument-hint: "<KEY>: <title>"
---

# Review code

Review: $ARGUMENTS

The reviewers attack the change. The `review-lead` agent merges their findings, challenges each one and proves the main safety fact. You get only its report. You fix what survives.

## 1. Start the review

The diff scope is "uncommitted changes", or "this branch against <base>" when the work is already committed.

Start the `review-lead` agent (shown as `kstack:review-lead`) with this prompt:

```text
Mode: code
Round: 1
Ticket: <KEY>: <title>, or the title alone with no tracker
Acceptance criteria: <the list from the issue or the plan, or "none: see the stated intent">
Stated intent: <what the change is for, when there are no acceptance criteria, or "none">
Principles: <the absolute path of ${CLAUDE_SKILL_DIR}/../../principles>
Config: <the absolute path of the repo's .claude/kstack/config.md>
Plan file: <absolute path, or "none">
Repo: <absolute path of the repo root>
Diff scope: <uncommitted changes | this branch against <base>>
```

Give only these facts. Do not add your reasoning or your opinion of the change.

It can take several minutes. The GPT reviewer runs opencode with the sandbox off, so the user approves that command.

## 2. Act on the report

- **A reviewer failed:** tell the user which one and why, in one line. Go on with the rest.
- **Confirmed:** fix it. Design the fix yourself and look for the simplest one (an existing pattern, a library, a smaller change). Use the fix direction only when it is the best one. Add a test only when the finding names a regression that the current tests miss. A fix that needs a big decision: run `/kstack:decide`.
- **Contested:** do the check that the report names. If it needs a product or design choice, run `/kstack:decide`.
- **Safety fact unproven:** prove it yourself, or report it as unproven.
- **Debt and Dismissed:** no change. Keep them for the report.

Run round 2 only when you fixed a blocking or major finding. Use a new `review-lead` with `Round: 2` and the list of findings you fixed. Two rounds at most.

## 3. Report

- One line per confirmed or contested finding: fixed, or not fixed and why.
- The safety fact and how it was proved.
- Debt, one line each, with what a fix would cost.
- One line with the dismissed count and the reasons in a few words. Name any dismissal the user may disagree with.
- Any reviewer that failed.
