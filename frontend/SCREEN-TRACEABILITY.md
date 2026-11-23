# Frontend screen traceability

The original Stitch export has been imported and checked. The inventory contains **233 screens** distributed across 12 primary workspaces plus shared supporting pages.

- `SCREEN-INVENTORY.csv`: immutable source inventory including folder, title, file path, file size and SHA-256.
- `SCREEN-TRACEABILITY.csv`: engineering mapping worksheet prefilled for every one of the 233 screens.
- `STITCH-IMPORT-INSTRUCTIONS.md`: source integrity and implementation rules.
- `stitch-export/`: extracted original screens and design-system document.
- Source archive integrity and SHA-256 are recorded in `PACKAGE-MANIFEST.json`; extracted page files are preserved individually.

## Required completion fields
For every screen, engineering must fill and review: actual route, allowed roles, domain/system of record, API operation IDs, permission codes, required UI states, E2E test IDs, implementation status and reviewer. Do not invent route names or endpoint identifiers before inspecting the actual router and OpenAPI contract.

## Definition of mapped
A screen is mapped only when its route resolves in the app, its access scope is specified, its source-of-truth domain and API contract are named, its loading/empty/error/success/denied states are defined, and an automated test covers the key user journey. A static Stitch export alone does not satisfy this definition.
