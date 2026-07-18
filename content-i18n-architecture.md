# Content & Internationalization Module

## Goal

Single owner of **what** the system communicates and **how** it is rendered in each locale/channel.

Domain emits facts. Content module emits language. Clients never invent copy.

---

## Core contract

```text
MessageRef = { code, params?, severity?, channel? }
ResolvedMessage = { code, locale, text, meta? }
```

| Field | Role |
|-------|------|
| `code` | Stable semantic id (`catalog.strategy_allowed`) |
| `params` | Typed data only (enums, numbers, ids) — never prose |
| `severity` | `info` \| `warning` \| `error` \| … |
| `channel` | `ui` \| `email` \| `push` \| `sms` \| `log` \| … |

**Rule:** APIs expose `MessageRef` (or arrays of them). Rendering happens only in the Content module.

---

## Placement

`shared/` is **not** a Content host today (OpenAPI snapshot only). v1 lives in the FE as a bounded module, then extracts.

```text
v1 (market-data-fe)              later (scale)
─────────────────────────────    ────────────────────────────
market-data-fe/                  content-service (HTTP/gRPC)
  content/                         same MessageRef + resolve contract
    catalogs/                      separate deploy; FE (and others) call it
    templates/
    resolve()
```

- **One responsibility:** catalogs + templates + resolve.
- **Not in BE domain:** no f-strings, no locale JSON inside analyzers.
- **Not in FE feature mappers/screens:** no ad-hoc code→label maps; those call Content.
- **Not in `shared/` for v1:** do not bootstrap `shared` just to hold Content.
- **Not brand chrome:** `@market-data/brand` wording stays design-system / shared UI chrome; analysis and catalog narrative go through Content.
- Evolution path: FE in-process module → dedicated service without changing `MessageRef` / `resolve` shape.

Keep Content as a clear directory/package boundary inside FE so extraction is a move, not a rewrite.

---

## Architecture

```text
Domain / use-cases
    │  emit MessageRef { code, params }
    ▼
HTTP DTOs (MessageRef on the wire)
    ▼
Content module  ── catalogs (codes) + templates (locale × channel)
    │  resolve(locale, channel, MessageRef) → ResolvedMessage
    ▼
Consumers: FE UI (v1) · later emails · push · API errors · reports · logs
```

```text
v1
┌─────────────┐   MessageRef    ┌──────────────────────────────┐
│  BE domain  │ ──────────────► │  market-data-fe               │
└─────────────┘                 │    content/ (catalogs,        │
                                │              templates,       │
                                │              resolve)         │
                                │         │                     │
                                │         ▼                     │
                                │    UI screens / VMs           │
                                └──────────────────────────────┘

later
┌─────────────┐   MessageRef    ┌──────────────────┐
│  BE domain  │ ──────────────► │  content-service │
└─────────────┘                 └────────┬─────────┘
         ┌───────────────────────────────┼────────────────┐
         ▼                               ▼                ▼
    FE (UI locale)                 Email/Push         Error body
```

---

## Capabilities (design for; ship incrementally)

| Capability | v1 | Later |
|------------|----|-------|
| Code catalogs (closed sets) | ● | |
| Locale templates (`en`, …) | ● | `es`, … |
| Param interpolation + formatters (number, %, date) | ● | |
| Fallback (missing key → code / default locale) | ● | |
| Channels (ui first) | ● | email, push, sms, in-app |
| Plurals / gender / select (ICU) | ○ | ● |
| Rich text / markdown slots | ○ | ● |
| Nested / composed messages | ○ | ● |
| A/B / experiment copy variants | ○ | ● |
| Tone / brand voice packs | ○ | ● |
| Accessibility (aria strings) | ○ | ● |
| Legal / compliance message versions | ○ | ● |
| Preview / glossary / translation workflow | ○ | ● |
| Runtime remote catalog fetch + cache | ○ | ● (with service) |
| Audit: which code was shown to whom | ○ | ● |

Do not shrink the contract to “enum → short label only.” Labels are one template shape among many.

---

## Catalog model (schematic)

```text
content/
  codes/
    catalog.strategy_allowed.yaml      # semantics, params schema, severity
    analysis.regime.trend_up.yaml
    error.validation.invalid_symbol.yaml
  templates/
    en/ui/catalog.strategy_allowed.ftl   # or ICU / JSON — pick one format
    en/email/...
  formatters/                            # number, percent, list join, …
```

Path is under `market-data-fe/` in v1 (exact folder naming is an FE implementation choice). After extraction, the same tree moves to the content-service package.

- **Code** = product meaning (owned with domain experts).
- **Template** = locale × channel wording (owned with content/i18n).
- Domain may reference codes; it never owns template text.

---

## Resolve API (stable)

```text
resolve({ locale, channel, messages: MessageRef[] }) → ResolvedMessage[]
```

In-process inside FE (v1) or HTTP (later). Same input/output.

---

## Changes required

### Backend

| Change | Notes |
|--------|-------|
| Stop emitting prose (`catalog_reason`, hard-gate f-strings, etc.) | Replace with `MessageRef` |
| Domain/catalog builders return codes + params | Numbers/enums as params, not interpolated English |
| HTTP DTOs: `message: { code, params }` (and lists where needed) | Reflect in OpenAPI; sync `shared/openapi.json` as the API snapshot |
| Drop display English from analysis/classification modules | Domain stays language-agnostic |
| Optional: BE calls Content resolve only for server-rendered channels | Emails/notifications — after Content is a service; not required for UI v1 |
| Errors: machine `code` + optional `MessageRef` | Clients resolve; no hardcoded error sentences in routes |

### Frontend

| Change | Notes |
|--------|-------|
| Own the Content module (catalogs, templates, `resolve`) | Bounded module inside `market-data-fe`, not scattered feature maps |
| Remove feature-local DTO→label maps as SoT | Prefer Content catalogs/resolve |
| Mappers pass through `MessageRef` (or resolve once at boundary) | VM holds resolved text **or** refs + locale |
| Screens bind resolved strings only | No code maps in components |
| Locale selection → input to resolve | Single locale pipeline |
| Retire / fold current `analysisLabels.*` ad-hoc catalogs into Content | Migration, not patch on top |
| Formatters live in Content | One place for `%`, lists, dates |

### `shared/` (contracts snapshot only)

| Change | Notes |
|--------|-------|
| Keep OpenAPI API snapshot in sync when BE DTOs gain `MessageRef` | Not a Content package host in v1 |
| No catalogs / templates / `resolve` here until (if ever) a shared package is intentionally bootstrapped | Prefer FE module → content-service |

### Out of this module

- Trading / analysis logic  
- Layout / design-system chrome tokens (brand may still own product name; feature narrative goes through Content)  
- Auth, storage, transport  

---

## v1 scope (minimal vertical)

1. Content module in `market-data-fe` with `en` + `ui` channel (contract + catalogs + `resolve`).  
2. `MessageRef` in analysis catalog + assessment summaries that today are prose/dumps (BE).  
3. FE resolves at the app boundary via the in-repo Content module; screens bind resolved text.  
4. No email/push/A/B yet — contract already allows them; extraction path is open.

---

## Principles

1. **One place** for communication meaning and wording.  
2. **Codes + params** cross the wire; text is derived.  
3. **Channel- and locale-ready** from day one; implement channels later.  
4. **Replace**, don’t patch, current FE label helpers / BE f-strings.  
5. **Extractable** to a service without redesigning the contract (FE module is a temporary home, not the forever SoT location).
