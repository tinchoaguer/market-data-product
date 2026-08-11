# Create UX Feature (Market Data product)

Use this when the user asks to create or refresh **harness Features** from a **UX Epic** — including materializing adopted UX contracts into the target PROJECT_ROOT.

This playbook is a **specialized sibling** of [`CREATE_FEATURE.md`](CREATE_FEATURE.md). Prefer `create features for epic` when there is no UX-definition handoff.

Policy: [`knowledge/ux-definitions-handoff.md`](knowledge/ux-definitions-handoff.md).  
UX Epics: [`CREATE_UX_EPIC.md`](CREATE_UX_EPIC.md).

Creating a UX Feature does **not** run the Orchestrator and does **not** invent `specs/<slug>/` — Spec Writer runs after `Start Feature <slug>`.

---

## Model

```text
UX Epic (## UX sources)
  →  read brand visual-definitions
  →  write PROJECT_ROOT/knowledge/ux/<topic>-contract.md
  →  append work/feature_list.json (Pending)
  →  Start Feature (harness; PROJECT_ROOT only)
```

---

## Relationship to CREATE_FEATURE.md

Reuse by **reference**:

| Concern | Authority |
|---------|-----------|
| Workspace folders / repo isolation | `CREATE_FEATURE.md` |
| Feature JSON schema, status values | `CREATE_FEATURE.md` |
| Quality bar for description/acceptance (repo-local, observable) | `CREATE_FEATURE.md` + this playbook’s UX rules |
| Materialize `knowledge/ux/*-contract.md` | **This playbook** |
| Next command after UX Epic | **`create ux features for epic`** (this playbook) |

---

## When to create UX Features

Create when:

- an Epic has a non-empty `## UX sources` table, **and**
- one or more Feature rows are `Proposed` (or the user asks to refresh adopted contracts for that epic).

Do **not**:

- use this playbook for Epics without UX sources (use `CREATE_FEATURE.md`);
- create Features inside `harness/` or `product/`;
- invent full `specs/<slug>/` while only adding the backlog entry;
- write HTTP/DTO field maps into the adopted contract (Spec Writer owns maps in `design.md`).

---

## Steps

1. Identify the Epic: read `product/epics/<slug>.md`.
2. **Require** `## UX sources` with at least one row for each PROJECT_ROOT that will receive a Feature. If missing or unusable → **stop** and ask (do not invent SoT).
3. For each target repo in the Epic Features table that you are creating/updating:
   1. Confirm harness adoption (`CREATE_FEATURE.md` workspace table).
   2. Match Epic Feature rows to `## UX sources` rows for that repo.
   3. Read brand SoT under `market-data-brand/knowledge/visual-definitions/` for those topics.
   4. **Write or update** `<PROJECT_ROOT>/knowledge/ux/<topic>-contract.md` (see Contract shape below).
   5. Read `work/feature_list.json`; pick next `id` if appending.
   6. Append or update Feature entry (`Pending` for new entries) with description/acceptance that reference the **local** adopted path and observable UI criteria.
4. Update Epic Features table: `Proposed` → `Pending`, confirm slugs; preserve **Release** if set.
5. Do **not** create `specs/<slug>/`, do **not** edit `work/progress/`, do **not** run Orchestrator stages.
6. Repeat for each target repo / Feature row in scope.

### Refresh-only mode

If the user asks to regenerate adopted contracts after brand SoT changed:

- Update `knowledge/ux/<topic>-contract.md` from current SoT + Epic UX sources.
- Do **not** duplicate Feature entries if slugs already exist; optionally tighten acceptance if the contract’s observable rules changed.
- Report which contracts were refreshed and whether In Progress Features may need Spec re-approval.

---

## Adopted contract shape

File: `<PROJECT_ROOT>/knowledge/ux/<topic>-contract.md`

Must be **self-contained** for Spec Writer. Include:

1. Purpose + topic name.
2. Region / unit map (role + question) relevant to the topic.
3. Hierarchy / viewport / attention rules that bind the UI.
4. Critical internal rules (e.g. Analysis: peer Assessments, no Overall/Synthesis).
5. Anti-patterns.
6. Optional Figma **URLs** (not sibling-repo file paths).
7. Footer: brand `visual-definitions` is canonical SoT; this file is adopted — regenerate via `create ux features for epic …`.

Must **not** include:

- Paths like `../market-data-brand/...`
- Full verbatim dump of every brand markdown file
- HTTP / DTO / OpenAPI field maps

---

## Feature entry schema

Same schema as `CREATE_FEATURE.md` (including required `bump_version`):

```json
{
  "id": <number>,
  "slug": "<kebab-case>",
  "title": "<short title>",
  "description": "<intent in this repo; cite knowledge/ux/<topic>-contract.md>",
  "acceptance": [
    "<observable UI / behavior criterion in this repo>",
    "<…>"
  ],
  "status": "Pending",
  "bump_version": "minor"
}
```

`bump_version`: `major` | `minor` | `patch` | `none` — see `CREATE_FEATURE.md`. Brand automates publish from this field; FE/BE use `none` unless a publish pipeline exists.

Acceptance must be testable inside PROJECT_ROOT. Prefer criteria that Spec Writer can turn into REQs using the adopted contract (structure, hierarchy, anti-patterns) without reading brand.

---

## Quality bar

- One Feature = one PROJECT_ROOT = one harness cycle.
- Adopted contract exists **before or with** the Feature entry.
- No sibling-repo paths in `feature_list` or adopted contract.
- Description/acceptance stay repo-local and observable.
- Data-map is **not** produced here — Spec Writer writes it in `specs/<slug>/design.md`.
- Prefer Epic proposed slugs; keep slugs stable and kebab-case.

---

## After creating the UX Feature(s)

Tell the user:

1. Repo path(s), new `id` / `slug` for each `feature_list.json` entry.
2. Paths of adopted contracts created or updated (`knowledge/ux/…`).
3. Whether the Epic table was updated (`Proposed` → `Pending`).
4. Next step: open that repo as PROJECT_ROOT, start Orchestrator, `Start Feature <slug>`.
5. Remind: Spec Writer owns `specs/<slug>/` (including any payload→Region map in `design.md`); human approves before Implementation.

Agent entrypoint: `product/agents/ux-feature-writer.md` (see `product/deployment.md`).
