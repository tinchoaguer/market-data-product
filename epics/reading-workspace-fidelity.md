---
id: 10
slug: reading-workspace-fidelity
title: Reading Workspace Fidelity
outcome: The Symbol Reading workspace is the product’s faithful UX→FE surface — Region bodies, Evidence/Diagnostics behavior, and navigation match adopted Reading contracts, not a gray-box shell beside legacy screens.
status: Planned
retrospective: false
repos: [market-data-fe, market-data-brand]
---

# Reading Workspace Fidelity

## Outcome

Users experience one coherent Symbol Reading: Context frames Identity, Analysis carries interpretation, Classification / Evidence / Market Data / Diagnostics are real Region bodies with correct ownership and prominence. The app no longer presents three peer destinations that compete with the Reading workspace. Fidelity stays gray-box / structural (wireframe + brand tokens), not hi-fi Figma polish.

Successor to epic `reading-layout` (shell + Analysis composition shipped).

## Features

| Order | Repo | Slug | Intent | Status | Release |
|------:|------|------|--------|--------|---------|
| 1 | market-data-brand | visual-definitions-index | Index/README for Reading + Analysis visual-definition stacks; align `ui-philosophy` terminology to Symbol (not Asset) so SoT language matches domain language | Done | |
| 2 | market-data-fe | reading-app-navigation | Make Symbol Reading the primary product surface; resolve dual IA (legacy Market Data / Analysis / Classification destinations vs one Reading); subordinate or retire competing screens | Pending | |
| 3 | market-data-fe | reading-context-identity | Context Region presents full Identity (display name / pair form — not ticker string alone); still must not compete with Analysis for message prominence | Pending | |
| 4 | market-data-fe | reading-evidence-composition | Evidence Region composes Drivers → Features → Contributions attached to claims; stop nesting assessment `components`/factors as Analysis-owned body — Interpretation before Evidence | Pending | |
| 5 | market-data-fe | reading-diagnostics | Diagnostics Region quiet when healthy, elevated when blocking; wire trust qualification (insufficient / partial / stale or equivalent mapped signals) without becoming the hero message | Pending | |
| 6 | market-data-fe | reading-classification-body | Classification Region shows compact signal packaging (source, score, reason) per dimension — not count-only summary; remains subordinate and adjacent to Analysis | Pending | |
| 7 | market-data-fe | reading-market-data-body | Market Data Region shows Price, Order book, and Candles as supporting observation — never the primary interpretive Region | Pending | |

> Slugs confirmed in each repo’s `work/feature_list.json` (status `Pending`). Run `Start Feature <slug>` per PROJECT_ROOT in the order above (brand Order 1 can run in parallel with FE).
>
> Suggested Start Feature order matches the table. Order 2 (navigation) should land before or with Region body Features so users reach one Reading surface. Evidence (4) before or with Classification/Market Data bodies so claim→Evidence ownership is clear.

## UX sources

| Topic | SoT (brand) | Adopt into repo | Adopted path |
|-------|-------------|-----------------|--------------|
| Reading | knowledge/visual-definitions/ (reading-model, reading-composition-model, reading-layout-blueprint, reading-layout-specification; shared domain-language, ui-philosophy) | market-data-fe | knowledge/ux/reading-contract.md |
| Analysis | knowledge/visual-definitions/analysis/ (analysis-information-architecture, analysis-presentation-model, analysis-composition-model, analysis-layout-blueprint, analysis-layout-specification) | market-data-fe | knowledge/ux/analysis-contract.md |
| Evidence | knowledge/visual-definitions/ (Evidence sections in reading-model, reading-composition-model, reading-layout-*; Evidence entity in domain-language; Analysis ownership boundaries in analysis/) | market-data-fe | knowledge/ux/evidence-contract.md |
| Diagnostics | knowledge/visual-definitions/ (Diagnostics sections in reading-model, reading-composition-model, reading-layout-*; Diagnostics entity in domain-language; Analysis ownership boundaries in analysis/) | market-data-fe | knowledge/ux/diagnostics-contract.md |

Refresh `reading-contract.md` and `analysis-contract.md` when materializing Features so dated provenance stays current. Create `evidence-contract.md` and `diagnostics-contract.md` on first UX Feature Writer pass for those topics.

## Acceptance (product)

- [ ] Symbol Reading is the primary way users inspect a Symbol; legacy peer destinations do not present a competing equal product IA.
- [ ] Context shows Identity beyond a bare ticker; Analysis remains the message.
- [ ] Evidence lives in the Evidence Region (Drivers → Features → Contributions), claim-attached — not as nested metric dumps inside Assessment peers.
- [ ] Diagnostics qualify trust (quiet / elevated) and never replace Analysis as the hero when healthy.
- [ ] Classification and Market Data Regions carry real compact/supporting bodies consistent with the Reading contract (not placeholders or count-only stubs as the end state).
- [ ] Spec/Implement work in FE uses adopted `knowledge/ux/*-contract.md` only — harness does not read brand.
- [ ] Brand visual-definitions stack is navigable (index) and philosophy wording uses Symbol.

## Out of scope

- Figma hi-fi / pixel-polish prototype as a delivery gate
- New brand visual-definitions authorship beyond index + terminology alignment (Evidence/Diagnostics SoT already lives in Reading/Analysis stacks)
- BE API or DTO redesign as a global goal (per-Feature mapping in `specs/<slug>/design.md`; new fields only if a Feature explicitly needs them)
- Content/i18n catalog work (epic `content-i18n`)
- Charting terminal, multi-Symbol portfolio, execution / order entry

## Notes

- Depends on shipped epic `reading-layout` (`reading-layout-shell`, `reading-analysis-composition`).
- Figma structural wireframe (Reading shell): https://www.figma.com/design/opTJ1E7ZHIgpLPQTw1YtKP/Reading-Layout-Wireframe?node-id=0-1 — gray-box fidelity only. Prefer additional Evidence/Diagnostics structural frames later if needed; do not block on hi-fi.
- **Product decision (Order 2):** choose whether Reading replaces the three nav destinations, becomes the default home with legacy screens demoted, or another subordinate pattern — record the choice in that Feature’s specification.
- **Evidence ownership risk:** current Analysis composition may still show assessment `components` inline; Feature `reading-evidence-composition` must correct ownership to match contracts.
- Regeneration: when brand SoT changes, re-run `create ux features for epic reading-workspace-fidelity` (refresh) per `knowledge/ux-definitions-handoff.md`.
- Adopted contracts materialized 2026-07-27: `reading-contract.md` / `analysis-contract.md` refreshed; `evidence-contract.md` / `diagnostics-contract.md` created.
