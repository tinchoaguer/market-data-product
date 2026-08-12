---
id: 11
slug: dashboard-visual-fidelity
title: Dashboard Visual Fidelity
outcome: The Symbol Reading workspace visually matches the product dashboard mockup, using market-data-brand tokens and components — not a parallel FE design system.
status: Done
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
| 1 | market-data-brand | dashboard-surface-kit | Close kit/token gaps required by the mockup (e.g. header density, theme-toggle control, timeframe pill pattern, sparkline/chart chrome recipes, live/status footer patterns, semantic up/down emphasis) so FE can compose without inventing a parallel design system | Done | |
| 2 | market-data-fe | dashboard-app-chrome | Adapt `AppLayout` / brand `AppShell` chrome to the mockup using published brand `0.2.0` (`AppHeaderBar` slim, `ThemeToggle`, `StatusFooter`/`LiveStatus`): slim header, brand mark + name, nav presentation, theme toggle, optional breadcrumb, date/source/Live footer — Reading remains the primary product surface | Done | |
| 3 | market-data-fe | dashboard-hero-band | Compose the top Reading band to the mockup hero on brand `0.2.0` (`ChartFrame`, `SegmentedControl`/`TimeframeControl`, semantic up/down): Symbol identity with adjacent observation (price, change, sparkline, timeframe pills, 24H high/low/vol, last update) without making Context own scores or recommendations | Done | |
| 4 | market-data-fe | dashboard-analysis-cards | Restyle Analysis Assessment peers (Regime, Risk, Economic, Exposure) as the mockup’s four vertical cards (icon, label, badge, score, summary) using brand `0.2.0` Card/Badge/Text primitives | Done | |
| 5 | market-data-fe | dashboard-observation-row | Restyle Market Data Region as the mockup middle row on brand `0.2.0`: price area chart in `ChartFrame` (left) + Market Stats 24H card (right); timeframe controls via `SegmentedControl`/`TimeframeControl` | Done | |
| 6 | market-data-fe | dashboard-classification-surface | Restyle Classification Region as the mockup Factor/Score/Reason table (and compact score visualization if needed) using brand `0.2.0` Table/Card/Text — without inventing an Overall Assessment / Synthesis rollup that replaces the four dimensions | Done | |

> All linked Features are `Done` in repo `work/feature_list.json`: brand id 3 (`dashboard-surface-kit`, published `@maguer/market-data-brand@0.2.0`); FE ids 19–23 (`dashboard-app-chrome` … `dashboard-classification-surface`). Epic status is `Done` per linked-Feature rule. Product acceptance below is still the visual SoT check against [`dashboard.png`](../dashboard.png) — residual mockup gaps (if any) need a follow-up epic/features, not reopening these Done slugs without human decision.

## Acceptance (product)

- [ ] Symbol Reading first viewport and scroll structure read as the dashboard mockup composition (header → hero → Analysis cards → chart/stats → Classification → footer), not a gray-box Region wall.
- [ ] Visual surface uses `@maguer/market-data-brand` tokens/CSS variables and UI components; FE does not introduce a second token set, component library, or ad-hoc Tailwind design system.
- [x] Brand kit gaps required by the mockup are closed in `market-data-brand` (Order 1) rather than patched only in FE — shipped in `@maguer/market-data-brand@0.2.0`.
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
- Current implementation snapshot for gap review: [`iterations/market-data-fe.vercel.app_.png`](../iterations/market-data-fe.vercel.app_.png).
- When specifying FE Features, copy or attach the mockup into that Feature’s spec/design artifacts as needed so the harness stays PROJECT_ROOT-local.

### Mockup → current FE mapping

| Mockup band | Current FE / brand anchor | Adaptation notes |
|-------------|---------------------------|------------------|
| Header & nav + theme toggle | `AppLayout` + brand `AppShell` / `AppHeader*` / `AppNav` / `ThemeToggle` (`0.2.0`) | Slim `AppHeaderBar`; Reading-primary IA; wire `ThemeToggle` persistence in FE |
| Breadcrumb (“Back to Analysis”) | Not present | Optional chrome; do not resurrect legacy peer screens as equal destinations |
| Hero asset (identity + price strip) | Context (`ContextIdentity` — identity only) + Market Data price fields | Compose visually as one hero band; keep ownership: identity vs observation; use semantic up/down + `ChartFrame` / `SegmentedControl` |
| Analysis four cards | `AssessmentsWorkspace` / Assessment peers | Surface/card treatment per mockup; keep four peers + Recommendation rules |
| Price chart + Market Stats 24H | Market Data Region (`MarketDataBody`, candles/price) | Chart in `ChartFrame` + stats card; timeframe via `SegmentedControl`/`TimeframeControl` |
| Classification table + gauge | Classification Region (`ClassificationBody`) | Factor/Score/Reason table; any gauge must not become Overall Assessment synthesis |
| Footer date + Source/Live | Missing / Diagnostics quiet state | Prefer `StatusFooter`/`LiveStatus`; Diagnostics remain trust qualification, not hero |

### Brand inventory (consume `@maguer/market-data-brand@^0.2.0`)

- **Tokens / CSS:** generated `tokens.css` / UI bundle (`--color-*`, `--font-*`, `--radius-*`, `--space-*`, motion, density, chart, semantic positive/negative). Dark ink/surface + wheat text remains the product dark theme — FE aligns mockup polish to these semantics; do not invent parallel tokens in FE.
- **Base components:** `AppShell` family (`AppHeader` / `AppHeaderBar` with `density="slim"`), `Page*`, `Stack` / `Inline` / `Cluster`, `Card*`, `Button`, `Badge` (incl. `positive`/`negative`), `Table*`, `Text` (incl. `tone="positive"`/`"negative"`) / `Label` / `SectionHeading`, `Select*`, `Skeleton`, `Alert*`, `Logo`.
- **Dashboard surface kit (Order 1 — published in `0.2.0`):** `ThemeToggle`, `SegmentedControl` / `TimeframeControl`, `ChartFrame` (+ chart CSS vars), `StatusFooter` / `LiveStatus`, slim header density tokens, semantic up/down tokens. FE Features compose these; further kit gaps go back to brand, not ad-hoc FE primitives.

### Dependencies / risks

- Depends on shipped Reading shell, Analysis composition, Region bodies, and Reading-primary navigation (`reading-layout`, `reading-workspace-fidelity` Features in FE).
- **Risk:** mockup’s “Overall Score” doughnut conflicts with Analysis contract (no Overall/Synthesis rollup). Feature `dashboard-classification-surface` must resolve this as Classification packaging or omit the rollup — not a fifth Assessment.
- **Risk:** mockup hero puts price in the top band while Context Identity is identity-only; Feature `dashboard-hero-band` must compose without re-homing interpretation into Context.
- Not a UX-definitions handoff epic: Spec/Implement use existing FE `knowledge/ux/*-contract.md` for Region rules and `dashboard.png` (+ brand package) for surface fidelity. If brand later authors dashboard-specific visual-definitions, regenerate via the UX path separately.
