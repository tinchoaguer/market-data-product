# Update Maturity (Market Data product)

Use this when the user asks to **create, refresh, or re-score** the product Capability Maturity Checklist.

The checklist lives at `product/capability-maturity.md`. It does **not** replace Epics, Releases, `feature_list.json`, or the harness Orchestrator.

---

## Model

```text
Evidence (work/ + epics/ + releases/)  →  Score bands  →  capability-maturity.md
```

| Artifact | Role |
|----------|------|
| `capability-maturity.md` | SoT for maturity scores and “green” targets |
| `work/feature_list.json` (be / fe / brand) | Feature Done / Pending evidence |
| `work/progress/`, `work/history/` | Active stage + completed feature evidence |
| `product/epics/`, `product/releases/` | Cross-repo outcomes and ship cuts |
| `product/knowledge/` | Target architecture vs current state |

**Never** invent scores from memory. **Never** mark Features Done or ship Releases as a side effect of a maturity update.

---

## Legend (fixed)

| Band | Color | Range |
|------|-------|-------|
| Absent / nascent | 🔴 Red | 0–25% |
| Partial / in progress | 🟡 Yellow | 25–75% |
| Near mature | 🔵 Blue | 75–90% |
| Mature | 🟢 Green | 90–100% |

Map each capability to a band by comparing evidence to that row’s **Target for green**. Pick a % inside the band; prefer round numbers (10, 15, 20, …, 95).

---

## When to update

Update when:

- the user runs `update maturity` (or close variants), **or**
- a Release is marked Shipped and maturity should reflect the new cut, **or**
- a major Epic moves Planned → Active / Done and scores would change.

Do **not** update on every tiny Feature edit unless the user asks.

---

## Steps

1. Read `product/capability-maturity.md` (current rows, targets, Notes).
2. Gather evidence (read-only) from:
   - `market-data-be/work/feature_list.json`
   - `market-data-fe/work/feature_list.json`
   - `market-data-brand/work/feature_list.json`
   - each repo’s `work/progress/current.md` (and recent `work/history/` / `work/progress/reviews/` if needed)
   - `product/epics/index.json` + relevant `product/epics/<slug>.md`
   - `product/releases/index.json` + relevant `product/releases/<slug>.md`
   - `product/knowledge/` when a capability has an architecture target (e.g. content-i18n)
   - light repo signals for engineering rows: test configs, CI workflows, health/docs (do not deep-audit source unless needed)
3. For each existing capability row:
   - Re-evaluate **Evidence** (short, factual).
   - Set **%** and **Status** emoji to match the legend band.
   - Keep **Target for green** stable unless the user explicitly redefines maturity.
4. Add a new capability row **only if**:
   - the user asks, **or**
   - a durable gap is clearly product-relevant,
   - and you can write Target for green + evidence sources + why a low score would change priority.
5. Remove or merge rows only if the user asks (prefer Notes for superseded approaches).
6. Set **Last reviewed** to today’s date (`YYYY-MM-DD`).
7. Update **Notes** when scoring depends on transitional work (e.g. labels vs Content).
8. Write the updated `product/capability-maturity.md` preserving the two-section structure:
   - Product capabilities
   - Engineering maturity

---

## Scoring guidance

### Product capabilities

| Signal | Typical band |
|--------|----------------|
| No Features / epic only Planned | 🔴 |
| Transitional solution Done; target architecture Pending | 🟡 |
| Most Features Done; small gaps or thin surface | 🔵 |
| Acceptance / green target met for current product horizon | 🟢 |

Use epic + release status as context; Feature statuses in `feature_list.json` are stronger evidence for shipped capability.

### Engineering maturity

| Signal | Typical band |
|--------|----------------|
| Absent tooling or docs | 🔴 |
| Present in some repos / manual only | 🟡 |
| Consistent across adopting repos; minor holes | 🔵 |
| Enforced and documented for how you actually ship | 🟢 |

Examples:

- **Testing:** FE suite + BE pytest → yellow/blue; no CI → keep CI row red even if tests exist.
- **Version & release management:** `releases/` with Shipped cuts but TBD tags → yellow; pins + tags on every Shipped cut → green.
- **Security / Observability:** may remain red without an epic; still score honestly.

---

## File shape (`product/capability-maturity.md`)

Keep this structure (tables may grow; columns stay fixed):

```markdown
# Capability Maturity Checklist

…

| Last reviewed | YYYY-MM-DD |
| Method | Evidence from work/ + epics/ + releases/ + knowledge/ |
| Agent | agents/maturity-writer.md |

## Legend
… (unchanged bands)

## Product capabilities
| Capability | Status | % | Evidence | Target for green |
| …

## Engineering maturity
| Capability | Status | % | Evidence | Target for green |
| …

## How to refresh
…

## Notes
…
```

---

## Quality bar

- Every % sits in the correct color band.
- Evidence cites concrete artifacts (feature slugs, release versions, epic slugs) — not vibes.
- Target for green stays outcome-oriented and observable.
- No edits to `work/`, `specs/`, source, or release/epic status as part of this playbook.
- Prefer few honest rows over a large decorative list.

---

## After updating

Tell the user:

1. Path of `capability-maturity.md` and the new **Last reviewed** date.
2. Which capabilities changed band or % (brief before → after).
3. Top 1–3 lowest scores that would most improve product maturity if prioritized next.
4. Reminder: Features still run via `Start Feature <slug>`; Releases via `create release` / release-writer.

Agent entrypoint: `product/agents/maturity-writer.md` (see `product/deployment.md`).
