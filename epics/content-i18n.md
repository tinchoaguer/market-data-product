---
id: 8
slug: content-i18n
title: Content & Internationalization
outcome: One Content module owns system language — domain emits MessageRef (code + params); clients never invent copy. Extractable to a service later.
status: Planned
retrospective: false
repos: [market-data-fe, market-data-be]
architecture: ../content-i18n-architecture.md
---

# Content & Internationalization

## Outcome

Centralize communication: `MessageRef → resolve(locale, channel) → text`. BE stops emitting prose; FE stops owning DTO→label maps as SoT. v1 Content lives as a bounded module in `market-data-fe`; contract allows a later content-service.

## Architecture

See [`content-i18n-architecture.md`](../content-i18n-architecture.md).

## Features (planned)

Order is suggested; each row is one harness Feature in that repo (N per repo allowed).


| Order | Repo           | Slug                         | Intent                                                                                           | Status  |
| ----- | -------------- | ---------------------------- | ------------------------------------------------------------------------------------------------ | ------- |
| 1     | market-data-fe | content-message-contract     | `MessageRef` / `ResolvedMessage` schemas + code registry shape inside FE Content module          | Pending |
| 2     | market-data-fe | content-catalogs-v1          | Catalogs + `en`/`ui` templates + in-process `resolve` in FE Content module                       | Pending |
| 3     | market-data-be | analysis-message-refs        | Replace catalog/assessment prose and dumps with MessageRefs in analysis DTOs                     | Pending |
| 4     | market-data-be | classification-message-refs  | Classification (and errors as needed) emit MessageRefs, not display English                      | Pending |
| 5     | market-data-fe | content-resolve-boundary     | App boundary uses Content `resolve`; drop ad-hoc label SoT (`analysisLabels.*`, etc.)            | Pending |
| 6     | market-data-fe | analysis-classification-copy | Wire analysis/classification VMs to resolved messages (summaries, rationale, levels as designed) | Pending |


> Slugs confirmed in each repo’s `work/feature_list.json` (status `Pending`). Run `Start Feature <slug>` per PROJECT_ROOT in the order above.
>
> `shared/` stays the OpenAPI snapshot only; sync it when BE DTOs change. It is not a Content PROJECT_ROOT for this epic.


## Acceptance (product)

- [ ] APIs expose structured `MessageRef` (code + params) for user-facing narrative that today is free-text or token dumps.
- [ ] Content module (in FE for v1) is the only place that turns refs into locale/channel text.
- [ ] FE screens bind resolved strings (or resolve once at the boundary); no feature-local code→English maps as SoT.
- [ ] BE domain/catalog builders do not emit interpolated English.
- [ ] Contract supports additional locales/channels later without redesign.
- [ ] Path to extract Content as a separate service remains open (same resolve API).

## Out of scope (v1)

- Email/push/SMS channels (contract-ready only)
- A/B copy, ICU plurals, translation workflow UI
- Full migration of brand chrome wording into Content (optional later)
- Bootstrapping `shared/` as a Content package host

## Notes

Retrospective epic `client-label-humanization` is the temporary FE approach; replace, do not patch on top, when this epic ships.
