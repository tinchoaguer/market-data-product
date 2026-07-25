# Create UX Epic (Market Data product)

Use this when the user asks to create or update a **UX Epic** — an Epic whose Features must adopt UX definitions from brand `visual-definitions` into an isolated PROJECT_ROOT.

This playbook is a **specialized sibling** of [`CREATE_EPIC.md`](CREATE_EPIC.md). It does **not** replace the generic Epic path. Prefer `create epic` when there is no UX-definition handoff.

Policy: [`knowledge/ux-definitions-handoff.md`](knowledge/ux-definitions-handoff.md).

---

## Model

```text
UX Epic (## UX sources required)
  →  create ux features for epic
  →  adopted contract in PROJECT_ROOT/knowledge/ux/
  →  feature_list Pending
  →  Start Feature (harness)
```

Same registry as generic Epics: `product/epics/<slug>.md` + `product/epics/index.json`.

---

## Relationship to CREATE_EPIC.md

Reuse by **reference** (do not fork the whole world):

| Concern | Authority |
|---------|-----------|
| Workspace folders / which repos may host Features | `CREATE_EPIC.md` |
| Epic status rules, `index.json` shape, Release column | `CREATE_EPIC.md` |
| When **not** to create an Epic (tiny single-repo task) | `CREATE_EPIC.md` |
| Required `## UX sources` + next command | **This playbook** |

---

## When to create a UX Epic

Create when the outcome:

- depends on brand `knowledge/visual-definitions/` (Reading, Analysis, future topics), **and**
- needs Features in an adopting repo (usually `market-data-fe`) whose harness must not read brand.

Do **not** use this playbook for outcomes without UX-definition handoff — use `CREATE_EPIC.md`.

---

## Steps

1. Read `product/epics/index.json` and pick next `id` (same as `CREATE_EPIC.md`).
2. Choose `slug` (kebab-case, unique).
3. Identify UX SoT paths under `market-data-brand/knowledge/visual-definitions/` and the adopt target repo + adopted path (`knowledge/ux/<topic>-contract.md`).
4. Write `product/epics/<slug>.md` using the template below (**`## UX sources` is mandatory**).
5. Append entry to `product/epics/index.json`.
6. If Features already exist in repos, link real slugs + statuses; otherwise list **Proposed** slugs.
7. Do **not** invent Feature specs; do **not** write adopted contracts here — that is `CREATE_UX_FEATURE.md`.
8. Optional: assign Release column later via `CREATE_RELEASE.md`.

---

## Template (`product/epics/<slug>.md`)

Same frontmatter and body shape as `CREATE_EPIC.md`, **plus** required UX sources:

```markdown
---
id: <number>
slug: <kebab-case>
title: <short title>
outcome: <one sentence product outcome>
status: Planned | Active | Done | Dropped
retrospective: false
repos: [<market-data-fe|…>, ...]
architecture: <optional relative path>
---

# <Title>

## Outcome

<2–4 lines. Value for the product, not implementation detail.>

## Features

| Order | Repo | Slug | Intent | Status | Release |
|------:|------|------|--------|--------|---------|
| 1 | market-data-fe | example-slug | … | Proposed | |

## UX sources

| Topic | SoT (brand) | Adopt into repo | Adopted path |
|-------|-------------|-----------------|--------------|
| Reading | knowledge/visual-definitions/ (Reading + Analysis stacks) | market-data-fe | knowledge/ux/reading-contract.md |

## Acceptance (product)

- [ ] <observable cross-cutting outcomes>

## Out of scope

- <bullets>

## Notes

<optional: Figma URLs, dependencies, risks>
```

### UX sources rules

- **Required** for every UX Epic (at least one row).
- **SoT (brand)** must point under `market-data-brand/knowledge/visual-definitions/` (topic description is enough; Feature Writer resolves files).
- **Adopt into repo** must be a harness-adopting folder (see `CREATE_EPIC.md` workspace table).
- **Adopted path** must be `knowledge/ux/<topic>-contract.md` relative to that repo (no sibling-repo paths).
- Empty UX sources table → invalid UX Epic; fix or use generic `CREATE_EPIC.md` instead.

---

## Quality bar

- Outcome is product language.
- Features are vertically sliceable; one adopting repo per row.
- UX sources rows have usable SoT + Adopted path under `knowledge/ux/`.
- Keep the epic brief; deep UX SoT stays in brand; deep product notes may live under `product/knowledge/`.
- Do not instruct Spec Writer to read brand.

---

## After creating the UX Epic

Tell the user:

1. Path of the new epic file.
2. Which Feature slugs are `Proposed` vs already in `feature_list.json`.
3. The `## UX sources` rows (topic → adopted path).
4. Next step: **`create ux features for epic <slug>`** (UX Feature Writer) — **not** `create features for epic`.
5. Then optionally `create release {version}`, then `Start Feature <slug>` in each PROJECT_ROOT.

Agent entrypoint: `product/agents/ux-epic-writer.md` (see `product/deployment.md`).
