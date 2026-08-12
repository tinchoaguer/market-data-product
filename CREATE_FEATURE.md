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

**Never** implement cross-repo work as one Feature. Split by repo; link via Epic (and Release). Ship cuts: `CREATE_RELEASE.md`.

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

### Repo isolation (workspace rule)

`product` and `shared` are the only places that know the workspace topology and how repos integrate.

| Layer | Responsibility |
|-------|----------------|
| `market-data-be` / `fe` / `brand` | Isolated PROJECT_ROOTs. Specs, knowledge, and backlog describe **this repo only**. They may consume published packages (e.g. `@market-data/brand`) and network protocols (HTTP API). They must **not** document sibling folder paths, `shared/`, other product repos as Spec SoT, or Epic coordination. |
| `harness` | Agnostic SDD tooling. No product folder names or workspace trees. |
| `product` | Owns Epics, Releases, cross-Feature links, and integration policy. |
| `shared` | Owns cross-repo contract artifacts (e.g. OpenAPI snapshot). |

**Integration handoffs owned here (not in adopting-repo Specs):**

- Refreshing `shared/openapi.json` from the BE app OpenAPI schema after public DTO changes (release/ops).
- How FE obtains `@market-data/brand` for local workspace development (e.g. npm override / link). Adopting `market-data-fe` commits a **package** dependency (semver), not `file:../market-data-brand`.
- Epic Feature tables link BE/FE/Brand work; adopting-repo `feature_list` descriptions stay repo-local.

### Product layer (`product/`)

| Artifact | Purpose |
|----------|---------|
| `CREATE_EPIC.md` | How to create/update Epics |
| `CREATE_FEATURE.md` | This file |
| `CREATE_UX_EPIC.md` | UX Epics with `## UX sources` (visual-definitions handoff) |
| `CREATE_UX_FEATURE.md` | UX Features + adopted `knowledge/ux/*-contract.md` |
| `CREATE_RELEASE.md` | How to create/update Releases |
| `agents/epic-writer.md` | Agent role: `create epic {idea}` |
| `agents/feature-writer.md` | Agent role: `create features for epic {slug}` |
| `agents/ux-epic-writer.md` | Agent role: `create ux epic {idea}` |
| `agents/ux-feature-writer.md` | Agent role: `create ux features for epic {slug}` |
| `agents/release-writer.md` | Agent role: `create release {version or idea}` |
| `deployment.md` | Install agents (any IDE / agent host) |
| `README.md` | Product vs Feature vs Release layers |
| `epics/index.json` | Epic registry |
| `epics/<slug>.md` | Epic body (outcome, feature table with Release column, acceptance) |
| `releases/index.json` | Release registry |
| `releases/<version>.md` | Release body (features in cut, repo pins) |
| `knowledge/`, `*-architecture.md` | Long-lived design notes (optional Epic/Release link) |
| `knowledge/ux-definitions-handoff.md` | Policy: brand UX SoT → adopted contracts |

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

Cross-repo artifacts (contract snapshots, shared packages) live here. Epics may propose `shared` Features; if so, adopt harness first (`harness/governance/deployment.md`), then add `work/feature_list.json`.

Adopting-repo Features must not require creating or updating files outside their PROJECT_ROOT. Cross-repo contract refresh and local package linking are **product / shared** (or release) concerns — never Spec REQs/tasks of an adopting repo.

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
- an Epic lists a `Proposed` slug that must enter that repo’s backlog, **and**
- Epic **human-decision** and **dependency** gates allow that row (see below).

Do **not**:

- fold multi-repo work into one Feature;
- create Features inside `harness/` or `product/`;
- invent full `specs/<slug>/` while only adding the backlog entry;
- start Implementation without `Start Feature` / Spec Writer / human approval;
- create Features while Epic Open human decisions block `create features` (or block those slugs);
- create a dependent Feature while any hard **Depends on** Feature is not yet `Done` (unless the Epic’s BE→FE mock exception applies to that row).

If the outcome spans repos and no Epic exists, prefer `CREATE_EPIC.md` first, then Features per row.

---

## Gates before creating Features from an Epic

Authority for what Epics must declare: `CREATE_EPIC.md` (Human intervention + Feature dependencies). Feature Writer **enforces** those gates here.

### 1. Human decisions gate

1. Read `product/epics/<slug>.md` → `## Human decisions`.
2. If the section is missing on a new/updated Epic that should have it → **stop** and ask the user to complete the Epic (do not invent Resolutions).
3. If any row is `Open` and **Blocks** includes `create features` (whole epic) → **stop**. Report the Open decisions; create **zero** Feature entries.
4. If an Open row **Blocks** specific slugs only → skip those slugs; may create other eligible rows.
5. Only `Resolved` or `None` clears the gate for the blocked scope.

Do **not** mark human decisions Resolved yourself. Wait for explicit user confirmation.

### 2. Dependency gate (phased creation)

1. Read each Feature row’s **Depends on** (and `## Dependency notes`).
2. A row is **eligible** to create when every hard dependency slug is `Done` in the dependency’s repo `work/feature_list.json` (and publish notes are satisfied when stated — e.g. brand version published).
3. Rows with empty Depends on (or soft-only / `mock-ok` BE→FE per Epic) are eligible in this pass if the human gate allows.
4. Create **only** eligible `Proposed` rows in this run. Leave ineligible dependents as `Proposed` on the Epic.
5. After dependency Features reach `Done`, the user runs `create features for epic <slug>` again; then create the newly eligible dependents (expanding description/acceptance with knowledge of the shipped dependency — e.g. consume brand `^x.y.0`).

**Example (brand → FE):**

```text
Pass 1: create brand/dashboard-surface-kit only
        → Start Feature … → Done + publish 0.2.0
Pass 2: create fe/dashboard-app-chrome (Depends on: dashboard-surface-kit)
```

**Exception (BE → FE mocks):** If the Epic marks the FE row as mock-ok (Dependency notes / empty Depends on with note), FE may be created without waiting for BE Done. Brand → FE hard deps never use this exception.

### Direct `create feature` (no Epic)

If the user asks for a Feature without an Epic, still ask whether cross-repo deps or product decisions exist. Prefer creating/updating an Epic when the outcome spans repos or needs human decisions.

---

## Steps

1. Identify target repo(s). One Feature entry **per** repo that owns work.
2. If linking an Epic: read `product/epics/<slug>.md` and apply **Gates before creating Features** (human decisions + dependencies). Abort or partially create per those rules.
3. Among eligible rows only: use the Epic’s proposed slug/intent when sensible.
4. In that repo, read `work/feature_list.json` and pick next `id` (max + 1).
5. Choose `slug` (kebab-case, unique **in that repo**). Prefer Epic’s proposed slug if present.
6. Append a Feature entry (schema below). Status must be `Pending`.
7. Optionally update the Epic Features table: set Status from `Proposed` → `Pending`, confirm the slug, preserve **Depends on** and **Release** if already set (do not invent a release here — use `CREATE_RELEASE.md`).
8. Do **not** create `specs/<slug>/`, do **not** edit `work/progress/current.md`, do **not** run Orchestrator stages.
9. If the target folder has no harness layout yet (see Workspace folders table): stop and tell the user to adopt via `harness/governance/deployment.md` before adding Features.
10. Report which rows were created, which remain `Proposed` (waiting on deps or human decisions), and the next command when gates clear.

Repeat steps 4–6 for each eligible target repo / row.

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
  "status": "Pending",
  "bump_version": "minor"
}
```

Status values (harness): `Pending` | `In Progress` | `Done` | `Blocked`

`bump_version` is **required** on every new Feature: `major` | `minor` | `patch` | `none`.

| Value | Use when |
|-------|----------|
| `major` | Breaking change for package consumers |
| `minor` | Backward-compatible new capability |
| `patch` | Fix or small non-breaking change |
| `none` | No package publish for this Feature (docs-only, internal, or repo with no publish pipeline) |

Today only **market-data-brand** automates npm versioning from this field (merge to `main` → bump + `v*` tag → publish). BE/FE still set `bump_version` for consistency; use `none` unless a publish pipeline exists for that repo.

Only Orchestrator (and human gates) move status after creation. New Features are always `Pending`.

---

## Quality bar

- **One repo, one outcome.** Description and acceptance stay inside that PROJECT_ROOT.
- **PROJECT_ROOT only.** Description and acceptance must not require paths or deliverables outside that repo; cross-repo artifacts are product/shared or release handoffs.
- **Acceptance is observable** — UI/API behavior, contracts, or artifacts **in that repo** — not “refactor X” alone.
- **Vertically sliceable** — Spec Writer can produce requirements/design/tasks without inventing other repos’ work (`harness/governance/spec_guidelines.md` Feature scope).
- **Slug stable** — matches Epic table when linked; kebab-case; no reuse of Done slugs for different intent.
- **Cross-repo links** — live in the Epic Features table (and Release), not in adopting-repo descriptions, Specs, or knowledge.
- **Gates honored** — no backlog entry while blocked by Open human decisions or unfinished hard Depends on (except documented BE→FE mock-ok).
- Prefer **replace over patch** when an Epic supersedes prior Features (note that in the Epic).

---

## After creating the Feature(s)

Tell the user:

1. Repo path(s) and new `id` / `slug` for each `feature_list.json` entry created in this pass.
2. Whether an Epic was updated (`Proposed` → `Pending`) or still needs linking.
3. Which Epic rows remain `Proposed` and **why** (Open human decision vs waiting on Depends on Done / publish).
4. Next step for created Features: open that repo as PROJECT_ROOT, start the Orchestrator, run `Start Feature <slug>` in dependency + Order sequence.
5. Remind: Spec Writer owns `specs/<slug>/`; human approves before Implementation.
6. If dependents were held back: after dependency Features are `Done`, re-run `create features for epic <slug>`.

---

## Related commands

| Intent | Doc / action |
|--------|----------------|
| Group multi-repo outcome | `create epic {idea}` → `agents/epic-writer.md` / `CREATE_EPIC.md` |
| Add backlog from Epic | `create features for epic {slug}` → `agents/feature-writer.md` / this file |
| UX outcome (adopt visual-definitions) | `create ux epic` / `create ux features for epic` → `CREATE_UX_EPIC.md` / `CREATE_UX_FEATURE.md` ([`knowledge/ux-definitions-handoff.md`](knowledge/ux-definitions-handoff.md)) |
| Group Features into a ship cut | `create release {version}` → `agents/release-writer.md` / `CREATE_RELEASE.md` |
| Execute Feature | `Start Feature <slug>` (Orchestrator) |
| Install agents | `product/deployment.md` |
| Harness rules | `harness/governance/*` via repo `HARNESS.md` |

Agent entrypoint: `product/agents/feature-writer.md`.
