# Market Data — Product layer

Product-level planning lives here. Per-repo SDD + harness stay in each project (`work/feature_list.json`, `specs/`).

| Layer | Location | Unit |
|-------|----------|------|
| Epic | `product/epics/` | Cross-repo outcome (1..N features **per** repo) |
| Feature | `<repo>/work/feature_list.json` | One repo, one harness workflow |
| Release | `product/releases/` | Product cut + per-repo version pins |
| Maturity | `product/capability-maturity.md` | Cross-repo capability scores (product + engineering) |
| Knowledge | `product/knowledge/`, `product/*-architecture.md` | Long-lived design notes |

**Rules:**

- Start work with `Start Feature <slug>` inside the target repo. Epics only group and prioritize.
- Releases group Features into a shippable cut. Epic Features tables include a **Release** column for glanceability; the release file is SoT for the cut.
- Maturity scores are refreshed from `work/` + epics/releases evidence; they do not replace Features or Releases.
- **Isolation:** BE, FE, and Brand do not document sibling repos, `shared/`, or workspace topology. Only `product` and `shared` know how the workspace integrates (see `CREATE_FEATURE.md` Repo isolation and [`knowledge/workspace-integration.md`](knowledge/workspace-integration.md)).

## Agents (portable)

| Agent | Command | Playbook |
|-------|---------|----------|
| `agents/epic-writer.md` | `create epic {idea}` | `CREATE_EPIC.md` |
| `agents/feature-writer.md` | `create features for epic {slug}` | `CREATE_FEATURE.md` |
| `agents/release-writer.md` | `create release {version or idea}` | `CREATE_RELEASE.md` |
| `agents/maturity-writer.md` | `update maturity` | `UPDATE_MATURITY.md` |

Install and IDE wiring: [`deployment.md`](deployment.md). Same philosophy as `harness/agents/` — markdown roles + playbooks; any IDE or agent host is packaging only.

See also `epics/index.json`, `releases/index.json`, and [`capability-maturity.md`](capability-maturity.md).
