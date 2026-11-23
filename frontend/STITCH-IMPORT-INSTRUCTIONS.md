# Stitch frontend source import — completed

## Source
- Original upload: `stitch_hospitalityos_design_system.zip` (validated before extraction)
- Extracted source files are preserved individually in this package; the redundant original ZIP container is not duplicated inside the complete package.
- Extracted source: `frontend/stitch-export/stitch_hospitalityos_design_system/`
- Design-system specification: `frontend/stitch-export/stitch_hospitalityos_design_system/hospitalityos_design_system.md`

## Verified inventory
- ZIP integrity check: passed.
- Individual `code.html` screen exports: **233**.
- Screen folders: **233** (one `code.html` per screen folder).
- Design-system Markdown file: 1.
- Total non-directory source files in supplied ZIP: 234.

## Screen counts by workspace
- CRM & Guest Relationships: **15**
- Customer & Guest Portal: **16**
- Finance & Accounts: **18**
- HR & Employee Management: **15**
- Hotel PMS & Front Desk: **26**
- Housekeeping & Maintenance: **21**
- Inventory & Procurement: **18**
- Kitchen Display System: **21**
- Organisation Owner & Management: **18**
- Reports & Business Intelligence: **14**
- Restaurant POS & Floor Management: **20**
- SaaS Super Admin: **18**
- Shared Supporting Pages: **13**

## Engineering handling rules
1. Treat these HTML files as authoritative visual references, not as a running application.
2. Preserve all 233 screens. Do not omit screens because two appear similar.
3. Build reusable components and responsive routes in the actual application stack; do not mount the static exports as the production app.
4. Read the full design-system file before implementing frontend components.
5. Use `SCREEN-INVENTORY.csv` for the exact screen folder/path, title, panel assignment, byte size and SHA-256.
6. Use `SCREEN-TRACEABILITY.csv` to fill in the actual route, roles, data owner, OpenAPI operation IDs, permission codes and E2E acceptance tests after inspecting the implementation repository. The `TO_BE_*` values are deliberately not fabricated.
7. Verify HTML behavior, external fonts/icons/images and asset licensing. Replace non-durable preview assets with owned/licensed production assets.
8. Do not assume page interactions or back-end persistence exist just because the static HTML includes controls.
