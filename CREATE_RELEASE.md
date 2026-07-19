# Create Release (Market Data product)

Use this when the user asks to create or update a **product Release**.

Releases live in `product/releases/`. They group Epic Features into a shippable cut and pin per-repo versions. They do **not** replace Epics, `feature_list.json`, or the harness Orchestrator.

---

## Model

```text
Epic (outcome)  →  Features (per repo)  →  Release (product cut)  →  Version pins (per repo)
```

| | Epic | Feature | Release |
|--|------|---------|---------|
| Where | `product/epics/` | `<repo>/work/feature_list.json` | `product/releases/` |
| Scope | Product outcome | One PROJECT_ROOT | Cross-repo ship unit |
| SoT for | Outcome + full feature set | Harness backlog / status | What ships together + repo pins |

**Rule:** Release owns the cut. Epic Features table may show a `Release` column for glanceability; that column must match the release file.

**Never** implement a release as one cross-repo harness Feature.

---

## When to create a Release

Create when:

- a set of Epic Features should ship together, **or**
- you need durable product-level version pins across repos, **or**
- you are backfilling a retrospective cut for already-Done Features.

Do **not** create a Release for a single tiny Feature unless that Feature is intentionally the whole cut.

---

## Steps

1. Read `product/releases/index.json` and pick next `id` and `version` (semver product cut, e.g. `0.2.0`).
2. Choose `slug` — usually the same as `version` (`0.2.0`).
3. Select Features from one or more Epics (repo + slug). Prefer whole epic slices; partial cuts are allowed and must be listed explicitly.
4. Write `product/releases/<slug>.md` using the template below.
5. Append entry to `product/releases/index.json`.
6. Update each linked Epic’s Features table: set the **Release** column to this version for every Feature in the cut.
7. Do **not** bump package versions, create git tags, or mark Features Done — shipping is a human/deploy step after Features complete.
8. Optional: link architecture under `product/knowledge/` or `product/*-architecture.md`.

---

## Template (`product/releases/<slug>.md`)

```markdown
---
version: <semver>
slug: <usually same as version>
title: <short title>
status: Planned | Active | Shipped | Dropped
date: <YYYY-MM-DD or null until Shipped>
epics: [<epic-slug>, ...]
architecture: <optional relative path>
---

# Release <version>

## Outcome

<2–4 lines. What this cut delivers for the product.>

## Versions (repo pins)

| Repo | Version | Git tag |
|------|---------|---------|
| market-data-be | TBD | TBD |
| market-data-fe | TBD | TBD |
| market-data-brand | TBD | TBD |

## Features in this release

| Repo | Slug | Epic | Status |
|------|------|------|--------|
| market-data-fe | example-slug | example-epic | Pending |

Status values: `Pending` | `In Progress` | `Done` | `Blocked` | `Proposed`
(Mirror the epic / feature_list status for that slug.)

## Acceptance (product)

- [ ] <observable outcomes for this cut>

## Notes

<optional: partial epic cuts, superseded releases, risks>
```

---

## `index.json` entry

```json
{
  "id": <number>,
  "version": "<semver>",
  "slug": "<slug>",
  "title": "<title>",
  "status": "Planned",
  "date": null,
  "epics": ["<epic-slug>"]
}
```

Keep index in sync when release status or date changes.

---

## Status rules

| Release status | Meaning |
|----------------|---------|
| Planned | Cut agreed; Features not all started |
| Active | At least one linked Feature In Progress |
| Shipped | All linked Features Done; repo pins + tags filled; `date` set |
| Dropped | Cut abandoned |

Epic status remains independent: an Epic becomes Done only when **all** its Features are Done (see `CREATE_EPIC.md`), even if an earlier Release shipped a subset.

---

## Sync rules

1. **Plan:** create/update the release file; set Epic Features table **Release** column for those rows.
2. **During work:** Feature status SoT is each repo’s `feature_list.json`. Mirror status into the epic table and the release Features table when updating product docs.
3. **On Feature Complete:** set that slug’s status to `Done` in epic + release tables (product docs); harness history stays in the repo.
4. **On ship:** set release `Shipped`, set `date`, fill **Versions (repo pins)** and git tags, update `index.json`.
5. **Empty Release column** on an epic Feature means “not yet assigned to a cut.”

---

## Epic Features table (Release column)

Epics use:

```markdown
| Order | Repo | Slug | Intent | Status | Release |
|------:|------|------|--------|--------|---------|
| 1 | market-data-fe | example-slug | … | Pending | 0.2.0 |
```

Retrospective epics may omit Order/Intent if already brief; still include **Release**.

---

## Quality bar

- Cut is a coherent product story, not a random Feature dump.
- Every Feature row exists (or is Proposed) in that repo’s `feature_list.json` before Active/Shipped.
- Repo pins are filled before calling the release Shipped.
- Prefer replace over patch when a cut is superseded; note it under Notes.

---

## After creating the Release

Tell the user:

1. Path of the new release file and `index.json` entry.
2. Which Epic rows had **Release** set.
3. Next step: finish Features via `Start Feature <slug>` in each PROJECT_ROOT; when the cut is ready, fill pins/tags and mark the release Shipped.

Agent entrypoint: `product/agents/release-writer.md` (see `product/deployment.md`).
