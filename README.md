# Market Data — Product layer

Product-level planning lives here. Per-repo SDD + harness stay in each project (`work/feature_list.json`, `specs/`).

| Layer | Location | Unit |
|-------|----------|------|
| Epic | `product/epics/` | Cross-repo outcome (1..N features **per** repo) |
| Feature | `<repo>/work/feature_list.json` | One repo, one harness workflow |
| Release | `product/releases/` | Product cut + per-repo version pins |
| Knowledge | `product/knowledge/`, `product/*-architecture.md` | Long-lived design notes |

**Rules:**

- Start work with `Start Feature <slug>` inside the target repo. Epics only group and prioritize.
- Releases group Features into a shippable cut. Epic Features tables include a **Release** column for glanceability; the release file is SoT for the cut.

## Agents (portable)

| Agent | Command | Playbook |
|-------|---------|----------|
| `agents/epic-writer.md` | `create epic {idea}` | `CREATE_EPIC.md` |
| `agents/feature-writer.md` | `create features for epic {slug}` | `CREATE_FEATURE.md` |
| `agents/release-writer.md` | `create release {version or idea}` | `CREATE_RELEASE.md` |

Install and IDE wiring: [`deployment.md`](deployment.md). Same philosophy as `harness/agents/` — markdown roles + playbooks; any IDE or agent host is packaging only.

See also `epics/index.json` and `releases/index.json`.
