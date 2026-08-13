---
id: 12
slug: dashboard-mockup-parity
title: Dashboard Mockup Parity
outcome: After dashboard-visual-fidelity, the Symbol Reading surface matches the product mockup’s composition, density, and band structure — not the current two-column Reading workspace snapshot.
status: Planned
retrospective: false
repos: [market-data-brand, market-data-fe, product]
---

# Dashboard Mockup Parity

## Outcome

Users open a Symbol Reading that **reads as** [`dashboard.png`](../dashboard.png): slim chrome, unified hero (identity + price + sparkline + timeframe + 24H strip), four horizontal Analysis cards, a middle Price Chart + Market Stats row, Classification as one Factor/Score/Reason table (plus compact score packaging if approved), and a quiet date/source/Live footer.

This epic closes residual gaps left after `dashboard-visual-fidelity` (all Features Done) where the live FE still matches [`iterations/market-data-fe.vercel.app_.png`](../iterations/market-data-fe.vercel.app_.png) more than the mockup.

## Features

| Order | Repo | Slug | Intent | Depends on | Status | Release |
|------:|------|------|--------|------------|--------|---------|
| 1 | market-data-brand | dashboard-parity-kit | Close remaining kit gaps for mockup parity: Classification score-ring/gauge packaging primitive (not Analysis synthesis), denser Assessment card recipe (icon/badge/score/summary), hero asset-mark sizes, humanized datetime display pattern, footer split layout recipe — so FE does not invent parallel chrome | | Proposed | |
| 2 | market-data-fe | reading-mockup-composition | Restructure Symbol Reading to the mockup’s single-column band order (hero → Analysis → Price Chart + Market Stats → Classification → footer); demote Evidence and Diagnostics out of competing mid-page prominence per human decision | dashboard-parity-kit | Proposed | |
| 3 | market-data-fe | dashboard-chrome-parity | Align app chrome to mockup presentation (header density, theme toggle placement, footer date left / Source+Live right) while keeping Symbol Reading primary — nav labels/presentation only as resolved in human decisions | dashboard-parity-kit | Proposed | |
| 4 | market-data-fe | dashboard-hero-parity | Unify Context identity + observation into the mockup hero: asset mark + pair/name, price with quote, semantic change, sparkline, timeframe pills (1D…1Y), 24H high/low/vol + humanized last update — no scores/recommendations in Context | reading-mockup-composition, dashboard-parity-kit | Proposed | |
| 5 | market-data-fe | dashboard-analysis-parity | Restyle Assessment peers as four **horizontal** mockup cards (icon, label, badge, Score, summary lines) without Evidence CTA clutter on the card face; keep Recommendation rules and no Overall/Synthesis Assessment | reading-mockup-composition, dashboard-parity-kit | Proposed | |
| 6 | market-data-fe | dashboard-observation-parity | Deliver mockup middle row: large Price Chart area fill in `ChartFrame` + Market Stats (24H) list (cap, supply, volume, volatility, funding, OI as available). Missing BE fields use mocks until a later BE Feature if human chooses mocks | reading-mockup-composition, dashboard-parity-kit | Proposed | |
| 7 | market-data-fe | dashboard-classification-parity | Single Factor/Score/Reason table (not four mini-tables) + compact score visualization only if human allows Classification packaging (not Analysis Overall Score) | reading-mockup-composition, dashboard-parity-kit | Proposed | |

> Successor to epic `dashboard-visual-fidelity` (id 11, Done). Do **not** reopen Done slugs from that epic; new slugs only. Not a UX-definitions handoff epic — Region rules stay in existing FE `knowledge/ux/*-contract.md`; visual SoT is `dashboard.png` + brand package.

## Human decisions

| ID | Decision | Options | Status | Blocks | Resolution |
|----|----------|---------|--------|--------|------------|
| H1 | Mockup centered nav (Market Data / Analysis / Classification with Analysis active) vs Reading-primary IA in `knowledge/ux/reading-contract.md` | **A)** Chrome labels/visual only — Reading remains the only primary product surface (recommended). **B)** Restore three equal peer destinations (conflicts prior Reading work). **C)** Omit centered nav; keep Reading badge + theme toggle only | Open | create features | |
| H2 | Mockup “Overall Score” doughnut (0.25 / Neutral) vs Analysis anti-pattern (no Overall/Synthesis rollup) | **A)** Classification-only packaging (gauge summarizes Classification, not a fifth Assessment) — recommended if gauge ships. **B)** Omit gauge; table only. **C)** Change Analysis contract to allow Overall Score (out of scope for this epic unless explicitly approved) | Open | create features | |
| H3 | Evidence + Diagnostics dominate the current FE mid/bottom page but are absent from the mockup first composition | **A)** Keep Regions but demote: below Classification / collapsed / quiet-by-default (recommended). **B)** Hide from default Reading viewport (reachable only via deep/Evidence entry). **C)** Keep current two-column prominence (rejects mockup parity for composition) | Open | create features | |
| H4 | Mockup Market Stats fields (Market Cap, Circulating Supply, Volatility, Funding Rate, Open Interest) may exceed current Market Data payloads | **A)** FE mocks / placeholders for missing fields (`mock-ok`) until a later BE Feature — recommended for parity velocity. **B)** Hard-wait on a new BE Feature before FE observation Feature. **C)** Show only fields already available (partial stats card) | Open | create features | |
| H5 | Mockup breadcrumb “← Back to Analysis” | **A)** Omit (recommended; avoids peer-destination IA). **B)** Include as non-navigating or Reading-internal chrome only. **C)** Wire as navigation back to a legacy Analysis route | Open | create features | |

All of H1–H5 are **Open** and collectively block **`create features`**. Feature Writer must not append `feature_list` entries until each is **Resolved** with an explicit human Resolution. Brand Order 1 is still gated by this epic-wide block so kit scope can reflect H2 (gauge vs omit) and H1/H5 chrome choices.

## Dependency notes

- **Hard:** all FE Features **Depends on** `dashboard-parity-kit` (brand publish / new minor). After brand Feature is `Done` and `@maguer/market-data-brand` publishes the parity kit, re-run `create features for epic dashboard-mockup-parity` for FE rows (still subject to human gates above — resolve humans **before** any create pass).
- **Hard (same-repo):** `dashboard-hero-parity`, `dashboard-analysis-parity`, `dashboard-observation-parity`, `dashboard-classification-parity` **Depends on** `reading-mockup-composition` so band order exists before restyle.
- **Soft / Order only:** `dashboard-chrome-parity` may start after composition or in parallel once kit + humans allow; Order prefers chrome after composition for less thrash.
- **BE→FE:** if H4 resolves to **A** (`mock-ok`), `dashboard-observation-parity` does **not** hard-depend on a BE Feature. If H4 resolves to **B**, add a BE Feature row and hard Depends on before creating the FE observation Feature.
- Brand→FE never uses the mock exception.

## Acceptance (product)

- [ ] First viewport / scroll structure matches mockup bands: header → hero → Analysis (4 cards) → Price Chart + Market Stats → Classification → footer — not the current Analysis|Classification + Evidence|Market-Data two-column wall ([`iterations/market-data-fe.vercel.app_.png`](../iterations/market-data-fe.vercel.app_.png)).
- [ ] Hero matches mockup density: identity + price/change/sparkline + timeframe pills + 24H strip with humanized last update.
- [ ] Analysis presents four horizontal Assessment cards with score/summary treatment; Recommendation remains non-Assessment.
- [ ] Observation row shows a large area Price Chart and a Market Stats list consistent with H4.
- [ ] Classification is one Factor/Score/Reason table; any gauge follows H2 (packaging or omitted) — never an Analysis Overall Assessment.
- [ ] Evidence and Diagnostics follow H3 (demoted or hidden) and do not dominate the mockup composition.
- [ ] Chrome/footer follow H1/H5; Live + Source + date present and brand-styled.
- [ ] Visual SoT remains [`dashboard.png`](../dashboard.png); surface uses `@maguer/market-data-brand` only (no parallel FE design system).
- [ ] Reading Region ownership rules in FE `knowledge/ux/*-contract.md` remain intact except where a Resolved human decision explicitly allows Classification packaging (H2-A).

## Out of scope

- Reopening Done Features from `dashboard-visual-fidelity` (new slugs only)
- Authoring a new brand `visual-definitions` stack solely for this mockup (use UX epic path if that becomes necessary)
- Changing Analysis contract to add Overall/Synthesis unless H2-C is explicitly Resolved
- Full trading terminal, order entry, multi-symbol portfolio
- Content/i18n catalog redesign (`content-i18n`)
- Pixel fights that contradict brand tokens — extend brand kit instead

## Notes

### Gap summary (iteration vs mockup)

| Band | Mockup (`dashboard.png`) | Current (`iterations/…vercel…png`) | Parity Feature |
|------|--------------------------|-------------------------------------|----------------|
| Chrome | Centered Market Data / Analysis / Classification; sun toggle | “Reading” pill + gear; no centered nav | `dashboard-chrome-parity` (+ H1, H5) |
| Hero | ETH mark + pair/name \| price USDT + green Δ + sparkline \| 1D…1Y \| 24H strip | Symbol select + Context fields (Base/Quota) \| sparse price/0% \| ISO time \| tiny 15m wave | `dashboard-hero-parity` |
| Analysis | 4 horizontal cards (badge, Score, summary lists) | 2×2 grid + Evidence buttons + Recommendation | `dashboard-analysis-parity` |
| Mid row | Large Price Chart + rich Market Stats | Tiny charts; stats high/low/update only; Evidence dominates left | `reading-mockup-composition`, `dashboard-observation-parity` (+ H3, H4) |
| Classification | One table + Overall Score gauge | Four mini Factor tables; no gauge | `dashboard-classification-parity` (+ H2) |
| Evidence / Diagnostics | Not in mockup composition | Large Evidence stack + Diagnostics card | H3 + composition |
| Footer | Date left; Source + Live right | Live + date + Source line (partial) | `dashboard-chrome-parity` |

### Visual sources

- Target: [`dashboard.png`](../dashboard.png)
- Baseline after prior epic: [`iterations/market-data-fe.vercel.app_.png`](../iterations/market-data-fe.vercel.app_.png)
- Prior epic: [`epics/dashboard-visual-fidelity.md`](dashboard-visual-fidelity.md)

### Risks

- Composition change is larger than “restyle cards” — treat `reading-mockup-composition` as the structural slice.
- H2 must stay Classification packaging or omit; do not smuggle Overall Score into Analysis.
- Mock Market Stats (H4-A) must be labeled/handled so Spec/Review do not treat mocks as production BE contracts forever.
