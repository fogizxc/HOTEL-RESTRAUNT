# HospitalityOS — Complete Frontend & Engineering Handoff

This repository contains the original Google Stitch export and the consolidated HospitalityOS engineering handoff for all 12 workspaces.

## Downloads

### 1. Original Google Stitch export
**File:** `stitch_hospitalityos_design_system.zip`

This is the original uploaded Stitch archive. It contains:
- **233 individual `code.html` page exports**
- **1 `hospitalityos_design_system.md`**
- Original screen-folder names and paths retained

Download it from the repository root and unzip it once to inspect the original Stitch screens.

### 2. Complete implementation handoff
**File:** `HospitalityOS_COMPLETE_MASTER_PACKAGE_v1.0.0.zip`

This consolidated package contains the 233 frontend page exports plus the implementation documentation:
- 12 panel specifications.
- 6 shared-engine specifications.
- Architecture, modular boundaries, multi-tenancy and data isolation.
- Database model and invariants.
- API contracts and OpenAPI starter.
- RBAC and permissions templates.
- QR, ordering, payment, room-folio, inventory, loyalty, finance and HR rules.
- Security threat model, test matrix, acceptance criteria and release gates.
- Deployment, migration, integration and operations runbooks.
- Screen inventory and screen-to-implementation traceability sheets.

**Extraction note:** to keep the complete handoff compact, this outer ZIP contains `HospitalityOS_SOURCE.tar.xz`. First unzip the outer package, then extract the source archive with:

```bash
tar -xJf HospitalityOS_SOURCE.tar.xz
```

Start with `HospitalityOS_Engineering_Handoff/README.md` inside the extracted source.

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

The Stitch export is a visual design reference, not a working application. The Markdown files are engineering specifications and an implementation handoff, not proof that the backend, migrations, integrations, security controls, or automated tests have already been implemented.

Before production release, the implementation agent must:
1. Import and inspect all 233 screens.
2. Complete the screen-to-route, API, domain, permission, and test traceability.
3. Implement and validate database migrations, domain services, APIs, integrations, and UI behavior.
4. Run the required security, tenancy-isolation, functional, accessibility, performance, backup/restore, and end-to-end tests.
5. Resolve owner decisions and satisfy the documented release gates.

Do not claim production readiness merely because the design exports and documentation are present.

## Integrity

- Original Stitch ZIP SHA-256: `25e258f98a84424c0a414dcd99eead4b2ddcfc2f4c37b5c74b497b8a4ee1f198`
- Complete handoff ZIP SHA-256: `99725a6a140213ca0c7036d548ffff4346f4aea9f8b572e95b449124af400976`

