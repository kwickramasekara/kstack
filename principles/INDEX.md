## Principles

Read `<name>.md` in this folder in full before you apply a principle. Each entry says when it applies.

**Core**

- **Make the Change Easy First** (`make-the-change-easy-first`). Every code change. Leave the code easier to change. Refactor first as its own step when the code is not ready. Raise every shortcut to the user.
- **Laziness Protocol** (`laziness-protocol`). Refactoring, sizing a diff, or tempted to add abstractions, layers, or signal threading. Bias to deletion and the smallest change that solves the problem.
- **Foundational Thinking** (`foundational-thinking`). Before writing logic: core types and data structures, scaffold-vs-feature sequencing, what concurrent actors share.
- **Redesign from First Principles** (`redesign-from-first-principles`). Integrating a new requirement into an existing design. Redesign as if it had been foundational from day one.
- **Attack the Premise** (`attack-the-premise`). Two or more fixes that share one premise have failed the same check. Write the premise down and test it directly before you write another fix that assumes it.
- **Subtract Before You Add** (`subtract-before-you-add`). Sequencing an addition, refactor, or rewrite. Remove dead weight first, then build on the simpler base.
- **Minimize Reader Load** (`minimize-reader-load`). Reviewing or shaping code that's hard to trace. Count layers and hidden state, collapse one-caller wrappers, shrink mutable scope.
- **Outcome-Oriented Execution** (`outcome-oriented-execution`). Planned rewrites and migrations with explicit phase boundaries. Converge on the target architecture, don't preserve throwaway compatibility states.
- **Experience First** (`experience-first`). Product, UX, or feature-scope tradeoffs. Choose the user's result over implementation convenience.
- **Exhaust the Design Space** (`exhaust-the-design-space`). A novel interaction or architectural decision with no precedent. Build 2-3 competing prototypes and compare before committing.
- **Build the Lever** (`build-the-lever`). Any non-trivial work. Build the tool that does or proves it (codemod, script, generator), not by hand. The tool is the artifact a reviewer reruns.

**Architecture**

- **Model the Domain** (`model-the-domain`). Writing stateful logic, or code that branches a lot or repeats a shape assumption across files. Encode the domain in a structure instead of scattered conditionals.
- **Boundary Discipline** (`boundary-discipline`). Wiring validation, error handling, or framework adapters. Guards at system boundaries, trust internal types, keep business logic pure.
- **Type System Discipline** (`type-system-discipline`). Designing types or a signature in any typed language. Make illegal states unrepresentable, brand primitives, parse external data at boundaries.
- **Make Operations Idempotent** (`make-operations-idempotent`). Designing commands, lifecycle steps, migrations, or loops that run amid crashes and retries. Converge to the same end state.
- **Migrate Callers Then Delete Legacy APIs** (`migrate-callers-then-delete-legacy-apis`). Introducing a new internal API while old callers exist. Migrate and delete in one wave.
- **Separate Before Serializing Shared State** (`separate-before-serializing-shared-state`). Concurrent actors might write the same file, branch, key, row, or object. Eliminate the sharing first.
- **Use Libraries as Documented** (`use-libraries-as-documented`). Planning or writing a call to a library, framework, SDK or CLI. Read the docs for the pinned version and use their canonical pattern.

**Verification**

- **Prove It Works** (`prove-it-works`). After a task, before declaring done. Verify against the real artifact, not a proxy or "it compiles".
- **Fix Root Causes** (`fix-root-causes`). Debugging. Trace each symptom back to the first place the system went wrong and fix it there. A guard is a fix only when the state it handles is legitimate.
- **Sequence Verifiable Units** (`sequence-verifiable-units`). Multi-step work (sweeps, migrations, runs of similar edits) and how you order commits. Break work into small units that each end in a check, and verify each before the next.
- **Test Behavior, Not Implementation** (`test-behavior-not-implementation`). Writing, changing, or keeping a test. Assert what the code's users can observe, so the test fails when that behavior breaks and keeps passing when only the internals change.
- **Explain the Number** (`explain-the-number`). Before you trust, report, or act on a number you measured. Find what limits it, and rule out that it measured something other than the work you think.

**Delegation**

- **Guard the Context Window** (`guard-the-context-window`). Large outputs, long files, repeated reads, fan-out planning. Route bulk to subagents, keep summaries in the main thread.

**Meta**

- **Encode Lessons in Structure** (`encode-lessons-in-structure`). You catch yourself writing the same instruction a second time. Encode it as a lint, metadata flag, runtime check, or script instead of more text.
