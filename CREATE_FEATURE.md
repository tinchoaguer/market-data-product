# Create Feature (Market Data product)

Use this when the user asks to create or update a **harness Feature**.

Features live in each adopting repo’s `work/feature_list.json`. They are the executable unit of SDD. Creating a Feature does **not** run the Orchestrator and does **not** invent `specs/<slug>/` — Spec Writer produces specs after `Start Feature <slug>`.

---

## Model

```text
Epic (product/)  →  1..N Features per repo  →  Release (product cut)  →  each Feature = one harness cycle
```

| | Epic | Feature | Release |
|--|------|---------|---------|
| Where | `product/epics/<slug>.md` + `index.json` | `<repo>/work/feature_list.json` | `product/releases/` |
| Scope | Product outcome; may span repos | **Single** PROJECT_ROOT | Cross-repo ship unit |
| Count | 1..N features **per** repo | One slug, one workflow | Subset or whole epic(s) |
| Execution | Planning only | `Start Feature <slug>` in that PROJECT_ROOT | Docs + pins only |

**Never** implement cross-repo work as one Feature. Split by repo; link via Epic and/or description. Ship cuts: `CREATE_RELEASE.md`.

---

## Workspace folders (repos)

Open via `market-data-workspace.code-workspace`. Use these names when choosing a Feature’s PROJECT_ROOT (Epic Features table **Repo** column, or a direct `create feature` target):

| Folder | Role | Has `feature_list.json`? |
|--------|------|--------------------------|
| `market-data-be` | Backend API + analysis/classification | Yes — PROJECT_ROOT |
| `market-data-fe` | Frontend app | Yes — PROJECT_ROOT |
| `market-data-brand` | Design system, tokens, UI kit, wording | Yes — PROJECT_ROOT |
| `shared` | Cross-repo contracts (e.g. `openapi.json`) | **No** today — bootstrap before Features |
| `product` | Epics + product architecture notes | No — planning only; never add Features here |
| `harness` | Reusable SDD governance + agents | No — never add Features here |

Only folders marked Yes (or newly adopted per `harness/governance/deployment.md`) may receive a Feature entry.


### Product layer (`product/`)

| Artifact | Purpose |
|----------|---------|
| `CREATE_EPIC.md` | How to create/update Epics |
| `CREATE_FEATURE.md` | This file |
| `CREATE_RELEASE.md` | How to create/update Releases |
| `agents/epic-writer.md` | Agent role: `create epic {idea}` |
| `agents/feature-writer.md` | Agent role: `create features for epic {slug}` |
| `agents/release-writer.md` | Agent role: `create release {version or idea}` |
| `deployment.md` | Install agents (any IDE / agent host) |
| `README.md` | Product vs Feature vs Release layers |
| `epics/index.json` | Epic registry |
| `epics/<slug>.md` | Epic body (outcome, feature table with Release column, acceptance) |
| `releases/index.json` | Release registry |
| `releases/<version>.md` | Release body (features in cut, repo pins) |
| `knowledge/`, `*-architecture.md` | Long-lived design notes (optional Epic/Release link) |

### Harness (`harness/`) — authority for workflow

| Artifact | Purpose |
|----------|---------|
| `governance/AGENTS.md` | Harness vs project layout, agent roles |
| `governance/workflow.md` | Feature lifecycle stages |
| `governance/protocol.md` | Collaboration rules |
| `governance/schemas.md` | `feature_list`, progress, history schemas |
| `governance/spec_guidelines.md` | Spec file contract |
| `governance/human_gates.md` | Human approval recording |
| `governance/deployment.md` | How a repo adopts the harness |
| `agents/*.md` | Orchestrator, Spec Writer, Implementer, Reviewer |

Each adopting repo points here via `HARNESS.md` → `../harness/governance/`.

### Per adopting PROJECT_ROOT

```text
<PROJECT_ROOT>/
  HARNESS.md                 # governance pointer
  knowledge/
    architecture.md          # writable layout, module map
    conventions.md
    adr/                     # decisions
    writing/                 # when present: copy rules (e.g. brand, fe)
  work/
    feature_list.json        # ← Features are created HERE
    progress/current.md      # active harness state (Orchestrator)
    progress/reviews/        # optional review copies
    history/<slug>.md        # completed features
  specs/
    _template/               # Spec Writer starting point
    <slug>/                  # created by Spec Writer after Start Feature
      requirements.md
      design.md
      tasks.md
  <source code>              # only Implementer modifies (per architecture.md)
```

### Shared contracts (`shared/`)

Today: `openapi.json` (API contract snapshot). Epics may propose `shared` Features; if so, adopt harness first (`harness/governance/deployment.md`), then add `work/feature_list.json`.

### Repo cheat-sheet (what Features usually touch)


| Repo | Typical Feature surface | Read before writing the entry |
|------|-------------------------|-------------------------------|
| `market-data-be` | HTTP DTOs, domain modules, providers, OpenAPI enums | `knowledge/architecture.md`, `conventions.md`, existing `specs/` |
| `market-data-fe` | Views, mappers, locales, layout; consumes brand + BE APIs | `knowledge/architecture.md`, `conventions.md`, `knowledge/writing/` |
| `market-data-brand` | Tokens, wording, UI kit, Brand Studio | `knowledge/architecture.md`, `knowledge/writing/*` |

---

## When to create a Feature

Create when:

- work is a vertically sliceable outcome in **one** repo, **or**
- an Epic lists a `Proposed` slug that must enter that repo’s backlog.

Do **not**:

- fold multi-repo work into one Feature;
- create Features inside `harness/` or `product/`;
- invent full `specs/<slug>/` while only adding the backlog entry;
- start Implementation without `Start Feature` / Spec Writer / human approval.

If the outcome spans repos and no Epic exists, prefer `CREATE_EPIC.md` first, then Features per row.

---

## Steps

1. Identify target repo(s). One Feature entry **per** repo that owns work.
2. If linking an Epic: read `product/epics/<slug>.md` and use its proposed slug/intent when sensible.
3. In that repo, read `work/feature_list.json` and pick next `id` (max + 1).
4. Choose `slug` (kebab-case, unique **in that repo**). Prefer Epic’s proposed slug if present.
5. Append a Feature entry (schema below). Status must be `Pending`.
6. Optionally update the Epic Features table: set Status from `Proposed` → `Pending`, confirm the slug, and preserve the **Release** column if already set (do not invent a release here — use `CREATE_RELEASE.md`).
7. Do **not** create `specs/<slug>/`, do **not** edit `work/progress/current.md`, do **not** run Orchestrator stages.
8. If the target folder has no harness layout yet (see Workspace folders table): stop and tell the user to adopt via `harness/governance/deployment.md` before adding Features.

Repeat steps 3–5 for each target repo.

---

## Feature entry schema (`work/feature_list.json`)

```json
{
  "id": <number>,
  "slug": "<kebab-case>",
  "title": "<short title>",
  "description": "<one or more sentences: intent in this repo only>",
  "acceptance": [
    "<observable, testable criterion>",
    "<…>"
  ],
  "status": "Pending"
}
```

Status values (harness): `Pending` | `In Progress` | `Done` | `Blocked`

Only Orchestrator (and human gates) move status after creation. New Features are always `Pending`.

---

## Quality bar

- **One repo, one outcome.** Description and acceptance stay inside that PROJECT_ROOT.
- **Acceptance is observable** — UI/API behavior, contracts, or artifacts — not “refactor X” alone.
- **Vertically sliceable** — Spec Writer can produce requirements/design/tasks without inventing other repos’ work.
- **Slug stable** — matches Epic table when linked; kebab-case; no reuse of Done slugs for different intent.
- **Cross-repo links** — mention dependent slugs/repos in `description` or Epic Notes; do not merge them.
- Prefer **replace over patch** when an Epic supersedes prior Features (note that in Epic / description).

---

## After creating the Feature(s)

Tell the user:

1. Repo path(s) and new `id` / `slug` for each `feature_list.json` entry.
2. Whether an Epic was updated (`Proposed` → `Pending`) or still needs linking.
3. Next step: open that repo as PROJECT_ROOT (or focus it in the multi-root workspace), start the Orchestrator, run `Start Feature <slug>` in the suggested Epic order.
4. Remind: Spec Writer owns `specs/<slug>/`; human approves before Implementation.

---

## Related commands

| Intent | Doc / action |
|--------|----------------|
| Group multi-repo outcome | `create epic {idea}` → `agents/epic-writer.md` / `CREATE_EPIC.md` |
| Add backlog from Epic | `create features for epic {slug}` → `agents/feature-writer.md` / this file |
| Group Features into a ship cut | `create release {version}` → `agents/release-writer.md` / `CREATE_RELEASE.md` |
| Execute Feature | `Start Feature <slug>` (Orchestrator) |
| Install agents | `product/deployment.md` |
| Harness rules | `harness/governance/*` via repo `HARNESS.md` |

Agent entrypoint: `product/agents/feature-writer.md`.
