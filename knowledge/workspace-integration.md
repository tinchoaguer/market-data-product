# Workspace integration

`product` and `shared` own how isolated repos connect. Adopting repos (BE, FE, Brand) must not document sibling paths or workspace topology in their Specs or knowledge.

## OpenAPI snapshot (`shared/openapi.json`)

After public BE HTTP DTO / OpenAPI-affecting changes, refresh the snapshot from the running BE app schema (`create_app().openapi()` or equivalent). This is a product/shared or release handoff — never a BE Feature REQ.

## Brand package on FE (local workspace)

FE declares a **package** dependency on `@market-data/brand` (semver), not a sibling `file:` path.

For local development in this workspace (until Brand is published to a registry):

```bash
# From market-data-brand (build UI kit if needed, then link)
npm run build
npm link

# From market-data-fe
npm link @market-data/brand
```

Product/Release may later replace link with a published version pin. Do not reintroduce `file:../market-data-brand` into FE `package.json` as SoT.

## Favicon / logo assets

Brand ships logo/favicon via the `@market-data/brand` package (or exported assets). FE consumes the package; copy steps that name sibling repo folders belong here or in Release notes — not in FE README as repo paths.
