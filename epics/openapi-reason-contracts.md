---
id: 4
slug: openapi-reason-contracts
title: OpenAPI Reason Contracts
outcome: Closed reason/regime code sets are documented in OpenAPI as the contract for client-facing codes.
status: Done
retrospective: true
repos: [market-data-be, shared]
---

# OpenAPI Reason Contracts

## Outcome

Analysis and classification expose enum-closed reason (and regime) codes in OpenAPI so clients do not scrape domain code.

## Features

| Repo | Slug | Status |
|------|------|--------|
| market-data-be | classification-reason-codes | Done |
| market-data-be | analysis-reason-codes | Done |

## Acceptance (product)

- [x] Classification signal reasons are closed sets in HTTP DTOs / OpenAPI.
- [x] Analysis regime labels and regime reason codes are closed sets in HTTP DTOs / OpenAPI.

## Notes

Precursor to Content & Internationalization: codes exist; language ownership is not yet centralized.
