# UX definitions handoff

Long-lived product policy for adopting UX definitions from brand into an isolated PROJECT_ROOT so the harness can specify and implement without reading sibling repos.

This does **not** replace Epics, Features, Releases, or the harness Orchestrator. It defines the **UX path** only.

---

## Model

```text
brand visual-definitions (SoT)
        ↓  read across repos
UX Epic (## UX sources)
        ↓  create ux features for epic
UX Feature Writer
        ↓  write
PROJECT_ROOT/knowledge/ux/<topic>-contract.md  +  feature_list Pending
        ↓  Start Feature
Harness Spec → Implement → Review  (PROJECT_ROOT only)
```

**Golden rule:** the harness never depends on paths into brand. The **UX Feature Writer** is the only cross-repo bridge for UX knowledge. Generic `epic-writer` / `feature-writer` do not materialize UX contracts.

---

## Sources of truth

| Artifact | Location | Role |
|----------|----------|------|
| UX definitions (SoT) | `market-data-brand/knowledge/visual-definitions/` | Regions, composition, layout language; Figma URLs may appear in Epic Notes |
| Handoff policy (this file) | `product/knowledge/ux-definitions-handoff.md` | When and how to adopt |
| UX Epic playbook | `product/CREATE_UX_EPIC.md` | Epic with required `## UX sources` |
| UX Feature playbook | `product/CREATE_UX_FEATURE.md` | Materialize contract + backlog entry |
| Adopted contract | `<PROJECT_ROOT>/knowledge/ux/<topic>-contract.md` | Self-contained snapshot for Spec Writer / Implementer |
| Feature data-map | `specs/<slug>/design.md` | Payload/API → Regions; **per Feature**, not under `knowledge/ux/` |

---

## When this path applies

Use **`create ux epic` / `create ux features for epic`** when:

- the FE (or another adopting app) UI must follow Reading / Analysis (or future) Regions, composition, or layout definitions from brand `visual-definitions`, **and**
- Spec Writer must not open brand to do its job.

Use the **generic** `create epic` / `create features for epic` when there is no UX-definition handoff.

---

## Adopted contract

| Rule | Decision |
|------|----------|
| Path | `<PROJECT_ROOT>/knowledge/ux/<topic>-contract.md` (e.g. `reading-contract.md`) |
| Who writes | **UX Feature Writer** when creating or refreshing UX-dependent Features |
| Distill | Regions + roles/questions, hierarchy / viewport, critical internal rules, anti-patterns |
| Do not copy | Entire `visual-definitions` tree |
| Do not include | HTTP / DTO field maps (those belong in `specs/<slug>/design.md`) |
| Isolation | No `../market-data-brand/...` paths in the contract or in `feature_list` description/acceptance; Figma **URLs** are allowed |
| Footer | State that brand `visual-definitions` remains canonical SoT; this file is an adopted contract — regenerate when SoT changes |

### What “self-contained” means

A Spec Writer with only PROJECT_ROOT `knowledge/` + `feature_list` + later `specs/` must be able to write requirements and design for the Feature’s UX structure without reading brand.

---

## Regeneration

When brand `visual-definitions` changes and a Feature that depends on an adopted contract is `Pending` or `In Progress`:

1. Run (or re-run) `create ux features for epic {slug}` with intent to **refresh** the adopted contract, **or** instruct the UX Feature Writer to regenerate `knowledge/ux/<topic>-contract.md` from the Epic’s `## UX sources`.
2. If the Feature is already In Progress / specified: human decides whether to return to Specification so Specs pick up the new contract.
3. Do not edit the adopted contract by hand as if it were SoT.

---

## Data mapping (out of this handoff)

| Step | Owner | Artifact |
|------|-------|----------|
| Payload / API → UI Regions | Spec Writer | `specs/<slug>/design.md` |
| Mapper / view-model code | Implementer | FE `src/` per architecture |

There is **no** `knowledge/ux/*-data-map.md`. Mapping is Feature-scoped and may differ per slice. Tradeoff: some repetition across Features is acceptable; avoid a second global SoT for maps.

---

## Agents

| Role | Command | Playbook |
|------|---------|----------|
| UX Epic Writer | `create ux epic {idea}` | `CREATE_UX_EPIC.md` |
| UX Feature Writer | `create ux features for epic {slug}` | `CREATE_UX_FEATURE.md` |

Generic Epic/Feature writers must **not** implement this handoff. At most, product README may point UX outcomes to these commands.

---

## Checklist: regenerate after visual-definitions change

1. Confirm which Epic `## UX sources` rows point at the changed SoT.
2. Run UX Feature Writer refresh for that epic (or the affected topic).
3. Confirm `<repo>/knowledge/ux/<topic>-contract.md` updated and still has no sibling-repo paths.
4. If Features are In Progress: note Spec re-approval if REQs/design depended on the old contract.
5. Do not invent a parallel contract outside `knowledge/ux/`.
