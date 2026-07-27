---
id: 9
slug: reading-layout
title: Reading Layout
outcome: FE presents a Symbol Reading as a structured workspace — Regions and Analysis units from brand visual-definitions, gray-box fidelity from the Figma wireframe, styled with brand tokens/components.
status: Planned
retrospective: false
repos: [market-data-fe]
---

# Reading Layout

## Outcome

Users read one Symbol in a coherent analytical workspace: Context frames the Symbol, Analysis carries the interpretive message, and supporting Regions (Classification, Evidence, Market Data, Diagnostics) stay subordinate. Structure comes from brand Reading + Analysis visual-definitions and the Figma wireframe’s gray-box arrangement; visual surface uses existing brand tokens and components — not a hi-fi Figma prototype.

## Features

| Order | Repo | Slug | Intent | Status | Release |
|------:|------|------|--------|--------|---------|
| 1 | market-data-fe | reading-layout-shell | Gray-box Reading Regions (Context, Analysis, Classification, Evidence, Market Data, Diagnostics) per adopted Reading contract; spatial structure from Figma wireframe; brand tokens/components for chrome and region chrome | Pending | |
| 2 | market-data-fe | reading-analysis-composition | Gray-box Analysis Region internals (peer Assessments, Recommendation, Narrative) per adopted Analysis contract; hierarchy and anti-patterns from Analysis visual-definitions; brand tokens/components | Pending | |

## UX sources

| Topic | SoT (brand) | Adopt into repo | Adopted path |
|-------|-------------|-----------------|--------------|
| Reading | knowledge/visual-definitions/ (reading-model, reading-composition-model, reading-layout-blueprint, reading-layout-specification; shared domain-language, ui-philosophy) | market-data-fe | knowledge/ux/reading-contract.md |
| Analysis | knowledge/visual-definitions/analysis/ (analysis-information-architecture, analysis-presentation-model, analysis-composition-model, analysis-layout-blueprint, analysis-layout-specification) | market-data-fe | knowledge/ux/analysis-contract.md |

## Acceptance (product)

- [ ] Symbol Reading in FE exposes the Reading Regions as a stable workspace structure (not a flat data dump or equal-weight card wall).
- [ ] Analysis is the primary interpretive Region; supporting Regions do not overpower it in the initial viewport.
- [ ] Analysis Region composition follows peer Assessments + Recommendation/Narrative rules (no invented Overall/Synthesis layer).
- [ ] Layout fidelity is gray-box / structural (wireframe-aligned), implemented with brand tokens and components in FE.
- [ ] Spec/Implement work in FE uses adopted `knowledge/ux/*-contract.md` only — harness does not read brand.

## Out of scope

- Figma hi-fi / pixel-polish prototype as a delivery gate
- New brand visual-definitions authorship (SoT stays in brand)
- BE API or DTO changes
- Full payload→Region data mapping as a global SoT (lives per Feature in `specs/<slug>/design.md`)
- Content/i18n catalog work (epic `content-i18n`)
- Replacing app shell navigation chrome beyond what Reading layout needs

## Notes

- Figma **wireframe** (structural SoT): https://www.figma.com/design/opTJ1E7ZHIgpLPQTw1YtKP/Reading-Layout-Wireframe?node-id=0-1 — gray-box fidelity only; hi-fi frames are not a delivery gate.
- Depends on shipped `design-system-shell` (brand kit + app layout) and existing market surfaces for data wiring; this epic reshapes Reading composition, not first-time API connectivity.
- Adopted contracts live in `market-data-fe/knowledge/ux/` (`reading-contract.md`, `analysis-contract.md`). Features `reading-layout-shell` and `reading-analysis-composition` shipped; post-ship wireframe alignment amends those Done Features in place.
