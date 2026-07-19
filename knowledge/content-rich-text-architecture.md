# Content Rich Text & Decorators

Design note for a **future Epic** that extends Content & Internationalization with inline emphasis and other text decorators.

**Depends on:** [`content-i18n-architecture.md`](./content-i18n-architecture.md) (`MessageRef → resolve → ResolvedMessage`).  
**Prerequisite product epic:** `content-i18n` (plain `text` resolve must exist first).

This file is planning-only. It does not create Features or change harness state.

---

## Goal

Allow Content templates to **remark fragments** of resolved copy (bold, italic, and a small closed set of inline decorators) without domain emitting prose or markup, and without feature screens inventing substring styling.

Domain still emits facts. Content still owns language — including which spans are emphasized.

---

## Why not in content-i18n v1

`content-i18n` ships:

```text
ResolvedMessage = { code, locale, text, meta? }
```

with plain interpolated strings. Rich text / markdown slots and nested messages are explicitly **later** in that architecture. This note is the place to design that later slice so a new Epic can reference a stable contract instead of inventing one under pressure.

---

## Principles

1. **Templates own decoration.** Bold/italic live in locale × channel templates (or in structured segments derived from them), never in BE params.
2. **Params stay typed data.** Numbers, enums, ids, dates — no HTML, markdown, or display English in `MessageRef.params`.
3. **Closed decorator set.** Only a small, channel-safe vocabulary (see below). No arbitrary HTML from catalogs or callers.
4. **Channel-aware rendering.** `ui` may render spans; `sms` / `log` strip or ignore decorators; `email` may allow a slightly wider but still closed set.
5. **Same resolve API shape.** Extend `ResolvedMessage`; do not invent a parallel “pretty string” path that bypasses Content.
6. **Replace, don’t patch.** Do not layer ad-hoc `<strong>` wrapping in screens on top of plain Content text.

---

## Contract extension (proposed)

Keep `MessageRef` unchanged:

```text
MessageRef = { code, params?, severity?, channel? }
```

Extend resolve output:

```text
ResolvedMessage = {
  code,
  locale,
  text,                 # plain fallback (decorators stripped or flattened)
  format?,              # "plain" | "inline-rich"  (default plain)
  segments?,            # present when format = inline-rich
  meta?
}

ResolvedSegment =
  | { type: "text", value: string }
  | { type: "strong", value: string }      # bold
  | { type: "em", value: string }          # italic / cursive
  | { type: "code", value: string }        # optional, monospace token
  # later (opt-in): "link" only if Content owns href policy
```

| Field | Role |
|-------|------|
| `text` | Always usable plain string (a11y, logs, copy-paste, SMS). |
| `format` | Tells consumers whether `segments` is authoritative for UI. |
| `segments` | Ordered inline runs; concatenation of `value`s equals `text` (modulo whitespace rules). |

**Rule:** Screens that need decoration bind `segments` (or a Content-provided React helper). Screens that do not care use `text`. No feature-local markdown parsers on arbitrary DTO fields.

---

## Template strategies (pick one in the Epic Spec)

### A — Markdown-inline in templates (authoring ergonomics)

```text
# en/ui/analysis.regime.trend_up  (schematic)
Price is in **{regime}** with *rising* momentum.
```

Resolve pipeline:

1. Interpolate params via Content formatters (typed).
2. Parse a **restricted** markdown-inline subset (`**strong**`, `*em*`, optional `` `code` ``).
3. Emit `format: "inline-rich"` + `segments`, plus flattened `text`.

Pros: easy for content authors. Cons: parser must reject links/HTML/images unless explicitly allowed later.

### B — Explicit placeholders / ICU-like markup

```text
Price is in <strong>{regime}</strong> with <em>rising</em> momentum.
```

Same resolve output. Pros: unambiguous AST. Cons: noisier templates; still must sanitize to closed tags only.

### C — Semantic param roles (emphasis on values only)

Template marks which **param** is emphasized; static words stay plain:

```text
{symbol} closed <strong>{change}</strong> today.
```

Use when “remark” always means “highlight this datum,” not arbitrary mid-sentence prose. Can combine with A/B for static emphasized words.

**Recommendation for a first Epic:** Strategy A with a hard-denied list (no raw HTML, no images, no arbitrary links). Expose `segments` so UI does not re-parse markdown in every screen.

---

## Allowed decorators (v1 of rich-text Epic)

| Decorator | Segment `type` | UI hint | Channels |
|-----------|----------------|---------|----------|
| Bold | `strong` | `<strong>` / brand weight token | `ui`, maybe `email` |
| Italic | `em` | `<em>` | `ui`, maybe `email` |
| Inline code | `code` | monospace token | `ui` |
| (none) | — | strip to `text` | `sms`, `log`, `push` (unless push template is plain-only) |

Out of decorator v1 unless a later revision:

- Links / anchors (needs href allowlist + phishing policy)
- Colors / severity coloring (use `MessageRef.severity` + brand styles, not inline color in copy)
- Block elements (paragraphs, lists, headings)
- Embedded components / JSX in catalogs

Severity and visual tone stay orthogonal: `severity` on the ref drives chrome (icon, color); inline decorators only change weight/style within the sentence.

---

## Ownership & placement

Same as Content module (FE bounded package in content-i18n v1 → content-service later):

```text
content/
  codes/
  templates/
  formatters/
  rich/                    # parser or segment builder (new)
    inlineMarkdown.ts      # or equivalent — Content-owned only
    stripDecorators.ts
```

| Layer | May | Must not |
|-------|-----|----------|
| BE domain / DTOs | Emit `MessageRef` | Emit markdown/HTML/prose |
| Content resolve | Produce `text` + `segments` | Leak unsafe HTML |
| FE screens / VMs | Bind `text` or render `segments` via Content/UI helper | Own code→label maps or ad-hoc `**` wrapping |
| Brand | Supply typography tokens for `strong`/`em` | Own analysis narrative catalogs |

---

## UI binding (schematic)

```text
resolve(...) → ResolvedMessage
    │
    ├─ format === "plain"     → render message.text
    └─ format === "inline-rich"
            → ContentInlineRich({ segments })
                 maps strong/em/code → brand-safe elements
```

Prefer one shared renderer (Content module or thin brand-aware wrapper) so every screen does not reimplement segment mapping.

---

## Security & safety

- Parse only Content-owned template output after interpolation — never trust client-supplied markdown as a message body.
- Disallow HTML passthrough; if Strategy B uses tags, whitelist `strong` / `em` / `code` only.
- `text` must remain safe for attributes and non-HTML channels.
- No `dangerouslySetInnerHTML` on raw template strings.

---

## Suggested Epic shape (when ready)

Outcome sketch: *Resolved analysis/classification (and other Content) copy can emphasize fragments via a closed inline-rich format; plain `text` remains available everywhere.*

| Order | Repo | Slug (proposed) | Intent |
|-------|------|-----------------|--------|
| 1 | market-data-fe | content-inline-rich-contract | Extend `ResolvedMessage` with `format` + `segments`; plain fallback rules |
| 2 | market-data-fe | content-inline-rich-resolve | Template → segment pipeline (restricted markdown or explicit tags) in Content module |
| 3 | market-data-fe | content-inline-rich-renderer | Shared UI helper to render segments with brand typography |
| 4 | market-data-fe | content-inline-rich-migration | Opt-in templates that need emphasis (analysis/classification narrative first) |

BE usually **unchanged** if MessageRefs already carry codes/params. Only add BE work if some API today ships pre-formatted HTML/markdown that must be removed.

`shared/`: update OpenAPI only if `ResolvedMessage` is ever exposed on the wire (not required while resolve stays FE-local).

---

## Acceptance (product sketch)

- [ ] Domain/API still expose only `MessageRef` (code + typed params).
- [ ] Decorated copy is produced only by Content resolve from templates.
- [ ] UI can render bold/italic (and optional inline code) via `segments` without feature-local markdown.
- [ ] Every rich message still has a correct plain `text` for fallback channels and a11y.
- [ ] Non-UI channels do not leak raw markdown/HTML.
- [ ] Contract remains extractable with Content to a future content-service.

---

## Out of scope (for that future Epic)

- Full markdown / MDX / CMS blocks
- Translation workflow UI, ICU plurals (separate later capabilities in content-i18n)
- Brand chrome wording migration into Content
- A/B copy variants, tone packs
- Colored spans, badges, or chips embedded in message templates

---

## Relation to content-i18n

| Doc | Owns |
|-----|------|
| `content-i18n-architecture.md` | Codes, catalogs, plain resolve, channels, extraction path |
| **This file** | Inline decorators / rich segments on top of that resolve |

When creating the Epic, set frontmatter roughly:

```yaml
architecture: ../knowledge/content-rich-text-architecture.md
```

and mention dependency on epic `content-i18n` (at least through FE resolve + catalogs).
