# Create Epic (Market Data product)

Use this when the user asks to create or update a **product Epic**.

Epics live in `product/epics/`. They complement per-repo SDD + harness. They do **not** replace `work/feature_list.json` or run the Orchestrator.

---

## Model

```text
Epic (product/)  →  1..N Features per repo  →  Release (product cut)  →  each Feature = one harness cycle
```

| | Epic | Feature | Release |
|--|------|---------|---------|
| Where | `product/epics/<slug>.md` + `index.json` | `<repo>/work/feature_list.json` | `product/releases/` |
| Scope | Product outcome; may span repos | Single repo | Cross-repo ship unit |
| Count | 1..N features **per** repo | One slug, one workflow | Subset or whole epic(s) |
| Execution | Planning only | `Start Feature <slug>` in that PROJECT_ROOT | Docs + pins only |

**Never** implement cross-repo work as one harness Feature.

Ship cuts and version pins: `CREATE_RELEASE.md`. Epic Features tables include a **Release** column; the release file is SoT for the cut.

---

## Workspace folders (repos)

Open via `market-data-workspace.code-workspace`. Use these names in Epic `repos:` and in the Features table **Repo** column:

| Folder | Role | May host harness Features? |
|--------|------|----------------------------|
| `market-data-be` | Backend API + analysis/classification | Yes — PROJECT_ROOT |
| `market-data-fe` | Frontend app | Yes — PROJECT_ROOT |
| `market-data-brand` | Design system, tokens, UI kit, wording | Yes — PROJECT_ROOT |
| `shared` | Cross-repo contracts (e.g. OpenAPI snapshot) | Only after harness adoption |
| `product` | Epics, releases, product architecture | No — planning only |
| `harness` | Reusable SDD governance + agents | No |

Epic `repos:` lists folders that participate in the outcome (including planning-only ones when relevant). Feature rows must target a folder that can host `work/feature_list.json` (or note that adoption is required first).

---

## When to create an Epic

Create when the outcome:

- spans more than one repo, **or**
- needs several Features in one repo under one product goal, **or**
- needs a durable product-level acceptance story.

Do **not** create an Epic for a tiny single-repo task — add a Feature there instead.

---

## Steps

1. Read `product/epics/index.json` and pick next `id`.
2. Choose `slug` (kebab-case, unique).
3. Write `product/epics/<slug>.md` using the template below.
4. Append entry to `product/epics/index.json`.
5. Identify **Feature dependencies** (cross-repo or same-repo) and fill the **Depends on** column. Empty means no dependency.
6. Identify **human decisions** required before Features can be created or started; add an Open row in `## Human decisions` for each (or write `None` under that section).
7. If Features already exist in repos, link their real slugs + statuses.
8. If Features are new: list **proposed** slugs under Features; tell the user they must be added to each repo’s `feature_list.json` before `Start Feature` — and that Feature Writer will only create rows whose dependencies and human gates allow it (see `CREATE_FEATURE.md`).
9. Do **not** invent Feature specs (`specs/`) from the Epic alone — Spec Writer runs per Feature after harness start.
10. Optional: link architecture notes under `product/knowledge/` or `product/*-architecture.md`.
11. Optional: assign Features to a Release (`CREATE_RELEASE.md`) and set the **Release** column.

---

## Human intervention (required when applicable)

The Epic is the SoT for product decisions the agent must **not** invent. Document every pending decision under `## Human decisions`.

### When human intervention is required

Create an **Open** human-decision row when any of the following is true and unresolved:

| Trigger | Examples |
|---------|----------|
| Product / IA conflict | Mockup conflicts with adopted UX contracts (e.g. Overall Score vs no-Synthesis rule); competing navigation models |
| Scope / fidelity tradeoff | Pixel-perfect vs brand tokens; omit vs reinterpret a mockup band; optional chrome (breadcrumb) |
| Cross-repo contract choice | Whether FE waits for a published brand version vs temporary local packaging; whether to refresh `shared/openapi.json` in this cut |
| Dependency strategy | Whether a BE→FE wait is real or may be broken with mocks (see exception below) |
| Acceptance ambiguity | Product acceptance checkboxes cannot be judged without a human call on “good enough” vs residual gap epic |
| Drop / supersede | Dropping a Feature row, reopening a Done slug, or replacing an Epic outcome |

### When human intervention is **not** required

- Vertically slicing an agreed outcome into Proposed Feature rows (agent may propose; human may later adjust).
- Filling **Depends on** from obvious package/API publish order (agent must still **record** the dependency).
- Syncing Feature Status from each repo’s `feature_list.json`.
- Routine Epic Notes (risks, links) that do not block Feature creation.

### Human decisions rules

- Section `## Human decisions` is **mandatory** on every Epic. Use a table, or a single line `None` if nothing is pending.
- Status values: `Open` | `Resolved`.
- Every `Open` row must name what it **Blocks** (`create features` for the epic, specific slugs, or `Start Feature <slug>`).
- Feature Writer (`CREATE_FEATURE.md` / `CREATE_UX_FEATURE.md`) **must not** append `feature_list` entries while any Open decision blocks `create features` (or blocks those slugs).
- Resolving a decision is a **human** (or explicit human-approved) edit: set Status to `Resolved` and record the chosen option in **Resolution**. Agents may draft options; they must not mark Resolved without clear user confirmation.

---

## Feature dependencies

When Feature B cannot be specified/implemented correctly until Feature A has shipped (or published), the Epic **must** say so.

### How to record

- Features table column **Depends on**: empty, or one or more Epic Feature slugs (same epic), optionally qualified as `repo/slug` when useful. Multiple deps: comma-separated.
- Optional `## Dependency notes` for publish/version semantics (e.g. “FE rows wait until brand Feature is Done and `@maguer/market-data-brand` minor is published”).
- **Order** alone is not enough: if there is a hard dependency, **Depends on** must be filled even when Order already implies sequence.

### Hard vs soft

| Kind | Meaning | Feature creation |
|------|---------|------------------|
| **Hard** | Consumer needs a shipped artifact from the dependency (published package version, stable API shape already in the cut’s SoT, adopted contract only after upstream Feature Done, etc.) | Create **only** dependency Feature(s) first. Dependents stay `Proposed` until every listed dependency is `Done` in that repo’s `feature_list.json` (and any publish note is satisfied). Then create dependents. |
| **Soft / parallel** | Ordering preference only; no shipped artifact required | May create in the same `create features` pass; Order still guides `Start Feature`. |

### Exception: backend → frontend via mocks

A **BE → FE** dependency **may** be treated as soft (create FE Feature without waiting for BE Done) when the FE Feature can be specified and implemented against **mocks** / contract stubs that match the intended API shape.

Rules for the exception:

- Epic must state the mock strategy in **Depends on** (e.g. leave empty and note `mock-ok`) **or** in `## Dependency notes` / the human-decision Resolution.
- If the team instead requires real BE before FE, record it as a **hard** Depends on (and optionally an Open human decision until chosen).
- This exception does **not** apply to **brand → FE** (or other published-package) hard deps: FE must not invent a parallel design system while waiting; create brand first, then FE after Done + version.

### Typical hard chains in this workspace

- `market-data-brand` Feature (bump/publish) → `market-data-fe` Features that consume the new kit.
- Same-repo Feature A → Feature B when B’s acceptance assumes A’s surface already exists (record Depends on; create A first).

---

## Template (`product/epics/<slug>.md`)

```markdown
---
id: <number>
slug: <kebab-case>
title: <short title>
outcome: <one sentence product outcome>
status: Planned | Active | Done | Dropped
retrospective: false
repos: [<market-data-be|market-data-fe|market-data-brand|shared|product>, ...]
architecture: <optional relative path to product/knowledge/*.md or product/*.md>
---

# <Title>

## Outcome

<2–4 lines. Value for the product, not implementation detail.>

## Features

| Order | Repo | Slug | Intent | Depends on | Status | Release |
|------:|------|------|--------|------------|--------|---------|
| 1 | market-data-brand | kit-slug | … | | Proposed | |
| 2 | market-data-fe | ui-slug | … | kit-slug | Proposed | |

Status values for linked features: `Pending` | `In Progress` | `Done` | `Blocked` | `Proposed`
(Use `Proposed` until the slug exists in that repo’s feature_list.json.)

**Depends on**: empty, or epic Feature slug(s) that must be `Done` before this row may leave `Proposed` via Feature Writer. Use `## Dependency notes` for publish/version / mock-ok detail.

**Release** column: product cut version (e.g. `0.2.0`) or empty if unassigned. SoT for the cut is `product/releases/<version>.md`.

## Human decisions

None

<!-- Or, when intervention is needed:
| ID | Decision | Options | Status | Blocks | Resolution |
|----|----------|---------|--------|--------|------------|
| H1 | <question the agent must not invent> | <A / B / …> | Open | create features | |
-->

## Dependency notes

<optional: hard vs soft, publish version expectations, BE→FE mock-ok>

## Acceptance (product)

- [ ] <observable cross-cutting outcomes>

## Out of scope

- <bullets>

## Notes

<optional: dependencies on other epics, supersedes, risks>
```
---

## `index.json` entry

```json
{
  "id": <number>,
  "slug": "<slug>",
  "title": "<title>",
  "status": "Planned",
  "retrospective": false
}
```

Keep index in sync when epic status changes.

---

## Status rules

| Epic status | Meaning |
|-------------|---------|
| Planned | Agreed, Features not started (or only Proposed) |
| Active | At least one linked Feature In Progress |
| Done | All linked Features Done (or consciously Dropped) |
| Dropped | Epic abandoned |

Retrospective epics: `retrospective: true`, usually `status: Done`, Features already shipped.

---

## Quality bar

- Outcome is product language, not “refactor X”.
- Features are vertically sliceable; each can be specified and reviewed alone.
- Same repo may appear many times (multiple Features).
- Hard dependencies are explicit in **Depends on** (not only Order).
- `## Human decisions` is present (`None` or Open/Resolved rows); Open rows that block Feature creation are visible to Feature Writer.
- Prefer replace over patch when direction changes (note superseded epics).
- Keep the epic file brief; put deep design in `product/knowledge/` or `product/<topic>-architecture.md`.
- When Features belong to a ship cut, keep **Release** aligned with `product/releases/`.

---

## After creating the Epic

Tell the user:

1. Path of the new epic file.
2. Which Feature slugs are `Proposed` vs already in `feature_list.json`.
3. Which **Depends on** chains exist (what will be created first vs held as Proposed).
4. Which **Human decisions** are Open (and what they block). If any Open decision blocks `create features`, say so explicitly — do not imply Feature Writer can run yet.
5. Next step when gates allow: run `create features for epic <slug>` (feature-writer), then optionally `create release {version}` to assign the cut, then `Start Feature <slug>` in each PROJECT_ROOT in dependency + Order sequence.

If the outcome must adopt brand `visual-definitions` into an app repo, use **`create ux epic`** / [`CREATE_UX_EPIC.md`](CREATE_UX_EPIC.md) instead (see [`knowledge/ux-definitions-handoff.md`](knowledge/ux-definitions-handoff.md)).

Agent entrypoint: `product/agents/epic-writer.md` (see `product/deployment.md`).
