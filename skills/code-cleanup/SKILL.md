---
name: code-cleanup
description: Runs once before pushing code for a feature or bug fix is code-complete. Ensures the code is clean before it is pushed to remote.
---

Read AGENTS.md in the project again and look at all the changes you did with a fresh set of eyes - see if anything can be simplified or if there are any issues/gaps.

- Clean and simple solutions are preferred.
- Follow SOLID principles, Martin Fowler concepts and general technology stack related best practices.
- Comments should follow the instructions set in the AGENTS.md file and `unslop` skill.
- Broken AGENTS.md rules: check each change against them by intent, not only by words: "use `Match` over `switch`" covers a chain of ternaries too.

## Scope

- Lines the diff added, changed or rewrote are in scope.
- "It was already like that" is a reason to skip only for lines the diff did not change.
- When the diff rewrites a block, for example changes what every branch of a chain compares, the whole block must meet the AGENTS.md rules.
