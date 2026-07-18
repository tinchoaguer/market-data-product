---
id: 7
slug: client-label-humanization
title: Client Label Humanization
outcome: FE maps known DTO codes to locale strings so users see readable labels instead of raw API tokens (transitional approach).
status: Done
retrospective: true
repos: [market-data-fe]
---

# Client Label Humanization

## Outcome

Short-term FE catalogs + `resolveLabel` humanize analysis/classification codes in the mapping layer.

## Features

| Repo | Slug | Status |
|------|------|--------|
| market-data-fe | human-readable-labels | Done |
| market-data-fe | extend-human-readable-labels | Done |

## Acceptance (product)

- [x] Known analysis regime labels/reasons display as human text.
- [x] Classification reasons, strategies, exposure factors, and component names are humanized where catalogued.
- [x] Unknown codes fall back to raw code without failing the screen.

## Notes

**Superseded direction:** Content & Internationalization (`content-i18n`). This epic is historical; new work should not extend ad-hoc FE label maps as the long-term SoT.
