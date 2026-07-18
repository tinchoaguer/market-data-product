# Market Data — Product layer

Product-level planning lives here. Per-repo SDD + harness stay in each project (`work/feature_list.json`, `specs/`).

| Layer | Location | Unit |
|-------|----------|------|
| Epic | `product/epics/` | Cross-repo outcome (1..N features **per** repo) |
| Feature | `<repo>/work/feature_list.json` | One repo, one harness workflow |
| Architecture | `product/*.md` (optional) | Long-lived design notes |

**Rule:** Start work with `Start Feature <slug>` inside the target repo. Epics only group and prioritize.

## Agents (portable)

| Agent | Command | Playbook |
|-------|---------|----------|
| `agents/epic-writer.md` | `create epic {idea}` | `CREATE_EPIC.md` |
| `agents/feature-writer.md` | `create features for epic {slug}` | `CREATE_FEATURE.md` |

Install and IDE wiring: [`deployment.md`](deployment.md). Same philosophy as `harness/agents/` — markdown roles + playbooks; Cursor (or any IDE) is packaging only.

See also `epics/index.json`.
