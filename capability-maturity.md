# Capability Maturity Checklist

Product-level view of how mature Market Data is across **product capabilities** and **engineering hygiene**.

This file is SoT for maturity scores. Refresh with `update maturity` (playbook: [`UPDATE_MATURITY.md`](UPDATE_MATURITY.md)).

| Field | Value |
|-------|-------|
| Last reviewed | 2026-08-13 |
| Method | Evidence from `work/` (be / fe / brand), `epics/`, `releases/`, `knowledge/` |
| Agent | [`agents/maturity-writer.md`](agents/maturity-writer.md) |

---

## Legend

| Band | Color | Range | Meaning |
|------|-------|-------|---------|
| Absent / nascent | 🔴 Red | 0–25% | Missing or only a sketch |
| Partial / in progress | 🟡 Yellow | 25–75% | Partial, transitional, or actively building |
| Near mature | 🔵 Blue | 75–90% | Usable; small gaps remain |
| Mature | 🟢 Green | 90–100% | Meets current “green” criterion |

Scores are judgment against the **Target for green** column, not raw feature counts alone.

---

## Product capabilities

| Capability | Status | % | Evidence | Target for green |
|------------|--------|---|----------|------------------|
| Backend platform & market APIs | 🟢 | 95 | BE `fastapi-bootstrap` … `market-data-analysis-api` Done; `analysis-message-refs` + `classification-message-refs` Done; release `0.1.0` Shipped | Runnable REST APIs with DTO contracts for market-data, analysis, classification |
| Analysis & classification engine | 🟢 | 95 | `analysis-classification-pipeline` Done; APIs consume pipeline | Deterministic analysis → classification path; no domain leak into HTTP |
| OpenAPI / reason contracts | 🟢 | 95 | `classification-reason-codes`, `analysis-reason-codes` Done; MessageRef shape on analysis/classification HTTP DTOs (BE Done) | Closed reason/regime sets documented in OpenAPI as client SoT for codes |
| Design system & UI kit | 🔵 | 88 | brand `brand-ui-kit` + `dashboard-surface-kit` Done; `@maguer/market-data-brand@0.2.0` published; FE pin `^0.2.0`. Residual kit in Planned epic `dashboard-mockup-parity` (`dashboard-parity-kit` Proposed, not in `feature_list`) | Full agreed primitive set + documented consumer contract (no Tailwind in FE) |
| App shell & market surfaces | 🟢 | 90 | FE `app-layout` + three view Features Done; `reading-app-navigation` + `dashboard-app-chrome` Done (Reading-primary IA). Residual chrome vs mockup in `dashboard-mockup-parity` (H1/H5 Open) | Shell navigates MD / Analysis / Classification with loading/error states |
| Symbol Reading surface | 🔵 | 80 | Epics `reading-layout`, `reading-workspace-fidelity`, `dashboard-visual-fidelity` Done (FE `reading-*` + `dashboard-*` ids 11–23 Done). Successor `dashboard-mockup-parity` Planned; H1–H5 Open block `create features` | Symbol Reading first viewport matches mockup bands (hero → Analysis → chart/stats → Classification → footer) with Region contracts intact and brand-only surface |
| Content management | 🔵 | 85 | FE `content-message-contract`, `content-catalogs-v1`, `content-resolve-boundary`, `analysis-classification-copy` Done; BE `*-message-refs` Done. Epic `content-i18n` and release `0.2.0` still Planned (stale vs `feature_list`) | Content module owns catalogs + resolve; MessageRef is SoT for narrative |
| Internationalization | 🔵 | 80 | Content `resolve(locale, channel)` + catalogs shipped (`en`/`ui`); feature-local `analysisLabels.*` SoT retired. Extra locales/channels out of v1 scope; `0.2.0` not Shipped | `resolve(locale, channel)`; no feature-local code→English maps as SoT |
| Components / UI definitions | 🔵 | 85 | brand `visual-definitions-index` Done; FE consumes kit + adopted `knowledge/ux/*-contract.md`. Residual parity primitives not authored | Stable public exports; FE consumes kit without private Tailwind toolchain |

---

## Engineering maturity

| Capability | Status | % | Evidence | Target for green |
|------------|--------|---|----------|------------------|
| Documentation | 🔵 | 80 | Product README, epics, releases, knowledge architectures; per-repo specs/history. `content-i18n` epic + `releases/0.2.0.md` Feature rows still say Pending despite `feature_list` Done | Onboarding + architecture notes current; release notes match shipped cuts |
| Testing | 🟡 | 65 | FE Vitest (e.g. 294 passed on `dashboard-classification-surface`); BE pytest configured; brand Vitest + `npm test` on tag publish. No PR CI on be/fe | Meaningful automated tests per adopting repo; failures block merge |
| Version & release management | 🟡 | 60 | `releases/0.1.0` Shipped (pins filled, git tags historically optional); `0.2.0` Planned with TBD pins despite Features Done. Brand `release.yml` (version+tag on main) + `publish.yml` (npm on `v*`) | Every Shipped cut has repo pins + git tags; index status in sync |
| Harness / SDD workflow | 🔵 | 85 | `feature_list` + specs + history/reviews in be, fe, brand; latest FE/brand/BE Features Completed | Consistent Start Feature → Complete across PROJECT_ROOTs |
| Cross-repo contract (shared OpenAPI) | 🟡 | 55 | `shared/openapi.json` snapshot present; MessageRef shipped on BE DTOs but **not** in `shared/` snapshot | Contract sync enforced when public DTOs change (e.g. MessageRef) |
| Security | 🔴 | 15 | Local/demo posture; no product security epic or auth surface documented | Threats for current deploy model addressed (secrets, deps, auth as needed) |
| CI / quality gates | 🔴 | 20 | Brand has tag publish + main version workflows (runs `npm test` on publish). No `.github` PR workflows in be/fe; no PR test gates | CI runs tests (and lint) on PR for adopting repos |
| Observability / ops | 🔴 | 20 | Health endpoint on BE bootstrap; no structured ops story | Health + basic logging/deploy path documented for a real environment |

---

## How to refresh

1. Run: `update maturity` (or `refresh maturity` / `score maturity`).
2. Agent follows [`UPDATE_MATURITY.md`](UPDATE_MATURITY.md).
3. Re-score from evidence; update **Last reviewed**, tables, and optional Notes.

Do **not** invent maturity from memory. Read `work/feature_list.json`, `work/progress/current.md`, `work/history/`, `epics/`, and `releases/` first.

---

## Notes

- Product capabilities track outcomes close to epics/releases; engineering items may stay 🔴/🟡 without an epic until they become a priority.
- **Content / i18n:** all six `content-i18n` Features are Done in be/fe `feature_list.json`. Epic `content-i18n` and release `0.2.0` remain Planned with stale Pending rows — cutting `0.2.0` would formalize the v1 ship without more Feature work.
- Transitional FE labels (`human-readable-labels`, `extend-human-readable-labels`) are superseded as SoT; Content resolve owns narrative.
- **Symbol Reading** is the primary product surface (epics 9–11 Done). Remaining visual gap is Planned epic `dashboard-mockup-parity` (H1–H5 Open).
- `shared/openapi.json` was not updated when MessageRef landed on BE DTOs — that keeps **Cross-repo contract** yellow.
- Add a new row only if you can define **Target for green**, name evidence sources, and act on a low score.
