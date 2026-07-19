---
id: 3
slug: market-apis
title: Market APIs
outcome: Clients can fetch market data, analysis, and classification over REST using HTTP DTOs.
status: Done
retrospective: true
repos: [market-data-be]
---

# Market APIs

## Outcome

Stable HTTP surfaces for the three product reads: market-data, analysis, classification.

## Features

| Repo | Slug | Status | Release |
|------|------|--------|---------|
| market-data-be | market-data-api | Done | 0.1.0 |
| market-data-be | market-data-analysis-api | Done | 0.1.0 |
| market-data-be | classification-api | Done | 0.1.0 |

## Acceptance (product)

- [x] Each endpoint accepts a symbol and returns DTOs (not domain models).
- [x] Invalid requests return appropriate HTTP status codes.
