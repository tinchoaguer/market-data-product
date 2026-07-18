# Product agents — deployment

## Purpose

How to wire Market Data **product** agents (Epic Writer, Feature Writer) into an IDE.

Behavior is defined by portable markdown:

- Playbooks: `CREATE_EPIC.md`, `CREATE_FEATURE.md`
- Role cards: `agents/*.md` (body)

IDE-specific frontmatter and install paths are **packaging only**. Any agent host that can load a markdown system prompt can run these roles.

Product agents complement the AI Harness; they do not replace it. Execution remains `Start Feature <slug>` via the harness Orchestrator.

---

# Package layout

```text
product/
    CREATE_EPIC.md
    CREATE_FEATURE.md
    README.md
    deployment.md          # this file
    agents/
        epic-writer.md
        feature-writer.md
    epics/
    *-architecture.md
```

---

# User commands (portable)

| Command | Agent | Playbook |
|---------|--------|----------|
| `create epic {idea}` | epic-writer | `CREATE_EPIC.md` |
| `create features for epic {slug}` | feature-writer | `CREATE_FEATURE.md` |
| `Start Feature {slug}` | harness orchestrator | `harness/governance/workflow.md` |

---

# Cursor

## Install agents

Copy or symlink into the global Cursor agents directory (same pattern as harness):

```text
product/agents/epic-writer.md      → ~/.cursor/agents/epic-writer.md
product/agents/feature-writer.md   → ~/.cursor/agents/feature-writer.md
```

Reuse the harness installer if available:

```bash
# from harness/scripts/ (or equivalent)
./install-agents.sh ../../product/agents ~/.cursor/agents symlink
```

On Windows (PowerShell), symlink example:

```powershell
New-Item -ItemType SymbolicLink -Path "$env:USERPROFILE\.cursor\agents\epic-writer.md" -Target "<repo>\product\agents\epic-writer.md" -Force
New-Item -ItemType SymbolicLink -Path "$env:USERPROFILE\.cursor\agents\feature-writer.md" -Target "<repo>\product\agents\feature-writer.md" -Force
```

YAML frontmatter (`name`, `description`, `model`, `readonly`) is for Cursor’s agent picker. Do not put product SoT only in `.cursor/rules`.

## Invoke

1. Open the Market Data multi-root workspace (`market-data-workspace.code-workspace`) so `product/` and adopting repos are visible.
2. Select **epic-writer** or **feature-writer**.
3. Run the matching user command above.

---

# Other IDEs

1. Load `product/agents/epic-writer.md` or `feature-writer.md` as the agent / custom-agent system prompt (ignore or strip YAML frontmatter if the host does not use it).
2. Ensure the workspace includes `product/` and the target repos.
3. Use the same user commands; agents resolve paths from the open workspace.

No Cursor-only APIs are required for correct behavior.

---

# Relationship to harness

| Concern | Owner |
|---------|--------|
| Epic planning | product / epic-writer |
| Feature backlog entries | product / feature-writer → `<repo>/work/feature_list.json` |
| Spec → Implement → Review | harness agents |
| Governance (workflow, schemas) | `harness/governance/` |

Install product agents **alongside** harness agents (`orchestrator`, `spec-writer`, `implementer`, `reviewer`).

---

# Upgrading

When playbooks or role cards change:

1. Pull updated `product/` files.
2. Refresh IDE agent installs (re-copy or keep symlinks).
3. In-flight Epics/Features keep their artifacts; new commands use the updated playbooks.
