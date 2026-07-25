# Product agents — deployment

## Purpose

How to wire Market Data **product** agents (Epic Writer, Feature Writer, UX Epic Writer, UX Feature Writer, Release Writer, Maturity Writer) into an IDE or agent host.

Behavior is defined by portable markdown:

- Playbooks: `CREATE_EPIC.md`, `CREATE_FEATURE.md`, `CREATE_UX_EPIC.md`, `CREATE_UX_FEATURE.md`, `CREATE_RELEASE.md`, `UPDATE_MATURITY.md`
- Role cards: `agents/*.md` (body)
- UX handoff policy: `knowledge/ux-definitions-handoff.md`

IDE-specific frontmatter and install paths are **packaging only**. Any agent host that can load a markdown system prompt can run these roles.

Product agents complement the AI Harness; they do not replace it. Execution remains `Start Feature <slug>` via the harness Orchestrator.

---

# Package layout

```text
product/
    CREATE_EPIC.md
    CREATE_FEATURE.md
    CREATE_UX_EPIC.md
    CREATE_UX_FEATURE.md
    CREATE_RELEASE.md
    UPDATE_MATURITY.md
    capability-maturity.md
    README.md
    deployment.md          # this file
    agents/
        epic-writer.md
        feature-writer.md
        ux-epic-writer.md
        ux-feature-writer.md
        release-writer.md
        maturity-writer.md
    epics/
    releases/
    knowledge/
    *-architecture.md
```

---

# User commands (portable)

| Command | Agent | Playbook |
|---------|--------|----------|
| `create epic {idea}` | epic-writer | `CREATE_EPIC.md` |
| `create features for epic {slug}` | feature-writer | `CREATE_FEATURE.md` |
| `create ux epic {idea}` | ux-epic-writer | `CREATE_UX_EPIC.md` |
| `create ux features for epic {slug}` | ux-feature-writer | `CREATE_UX_FEATURE.md` |
| `create release {version or idea}` | release-writer | `CREATE_RELEASE.md` |
| `update maturity` | maturity-writer | `UPDATE_MATURITY.md` |
| `Start Feature {slug}` | harness orchestrator | `harness/governance/workflow.md` |

When the UI outcome depends on brand `visual-definitions`, use the **UX** commands so adopted contracts land under `<repo>/knowledge/ux/` before the harness runs.

---

# Installing agents

Copy or symlink product agent role cards into the directory your agent host uses for custom agents (same pattern as harness):

```text
product/agents/epic-writer.md
product/agents/feature-writer.md
product/agents/ux-epic-writer.md
product/agents/ux-feature-writer.md
product/agents/release-writer.md
product/agents/maturity-writer.md
```

Reuse the harness installer if available:

```bash
# from harness/scripts/ (or equivalent)
./install-agents.sh ../../product/agents <host-agents-dir> symlink
```

```powershell
.\install-agents.ps1 -Source ..\..\product\agents -Destination <host-agents-dir> -Mode symlink
```

Optional YAML frontmatter (`name`, `description`, `model`, `readonly`) is for hosts that use an agent picker. Behavior is defined by the markdown body and playbooks. Do not put product SoT only in IDE-specific rules files.

---

# Invoking

1. Open a workspace that includes `product/` and the adopting repos (e.g. multi-root `market-data-workspace.code-workspace`, or an equivalent IDE workspace).
2. Load **epic-writer**, **feature-writer**, **ux-epic-writer**, **ux-feature-writer**, **release-writer**, or **maturity-writer** as the agent / custom-agent system prompt (ignore or strip YAML frontmatter if the host does not use it).
3. Run the matching user command above.

Agents resolve paths from the open workspace. No host-specific APIs are required for correct behavior.

---

# Relationship to harness

| Concern | Owner |
|---------|--------|
| Epic planning | product / epic-writer |
| UX Epic planning (visual-definitions handoff) | product / ux-epic-writer |
| Feature backlog entries | product / feature-writer → `<repo>/work/feature_list.json` |
| UX Feature + adopted `knowledge/ux/*-contract.md` | product / ux-feature-writer |
| Release cuts + version pins | product / release-writer → `product/releases/` |
| Capability maturity scores | product / maturity-writer → `product/capability-maturity.md` |
| Spec → Implement → Review | harness agents |
| Governance (workflow, schemas) | `harness/governance/` |

Install product agents **alongside** harness agents (`orchestrator`, `git-preflight`, `spec-writer`, `implementer`, `reviewer`).

---

# Upgrading

When playbooks or role cards change:

1. Pull updated `product/` files.
2. Refresh agent-host installs (re-copy or keep symlinks).
3. In-flight Epics/Features/Releases keep their artifacts; new commands use the updated playbooks.
