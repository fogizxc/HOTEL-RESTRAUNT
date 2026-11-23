# HospitalityOS — Engineering Handoff

**Version:** 0.1.0 specification baseline  
**Status:** Product/domain specification; Stitch source import and implementation validation remain required.  
**Scope:** Cloud-only, multi-tenant hotel and restaurant SaaS with 12 workspaces.

## Reading order
1. `CLAUDE_MASTER_IMPLEMENTATION_PROMPT.md`
2. `docs/00-PRODUCT-SCOPE-BASELINE.md`
3. `docs/01-SYSTEM-ARCHITECTURE.md`
4. `docs/02-TENANCY-IDENTITY-AND-RBAC.md`
5. `docs/03-DOMAIN-OWNERSHIP-AND-EVENTS.md`
6. `docs/04-DATABASE-MODEL-AND-INVARIANTS.md`
7. `docs/05-API-CONTRACTS.md` and `contracts/openapi.yaml`
8. `docs/06-CROSS-PANEL-WORKFLOWS.md`
9. `engines/` shared domain contracts
10. `panels/01` through `panels/12`
11. `security/SECURITY-THREAT-MODEL.md`
12. `testing/TEST-PLAN-AND-RELEASE-GATES.md`
13. `operations/` integration, deployment and runbooks
14. `decisions/OPEN-DECISIONS-REGISTER.md`
15. `frontend/` source import and screen traceability

## Product baseline
- Exactly 12 core workspaces: SaaS Super Admin; Organisation Owner/Management; Hotel PMS/Front Desk; Restaurant POS/Floor; KDS; Housekeeping/Maintenance; Inventory/Procurement; Finance/Accounts; HR; CRM/Guest Relationships; Reports/BI; Customer/Guest Portal.
- One organisation/tenant can own multiple properties; each property can contain multiple restaurant outlets.
- Hotel-only, restaurant-only and combined subscriptions are supported.
- Subscription entitlements, platform overrides, tenant configuration and employee permissions are independent layers.
- Modular monolith + PostgreSQL are the recommended baseline. Redis, private object storage, workers and a transactional outbox are supporting infrastructure.
- One shared QR engine supports table and room targets, plus future configured service locations.
- Visual baseline: premium Apple-inspired HospitalityOS (porcelain/off-white, white surfaces, graphite text, restrained blue accent, consistent spacing and typography). Actual Stitch export is the visual source of truth once supplied.

## Important completeness notice
This is a detailed engineering specification, **not an implemented application**. It does not include an application backend, schema migrations, API server, configured payment gateway, production database, test execution evidence, production secrets or deployed infrastructure. Tests listed here are required tests, not tests already run.

The actual HospitalityOS Stitch export was not present in the accessible conversation or Library at package creation time. Exact source screen filenames, generated screen titles, routes, visual assets and viewport variants are therefore not included or certified. Place the unmodified Stitch ZIP/export under `frontend/source/` and complete `frontend/SCREEN-TRACEABILITY.csv` from real files. Never fabricate source pages or screenshots.

## Source-of-truth hierarchy
1. Owner-approved decisions recorded in `decisions/`.
2. Product/domain invariants and panel specifications in this package.
3. Approved Stitch export for exact visual/page inventory.
4. Implementation decisions recorded as ADRs.

When a visual mock conflicts with a security or data invariant, preserve the invariant and record the mismatch for owner/design review.

`PACKAGE-MANIFEST.json` contains file sizes and SHA-256 checksums. Regenerate whenever the package changes.

## Supporting application pages
- `docs/07-SUPPORTING-SCREENS-AND-GLOBAL-UTILITIES.md` defines shared authentication, onboarding, profile, notification, support, subscription lifecycle and recovery pages.
- `frontend/STITCH-IMPORT-INSTRUCTIONS.md` defines how to inventory the actual Stitch export and map each screen to implementation requirements.
- `frontend/SCREEN-TRACEABILITY.md` explicitly records that exact exports are not yet imported; templates are provided but must be populated from real source files.

## Additional implementation controls
- `operations/DATABASE-MIGRATION-RUNBOOK.md` defines PostgreSQL migration and RLS rollout rules.
- `testing/WORKFLOW-ACCEPTANCE-MATRIX.csv` provides initial test IDs and expected outcomes; all rows are **Not run** until implemented and executed.
- `security/ROLE-PERMISSION-MATRIX-TEMPLATE.csv` is a starting matrix, not a complete final role catalogue.
- `docs/IMPLEMENTATION-STATUS-TEMPLATE.md` and `docs/DECISION-LOG-TEMPLATE.md` are project tracking templates.

## API status
`contracts/openapi.yaml` is intentionally a partial OpenAPI 3.1 contract baseline for representative critical operations. It is not yet a complete specification for every route and must be expanded and validated against the actual selected application stack before implementation release.

## Package structure
- `panels/` — 12 panel-specific functional/UI/workflow requirements and acceptance tests.
- `engines/` — shared subscription, QR, ordering, payment, inventory and CRM engine contracts.
- `security/` — threat model and security control baseline.
- `testing/` — test plan, negative cases and release gates.
- `operations/` — integrations, deployment, migration and incident procedures.
- `decisions/` — unresolved decisions requiring owner approval.
- `frontend/` — Stitch import requirements and screen traceability templates.

## Local validation state
The Markdown package and starter OpenAPI file are documentation artifacts. No application build, database migration, API server, security test suite or production integration has been run as part of package authoring. See the package manifest for the exact files included.


## Frontend source — 233 Stitch screens

The provided Stitch archive has now been imported and verified. It contains **233 `code.html` screen exports across 233 screen folders**, plus `hospitalityos_design_system.md`. All screen files are preserved under `frontend/stitch-export/stitch_hospitalityos_design_system/`; the original ZIP is preserved under `frontend/source-archive/`.

Use `frontend/SCREEN-INVENTORY.csv` for the exact inventory and `frontend/SCREEN-TRACEABILITY.csv` to map every screen to the actual route, role, domain, API, permission and automated test. The traceability sheet deliberately leaves implementation-specific fields unassigned until the actual application router, API and RBAC catalog are inspected.

### Verified screen counts
- CRM & Guest Relationships: 15
- Customer & Guest Portal: 16
- Finance & Accounts: 18
- HR & Employee Management: 15
- Hotel PMS & Front Desk: 26
- Housekeeping & Maintenance: 21
- Inventory & Procurement: 18
- Kitchen Display System: 21
- Organisation Owner & Management: 18
- Reports & Business Intelligence: 14
- Restaurant POS & Floor Management: 20
- SaaS Super Admin: 18
- Shared Supporting Pages: 13
