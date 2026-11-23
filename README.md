# HospitalityOS — Complete Frontend & Engineering Handoff

This repository contains the original Google Stitch export, the 12-panel engineering specifications, shared-engine contracts, and the screen-by-screen design source.

## All 233 frontend pages are committed individually

Browse:
- `frontend/stitch-export/stitch_hospitalityos_design_system/` — all 233 original `code.html` screen exports plus `hospitalityos_design_system.md`.
- `frontend/SCREEN-INVENTORY.csv` — exact screen folders, titles, workspace assignment, file sizes and SHA-256 hashes.
- `frontend/SCREEN-TRACEABILITY.csv` — one row per screen. Implementation route, API operation, allowed role, permission and E2E test fields must be populated after auditing the actual application router, services, OpenAPI contract and RBAC catalogue; they are intentionally not fabricated.
- `handoff/panels/` — 12 detailed workspace specifications.
- `handoff/engines/` — shared subscription, QR, order/KOT/billing, payment/folio, inventory and CRM contracts.
- `handoff/docs/`, `handoff/security/`, `handoff/testing/`, `handoff/operations/`, `handoff/contracts/`, and `handoff/decisions/` — architecture, database, tenancy, API, security, acceptance tests, release gates, deployment and open decisions.
- `handoff/CLAUDE_MASTER_IMPLEMENTATION_PROMPT.md` — implementation agent instructions.

## Downloads

**Original Stitch source ZIP:** `stitch_hospitalityos_design_system.zip`  
This is the original uploaded file. It contains 233 `code.html` exports and one `hospitalityos_design_system.md`. Its integrity was checked before extraction. The same source pages are individually browseable in `frontend/stitch-export/`.

**Complete engineering handoff ZIP:** `HospitalityOS_COMPLETE_MASTER_PACKAGE_v1.0.0.zip`  
This contains the consolidated frontend source and engineering documentation. The compact outer ZIP contains `HospitalityOS_SOURCE.tar.xz`; unzip the outer package, then run:

```bash
tar -xJf HospitalityOS_SOURCE.tar.xz
```

Start with `README.md` in the extracted source root.

## Screen inventory

| Workspace | Pages |
|---|---:|
| 01 — SaaS Super Admin | 18 |
| 02 — Organisation Owner & Management | 18 |
| 03 — Hotel PMS & Front Desk | 26 |
| 04 — Restaurant POS & Floor Management | 20 |
| 05 — Kitchen Display System | 21 |
| 06 — Housekeeping & Maintenance | 21 |
| 07 — Inventory & Procurement | 18 |
| 08 — Finance & Accounts | 18 |
| 09 — HR & Employee Management | 15 |
| 10 — CRM & Guest Relationships | 15 |
| 11 — Reports & Business Intelligence | 14 |
| 12 — Customer & Guest Portal | 16 |
| 13 — Shared Supporting Pages | 13 |
| **Total** | **233** |

## Engineering status and limitations

The Stitch HTML files are visual design references, not a working application. The Markdown files are engineering specifications and a Claude implementation handoff; they do not prove that the backend, migrations, integrations, security controls, or automated tests have already been implemented.

Before production release, engineering must:
1. Inspect all 233 screen exports and the complete design-system specification.
2. Complete screen-to-route, API, domain, permission and test traceability using the actual application code.
3. Implement and validate database migrations, domain services, APIs, integrations and UI behavior.
4. Run required tenancy-isolation, security, functional, accessibility, performance, backup/restore and end-to-end tests.
5. Resolve open owner decisions and satisfy the documented release gates.

Do not claim production readiness merely because the design exports and documentation are present.

## Integrity

- Original Stitch ZIP SHA-256: `25e258f98a84424c0a414dcd99eead4b2ddcfc2f4c37b5c74b497b8a4ee1f198`
- Complete handoff ZIP SHA-256 (committed version): `99725a6a140213ca0c7036d548ffff4346f4aea9f8b572e95b449124af400976`
