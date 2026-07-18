# Create Epic (Market Data product)

Use this when the user asks to create or update a **product Epic**.

Epics live in `product/epics/`. They complement per-repo SDD + harness. They do **not** replace `work/feature_list.json` or run the Orchestrator.

---

## Model

```text
Epic (product/)  →  1..N Features per repo  →  each Feature = one harness cycle in that repo
```

| | Epic | Feature |
|--|------|---------|
| Where | `product/epics/<slug>.md` + `index.json` | `<repo>/work/feature_list.json` |
| Scope | Product outcome; may span repos | Single repo |
| Count | 1..N features **per** repo | One slug, one workflow |
| Execution | Planning only | `Start Feature <slug>` in that PROJECT_ROOT |

**Never** implement cross-repo work as one harness Feature.

---

## Workspace folders (repos)

Open via `market-data-workspace.code-workspace`. Use these names in Epic `repos:` and in the Features table **Repo** column:

| Folder | Role | May host harness Features? |
|--------|------|----------------------------|
| `market-data-be` | Backend API + analysis/classification | Yes — PROJECT_ROOT |
| `market-data-fe` | Frontend app | Yes — PROJECT_ROOT |
| `market-data-brand` | Design system, tokens, UI kit, wording | Yes — PROJECT_ROOT |
| `shared` | Cross-repo contracts (e.g. OpenAPI snapshot) | Only after harness adoption |
| `product` | Epics + product architecture | No — planning only |
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
5. If Features already exist in repos, link their real slugs + statuses.
6. If Features are new: list **proposed** slugs under Features; tell the user they must be added to each repo’s `feature_list.json` before `Start Feature`.
7. Do **not** invent Feature specs (`specs/`) from the Epic alone — Spec Writer runs per Feature after harness start.
8. Optional: link architecture notes under `product/*.md`.

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
architecture: <optional relative path to product/*.md>
---

# <Title>

## Outcome

<2–4 lines. Value for the product, not implementation detail.>

## Features

| Order | Repo | Slug | Intent | Status |
|------:|------|------|--------|--------|
| 1 | market-data-be | example-slug | … | Pending |

Status values for linked features: `Pending` | `In Progress` | `Done` | `Blocked` | `Proposed`
(Use `Proposed` until the slug exists in that repo’s feature_list.json.)

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
- Prefer replace over patch when direction changes (note superseded epics).
- Keep the epic file brief; put deep design in `product/<topic>-architecture.md`.

---

## After creating the Epic

Tell the user:

1. Path of the new epic file.
2. Which Feature slugs are `Proposed` vs already in `feature_list.json`.
3. Next step: run `create features for epic <slug>` (feature-writer), then `Start Feature <slug>` in each PROJECT_ROOT in the suggested order.

Agent entrypoint: `product/agents/epic-writer.md` (see `product/deployment.md`).
