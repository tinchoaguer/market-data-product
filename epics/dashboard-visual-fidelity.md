---
id: 11
slug: dashboard-visual-fidelity
title: Dashboard Visual Fidelity
outcome: The Symbol Reading workspace visually matches the product dashboard mockup, using market-data-brand tokens and components — not a parallel FE design system.
status: Planned
retrospective: false
repos: [market-data-brand, market-data-fe, product]
---

# Dashboard Visual Fidelity

## Outcome

Users open a Symbol Reading that looks like the product’s dark dashboard mockup: slim chrome, asset hero band, Analysis assessment cards, observation (chart + 24H stats), Classification packaging, and a quiet status footer. Visual surface fidelity comes from brand tokens/components plus the mockup; Reading Region semantics from prior UX work stay intact.

Successor to gray-box Reading delivery (`reading-layout`, `reading-workspace-fidelity`): structure and Region bodies exist; this epic elevates the surface to mockup fidelity.

## Features

| Order | Repo | Slug | Intent | Status | Release |
|------:|------|------|--------|--------|---------|
| 1 | market-data-brand | dashboard-surface-kit | Close kit/token gaps required by the mockup (e.g. header density, theme-toggle control, timeframe pill pattern, sparkline/chart chrome recipes, live/status footer patterns, semantic up/down emphasis) so FE can compose without inventing a parallel design system | Pending | |
| 2 | market-data-fe | dashboard-app-chrome | Adapt `AppLayout` / brand `AppShell` chrome to the mockup: slim header, brand mark + name, nav presentation, theme toggle, optional breadcrumb, date/source/Live footer — Reading remains the primary product surface | Pending | |
| 3 | market-data-fe | dashboard-hero-band | Compose the top Reading band to the mockup hero: Symbol identity (logo/pair/full name) with adjacent observation (price, change, sparkline, timeframe pills, 24H high/low/vol, last update) without making Context own scores or recommendations | Pending | |
| 4 | market-data-fe | dashboard-analysis-cards | Restyle Analysis Assessment peers (Regime, Risk, Economic, Exposure) as the mockup’s four vertical cards (icon, label, badge, score, summary) using brand Card/Badge/Text primitives | Pending | |
| 5 | market-data-fe | dashboard-observation-row | Restyle Market Data Region as the mockup middle row: price area chart (left) + Market Stats 24H card (right); timeframe controls stay brand-styled | Pending | |
| 6 | market-data-fe | dashboard-classification-surface | Restyle Classification Region as the mockup Factor/Score/Reason table (and compact score visualization if needed) without inventing an Overall Assessment / Synthesis rollup that replaces the four dimensions | Pending | |

> All slugs confirmed `Pending` in each repo’s `work/feature_list.json` (`market-data-brand` id 3; `market-data-fe` ids 19–23). Next: `Start Feature` in Order (brand Order 1 before FE Features that depend on kit gaps).

## Acceptance (product)

- [ ] Symbol Reading first viewport and scroll structure read as the dashboard mockup composition (header → hero → Analysis cards → chart/stats → Classification → footer), not a gray-box Region wall.
- [ ] Visual surface uses `@maguer/market-data-brand` tokens/CSS variables and UI components; FE does not introduce a second token set, component library, or ad-hoc Tailwind design system.
- [ ] Brand kit gaps required by the mockup are closed in `market-data-brand` (Order 1) rather than patched only in FE.
- [ ] Reading Region ownership remains coherent: Analysis carries interpretation; Classification stays compact/subordinate; Market Data stays supporting observation; Context does not become a scoreboard of recommendations.
- [ ] Mockup chrome (theme toggle, Live indicator, footer) is present and brand-styled.
- [ ] Visual source of truth for composition and density is [`dashboard.png`](../dashboard.png) at the product root.

## Out of scope

- New BE APIs or DTO redesign (map existing payloads in per-Feature `specs/<slug>/design.md`)
- Content/i18n catalog redesign (epic `content-i18n`)
- Replacing Reading with three equal peer destinations (Market Data / Analysis / Classification as competing IA) — mockup nav labels may inform chrome presentation only; Reading stays primary
- Inventing an Overall Score / Overall Assessment / Synthesis Region that replaces Regime/Risk/Economic/Exposure peers (explicit Analysis anti-pattern)
- Pixel-perfect recreation that conflicts with brand tokens (prefer brand palette/type/radius; extend brand when the mockup needs a missing primitive)
- Execution, order entry, multi-Symbol portfolio, or charting-terminal features beyond the mockup’s observation chart
- Authoring a new brand `visual-definitions` Region stack solely for this mockup (structure SoT remains Reading/Analysis definitions; this epic is surface fidelity)

## Notes

### Visual source of truth

- Product mockup: [`dashboard.png`](../dashboard.png) (repo root of `market-data-product`).
- When specifying FE Features, copy or attach the mockup into that Feature’s spec/design artifacts as needed so the harness stays PROJECT_ROOT-local.

### Mockup → current FE mapping

| Mockup band | Current FE / brand anchor | Adaptation notes |
|-------------|---------------------------|------------------|
| Header & nav + theme toggle | `AppLayout` + brand `AppShell` / `AppHeader*` / `AppNav` / `Button` | Slim bar; Reading-primary IA (nav already Reading-centric); add theme toggle via brand kit |
| Breadcrumb (“Back to Analysis”) | Not present | Optional chrome; do not resurrect legacy peer screens as equal destinations |
| Hero asset (identity + price strip) | Context (`ContextIdentity` — identity only) + Market Data price fields | Compose visually as one hero band; keep ownership: identity vs observation |
| Analysis four cards | `AssessmentsWorkspace` / Assessment peers | Surface/card treatment per mockup; keep four peers + Recommendation rules |
| Price chart + Market Stats 24H | Market Data Region (`MarketDataBody`, candles/price) | Chart + stats card layout; brand chart chrome from Order 1 |
| Classification table + gauge | Classification Region (`ClassificationBody`) | Factor/Score/Reason table; any gauge must not become Overall Assessment synthesis |
| Footer date + Source/Live | Missing / Diagnostics quiet state | Prefer chrome footer + Live affordance from brand kit; Diagnostics remain trust qualification, not hero |

### Brand inventory (consume; extend only for gaps)

- **Tokens / CSS:** `market-data-brand` `src/tokens/*` → generated `src/css/tokens.css` / UI bundle (`--color-*`, `--font-*`, `--radius-*`, `--space-*`, motion). Dark ink/surface + wheat text is the existing product dark theme — align mockup polish to these semantics (extend tokens in brand if mockup needs missing positive/up emphasis, denser header spacing, etc.).
- **Components already exported:** `AppShell` family, `Page*`, `Stack` / `Inline` / `Cluster`, `Card*`, `Button`, `Badge`, `Table*`, `Text` / `Label` / `SectionHeading`, `Select*`, `Skeleton`, `Alert*`, `Logo`.
- **Likely kit gaps for this epic (Order 1):** theme-toggle control, timeframe pill / segmented control pattern, sparkline or chart-area chrome recipe, Live/status footer pattern — implement in brand, consume in FE.

### Dependencies / risks

- Depends on shipped Reading shell, Analysis composition, Region bodies, and Reading-primary navigation (`reading-layout`, `reading-workspace-fidelity` Features in FE).
- **Risk:** mockup’s “Overall Score” doughnut conflicts with Analysis contract (no Overall/Synthesis rollup). Feature `dashboard-classification-surface` must resolve this as Classification packaging or omit the rollup — not a fifth Assessment.
- **Risk:** mockup hero puts price in the top band while Context Identity is identity-only; Feature `dashboard-hero-band` must compose without re-homing interpretation into Context.
- Not a UX-definitions handoff epic: Spec/Implement use existing FE `knowledge/ux/*-contract.md` for Region rules and `dashboard.png` (+ brand package) for surface fidelity. If brand later authors dashboard-specific visual-definitions, regenerate via the UX path separately.
