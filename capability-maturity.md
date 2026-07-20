# Capability Maturity Checklist

Product-level view of how mature Market Data is across **product capabilities** and **engineering hygiene**.

This file is SoT for maturity scores. Refresh with `update maturity` (playbook: [`UPDATE_MATURITY.md`](UPDATE_MATURITY.md)).

| Field | Value |
|-------|-------|
| Last reviewed | 2026-07-20 |
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
| Backend platform & market APIs | 🟢 | 95 | BE `fastapi-bootstrap` … `market-data-analysis-api` Done; release `0.1.0` Shipped | Runnable REST APIs with DTO contracts for market-data, analysis, classification |
| Analysis & classification engine | 🟢 | 95 | `analysis-classification-pipeline` Done; APIs consume pipeline | Deterministic analysis → classification path; no domain leak into HTTP |
| OpenAPI / reason contracts | 🟢 | 95 | `classification-reason-codes`, `analysis-reason-codes` Done | Closed reason/regime sets documented in OpenAPI as client SoT for codes |
| Design system & UI kit | 🔵 | 85 | brand `brand-ui-kit` Done (Button, Select, Card, Table, Badge, Skeleton, Alert) | Full agreed primitive set + documented consumer contract (no Tailwind in FE) |
| App shell & market surfaces | 🟢 | 90 | FE `app-layout` + three view Features Done | Shell navigates MD / Analysis / Classification with loading/error states |
| Content management | 🔴 | 10 | Epic `content-i18n` Planned; FE `content-*` + BE `*-message-refs` Pending | Content module owns catalogs + resolve; MessageRef is SoT for narrative |
| Internationalization | 🟡 | 40 | Transitional locale labels Done (`human-readable-labels`, `extend-…`); Content resolve not shipped | `resolve(locale, channel)`; no feature-local code→English maps as SoT |
| Components / UI definitions | 🔵 | 80 | Brand kit exports primitives + CSS; Studio/lab preview in brand work | Stable public exports; FE consumes kit without private Tailwind toolchain |

---

## Engineering maturity

| Capability | Status | % | Evidence | Target for green |
|------------|--------|---|----------|------------------|
| Documentation | 🔵 | 80 | Product README, epics, releases, knowledge architectures; per-repo specs/history | Onboarding + architecture notes current; release notes match shipped cuts |
| Testing | 🟡 | 55 | FE Vitest suite (screens/mappers/labels); BE pytest configured; brand coverage thinner; no product CI | Meaningful automated tests per adopting repo; failures block merge |
| Version & release management | 🟡 | 50 | `releases/0.1.0` Shipped (pins filled, git tags historically optional); `0.2.0` Planned with TBD pins | Every Shipped cut has repo pins + git tags; index status in sync |
| Harness / SDD workflow | 🔵 | 85 | `feature_list` + specs + history/reviews in be, fe, brand | Consistent Start Feature → Complete across PROJECT_ROOTs |
| Cross-repo contract (shared OpenAPI) | 🟡 | 60 | `shared/` OpenAPI snapshot; sync called out in content-i18n epic | Contract sync enforced when public DTOs change (e.g. MessageRef) |
| Security | 🔴 | 15 | Local/demo posture; no product security epic or auth surface documented | Threats for current deploy model addressed (secrets, deps, auth as needed) |
| CI / quality gates | 🔴 | 10 | No `.github` workflows in be/fe/brand product repos | CI runs tests (and lint) on PR for adopting repos |
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
- Transitional FE labels raise **Internationalization** into yellow but keep **Content management** red until MessageRef + Content resolve land (release `0.2.0`).
- Add a new row only if you can define **Target for green**, name evidence sources, and act on a low score.
