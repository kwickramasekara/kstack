# Long-form documents (in-repo)

## What this source contains

Most durable long-form context lives in the repo itself:

- `AGENTS.md` and `CLAUDE.md` files: conventions and the reasoning encoded with them
- Architecture and vision docs (for example `ARCHITECTURE.md`, `VISION.md`), and any decisions log in them
- `docs/**`: design docs, plans, notes
- READMEs next to the code they describe
- Sources the kstack config names under "Why sources" (a gist, a reference project)

Long-form "why" often lives here before it becomes code: problem statements, alternatives considered, constraints, and rejected approaches.

## How to search it

1. **Start at the indexes.** Read the root `AGENTS.md` for the docs it names. List a big doc's headings (`grep -n '^#' <file>`) before you read it.
2. **Check the decisions log first.** A decisions log row often states the decision, the rejected alternative and the reason in one line.
3. **Keyword search.** `rg -il '<feature name>'` over the docs with several phrasings: the feature name, key symbols, error strings, the issue ID.
4. **Read the whole section, not the hit line.** Rationale is often buried mid-document, in "alternatives considered" or "non-goals".
5. **Check the document's own git history.** A doc edit that landed with the code change often names the motivating PR or issue. Hand commit-level leads to the source-control investigator.

## What good evidence looks like here

- A decisions log row that matches the target
- A plan or spec with a problem statement that matches the target's purpose
- An "alternatives considered" or "rejected approaches" section
- A non-goal or principle that rules out the obvious alternative

## Common pitfalls

- **Outdated docs.** Docs are written before implementation and not always updated. Cross-check against the actual commit or PR.
- **Doc vs. reality drift.** A doc may say "we'll do X" while the code does Y. Flag the divergence; the synthesizer surfaces contradictions.
- **Unlinked docs.** The most relevant doc may not be linked from anywhere. Broad keyword searches help.

## What to return

For each relevant doc:
- Path and section
- The motivation text (verbatim quote), with its location
- Last-touched commit and date for that section (`git log -1 -- <path>`)
- Whether the doc reads as current or stale relative to the code's ship date
