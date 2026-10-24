# Ship

Finish a change: prove it, get the user's check, then on "ship" commit, push and close the issue.

Issue: the key from the prompt or the conversation. Work without one if there is none.

A change that works but leaves the code harder to change is not ready to ship without the user knowing it ([Make the Change Easy First](../../../principles/make-the-change-easy-first.md)).

The report in B is message text. Write the full report in your message before you call any tool that ends the turn, such as a wrap-up or next-step tool. A recap or a status line never replaces the report.

## A. Prove it (no user input)

Read these principles first, in full: [Prove It Works](../../../principles/prove-it-works.md), [Sequence Verifiable Units](../../../principles/sequence-verifiable-units.md), [Build the Lever](../../../principles/build-the-lever.md), [Fix Root Causes](../../../principles/fix-root-causes.md), [Attack the Premise](../../../principles/attack-the-premise.md).

The change is the uncommitted work in the repo, plus untracked files.

1. The config's checks pass. If a check fails, fix it or tell the user. Never skip a hook or use `--no-verify`.
2. Run `/kstack:code-cleanup` on the change.
3. Run `/kstack:review-code <KEY>: <title>` on the change. It covers code smells, `AGENTS.md` rules, rebuilt code, gaps, what breaks outside the diff and the main safety fact. Its debt goes under "Heads up". If you change code, run steps 1 and 2 again.
4. Run the feature the way a user would, as the config's "Verify" line says. Note anything you could not check.
5. Docs: the plan's **Docs** items are done, and each design decision the change made has its row in the config's decisions log.

## B. User check (stop and wait)

Send this, and keep it short. "Heads up" comes first and is never skipped. Write "Nothing" only when you checked every item below and found none.

Heads up lists, one line each, worst first:
- Something that is broken or fails now (a test, a feature, the build), even when it is outside this ticket or fails only sometimes.
- A plan item you did not do (a test, a file, a check).
- A change in behavior the user did not ask for, or a change from the plan.
- A risk you accepted, or a review finding you dismissed that the user may disagree with.
- A manual step: a new env var, a migration, a deploy setting, a data fix, or a check the repo's `AGENTS.md` asks the user to run before shipping (for example a paid live check).
- A new cost: a new dependency, paid API calls, more memory, disk or time.
- Debt this change adds: code that is now harder to change, a shortcut, a copy instead of a shared piece. Say what a fix would cost.
- Each default you took during the build without asking (the "Defaults I used" list).

```markdown
**Ready to ship <KEY>: <title>**

**Heads up:** <the items above, or "Nothing">
**What changed:** 3–5 bullets, plain words.
**Proved:** checks and what you ran. Review findings: one line each, fixed or dismissed with the reason.
**Test it yourself:**
1. <exact command or click>, and what you should see.
(5 steps or fewer)
**Not checked:** <anything you could not verify, or "nothing">

Say "ship" to commit, push and close the issue.
```

## C. On "ship"

1. The current branch is the config's branch. If it is not (a worktree, for example), stop and tell the user. `git status`. Stage only the files of this change, by name.
2. Commit on the config's branch, in the format the config's "Commit" line names.
3. Push that branch. Read `git status` after, and report only what it shows.
4. Do what the config's Tracker "Finish" line says. Read the status back before you report it. With tracker `none`, skip this.
5. Follow-up work found during the ticket: show the text to the user and ask where it goes (an existing issue or a new one).
