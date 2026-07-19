---
id: 6
slug: market-data-surfaces
title: Market Data Surfaces
outcome: Users can inspect market data, analysis, and classification for a selected symbol in the product UI.
status: Done
retrospective: true
repos: [market-data-fe]
---

# Market Data Surfaces

## Outcome

Three primary product screens wired to the corresponding backend APIs, with loading and error states.

## Features

| Repo | Slug | Status | Release |
|------|------|--------|---------|
| market-data-fe | view-market-data | Done | 0.1.0 |
| market-data-fe | view-market-analysis | Done | 0.1.0 |
| market-data-fe | view-market-classification | Done | 0.1.0 |

## Acceptance (product)

- [x] Symbol selection drives fetch for each surface.
- [x] Core sections for data / analysis / classification are visible on success.
- [x] Loading and error/retry behaviors exist per screen.
