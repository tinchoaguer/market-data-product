---
id: 5
slug: design-system-shell
title: Design System & App Shell
outcome: Product UI uses a shared brand kit and a consistent app shell with navigation across main views.
status: Done
retrospective: true
repos: [market-data-brand, market-data-fe]
---

# Design System & App Shell

## Outcome

Brand ships consumable UI primitives; FE wraps Market Data / Analysis / Classification in one layout.

## Features

| Repo | Slug | Status | Release |
|------|------|--------|---------|
| market-data-brand | brand-ui-kit | Done | 0.1.0 |
| market-data-fe | app-layout | Done | 0.1.0 |

## Acceptance (product)

- [x] FE consumes brand UI primitives without installing Tailwind in the app.
- [x] Users navigate between Market Data, Analysis, and Classification inside a shared shell.
