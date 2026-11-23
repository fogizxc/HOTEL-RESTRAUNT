# HospitalityOS — Engineering Handoff

**Version:** 1.0.0 specification baseline  
**Status:** All 233 Stitch design pages and the design-system file are imported and committed. Product/domain specifications are provided. Application implementation and production verification remain pending.  
**Scope:** Cloud-only, multi-tenant hotel and restaurant SaaS with 12 core workspaces and shared supporting experiences.

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
13. `operations/` integration, deployment, migration and incident runbooks
14. `decisions/OPEN-DECISIONS-REGISTER.md`
15. `../frontend/` imported Stitch source, inventory and traceability

## Product baseline
- Exactly 12 core workspaces: SaaS Super Admin; Organisation Owner/Management; Hotel PMS/Front Desk; Restaurant POS/Floor; KDS; Housekeeping/Maintenance; Inventory/Procurement; Finance/Accounts; HR; CRM/Guest Relationships; Reports/BI; Customer/Guest Portal.
- One organisation/tenant can own multiple properties; each property can contain multiple restaurant outlets.
- Hotel-only, restaurant-only and combined subscriptions are supported.
- Subscription entitlements, platform overrides, tenant configuration and employee permissions are independent layers.
- Modular monolith + PostgreSQL are the recommended baseline. Redis, private object storage, workers and a transactional outbox are supporting infrastructure.
- One shared QR engine supports table and room targets, plus future configured service locations.
- Visual baseline: premium Apple-inspired HospitalityOS. The actual Stitch export is the source of truth for the supplied page designs.

## Verified frontend source
The uploaded `stitch_hospitalityos_design_system.zip` passed integrity validation and was extracted. It contains:
- **233** `code.html` screen exports in 233 screen folders.
- **1** `hospitalityos_design_system.md`.
- Original source archive SHA-256: `25e258f98a84424c0a414dcd99eead4b2ddcfc2f4c37b5c74b497b8a4ee1f198`.

All exported pages are individually committed under:
`frontend/stitch-export/stitch_hospitalityos_design_system/`

Use:
- `frontend/SCREEN-INVENTORY.csv` for exact source paths, titles, panel assignment, file sizes and page SHA-256 hashes.
- `frontend/SCREEN-TRACEABILITY.csv` for one implementation-mapping row per screen.
- `frontend/SCREEN-TRACEABILITY.md` for the definition of mapped.
- `frontend/STITCH-IMPORT-INSTRUCTIONS.md` for the source import and conversion requirements.

Route, role, data owner, API operation, permission and E2E fields in the traceability CSV are intentionally left as `TO_BE_*` where these cannot be grounded until the actual app router, service layer, API contract and RBAC catalogue are audited. Do not invent identifiers.

The original ZIP is preserved at the repository root as `stitch_hospitalityos_design_system.zip`; it is not duplicated inside this extracted documentation folder.

## Important completeness notice
This is a detailed engineering specification, **not an implemented application**. It does not include a completed application backend, executable production schema migrations, configured live payment/messaging integrations, production database, production secrets, deployed infrastructure or executed security/test evidence. Tests listed here are required tests, not tests already run.

## Source-of-truth hierarchy
1. Owner-approved decisions recorded in `decisions/`.
2. Product/domain invariants and panel specifications in this package.
3. The approved Stitch export for exact visual and page inventory.
4. Implementation decisions recorded as ADRs.

When a visual mock conflicts with a security or data invariant, preserve the invariant and record the mismatch for owner/design review.

## Supporting application pages
- `docs/07-SUPPORTING-SCREENS-AND-GLOBAL-UTILITIES.md` defines authentication, onboarding, profile, notification, support, subscription lifecycle and recovery pages.
- `frontend/STITCH-IMPORT-INSTRUCTIONS.md` defines the design-reference conversion rules.
- `frontend/SCREEN-TRACEABILITY.csv` maps each supplied screen to implementation fields that engineering must resolve from the real app.

## Additional implementation controls
- `operations/DATABASE-MIGRATION-RUNBOOK.md` defines PostgreSQL migration and RLS rollout rules.
- `testing/WORKFLOW-ACCEPTANCE-MATRIX.csv` provides initial test IDs and expected outcomes; all are **Not run** until implemented and executed.
- `security/ROLE-PERMISSION-MATRIX-TEMPLATE.csv` is a starting matrix, not a complete final role catalogue.
- `docs/IMPLEMENTATION-STATUS-TEMPLATE.md` and `docs/DECISION-LOG-TEMPLATE.md` are project tracking templates.

## API status
`contracts/openapi.yaml` is intentionally a partial OpenAPI 3.1 baseline for representative critical operations. It is not a complete specification for every route. Expand and validate it against the selected application stack before implementation release.

## Package structure
- `panels/` — 12 panel-specific functional/UI/workflow requirements and acceptance tests.
- `engines/` — shared subscription, QR, ordering, payment, inventory and CRM engine contracts.
- `docs/` — product scope, architecture, tenancy, domain ownership, database, API, workflows and supporting pages.
- `security/` — threat model and security control baseline.
- `testing/` — test plan, negative cases and release gates.
- `operations/` — integrations, deployment, migration, observability and incident procedures.
- `decisions/` — unresolved owner decisions.
- `contracts/` — partial OpenAPI starter contract.
- `../frontend/` — 233 Stitch screen exports, design system, inventory and traceability.

## Local validation state
The HTML exports and Markdown specifications were checked as source artifacts. No application build, database migration, API server, live provider transaction, security test suite or production integration has been run as part of package authoring.
