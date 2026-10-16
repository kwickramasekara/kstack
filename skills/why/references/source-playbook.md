# Source playbooks

The why skill spawns one investigator per available evidence category, each reading the single playbook below that matches its category.

| Category | Playbook | Tool it documents |
|---|---|---|
| Source control history | [`code-archaeology.md`](./sources/code-archaeology.md) | git, `gh` |
| Issue / ticket tracker | [`linear.md`](./sources/linear.md) | Linear MCP |
| Long-form documents | [`docs.md`](./sources/docs.md) | in-repo docs |

A source that the repo's `AGENTS.md` names (for example a reference project or a gist that is the source of truth for a design) gets its own investigator. Give it `docs.md` as its playbook, pointed at that source.
