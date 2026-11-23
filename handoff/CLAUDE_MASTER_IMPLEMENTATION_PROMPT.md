# MASTER IMPLEMENTATION INSTRUCTIONS — HOSPITALITYOS

You are the lead engineering agent implementing HospitalityOS from this specification and a separately supplied Google Stitch frontend export. Work as a senior SaaS engineer: exact, incremental, test-driven and honest about actual results.

## 0. Mandatory preflight
Before coding:
1. Inspect repository, branch, current commit, working tree, package manifests, CI, deploy configuration and existing code.
2. Locate the original HospitalityOS Stitch ZIP/extracted folder. Search `frontend/source/`, `INPUTS/`, repository root and attached workspace files. Do not assume one filename.
3. Inventory every real export file, exact screen name, viewport variant, asset reference and duplicate/variant relationship. Record in `frontend/SCREEN-TRACEABILITY.csv`.
4. Read the design-system file completely. Preserve the approved Apple-inspired HospitalityOS system. Do not treat static HTML/screenshots as a functioning frontend.
5. Read all 12 panel docs, domain contracts, security rules and open decisions.
6. If the Stitch export is missing, report it as a blocker. Continue safe domain implementation planning if possible, but do not invent exact screens or claim page mapping is complete.
7. Do not run destructive DB resets, production writes, live payment transactions or production deployment during audit.

## 1. Product outcome and non-negotiables
Build one cloud-only, multi-tenant SaaS for hotel-only, restaurant-only and combined hotel+restaurant organisations. One tenant may own multiple properties; one property may contain multiple outlets. Implement exactly the 12 workspaces defined in `README.md` and `panels/`.

Tenant isolation is mandatory. UI hiding is never authorization. Every API read/write validates principal, tenant, property, outlet, resource ownership, entitlement, tenant configuration and employee/guest permission as applicable. Use PostgreSQL RLS plus application checks.

The universal QR engine is a shared domain subsystem. Table and room QR tokens are cryptographically random, opaque and revocable. Multiple guests at one table can place separate orders into one active table session, while each guest can only read their own order/payment data. A room QR is not guest authentication. Room folio charging requires an active eligible stay plus an authorised guest/service session.

## 2. Architecture baseline
Prefer a modular monolith with explicit bounded contexts unless the existing repository has a proven architecture. PostgreSQL is authoritative for transactional data. Redis is cache/session/short-lived coordination only. Private object storage holds documents and exports. Use background jobs and a transactional outbox. Do not introduce microservices, Cassandra or MongoDB simply because there are many panels. Any deviation needs an ADR documenting scale, reliability, team ownership, consistency and operations.

No cross-module direct writes. Each domain owns its tables, invariants, services and events. See `docs/03-DOMAIN-OWNERSHIP-AND-EVENTS.md`.

## 3. Implementation phases
### Phase 0 — Repository and source audit
Verify repo, branch, framework, DB, auth, storage, payment, workers, CI and tests. Inventory all Stitch files/tokens. Create `docs/IMPLEMENTATION-STATUS.md`, `docs/DECISION-LOG.md`, `docs/SCREEN-TRACEABILITY.csv`, `docs/REQUIREMENT-TRACEABILITY.csv` and `docs/RISK-REGISTER.md`.

### Phase 1 — Platform foundation
Tenant, organisation, property, outlet and legal entity; identity/sessions/MFA/invitations; RBAC and scopes; subscription entitlement evaluator; audit; idempotency; outbox; RLS; cross-tenant negative tests.

### Phase 2 — Shared engines
Reservation/room inventory; order/table session/KOT; QR lifecycle; payment adapter/webhooks/refunds; stock ledger/recipes; Finance journal boundaries; CRM identity/consent/loyalty; notification/support infrastructure.

### Phase 3 — Workspace domains
Implement each `panels/` specification. Panels are role-scoped surfaces; do not duplicate domain logic.

### Phase 4 — Guest portal
Implement public booking, menu, table QR, room QR, account, loyalty and guest services. Enforce least disclosure and room-folio authorization.

### Phase 5 — BI and integrations
Metric dictionary, read models, exports, provider adapters, signed webhooks, freshness and reconciliation.

### Phase 6 — Hardening and release
Run security, tenancy, accessibility, performance, migration, backup/restore, integration, staging and release gates. Production requires explicit owner approval.

## 4. Engineering rules
- Define acceptance criteria and test IDs before each phase.
- Run tests after each coherent change; fix failures before continuing.
- Never claim a test passed without execution output/evidence.
- Every migration must have safe forward/backward strategy and tests.
- Money uses integer minor units or exact decimal, never binary float.
- Store UTC timestamps; retain property timezone/business-day rules separately.
- Use idempotency for order, payment, refund, webhook, inventory, journal and folio writes.
- Use version checks/optimistic concurrency for booking, table session, stock and payments.
- Audit privileged/financial/HR/entitlement/QR changes; append-only for ledgers and audits.
- Never log secrets, OTPs, card details, session tokens, raw QR tokens, private documents or unnecessary PII.
- External provider calls must not be held inside long DB transactions; use state machine + outbox/saga.
- A browser redirect is not proof of payment. A hidden button is not authorization.
- A successful UI mock is not production integration evidence.

## 5. Every screen implementation contract
For every imported source file:
1. Map exact source file to stable route, panel, viewport, user role, task and domain owner.
2. Map required permissions, feature entitlements, tenant/property/outlet scope and API operations.
3. Implement using shared design tokens/components while preserving panel-specific layouts.
4. Implement applicable loading, empty, error, success, validation, disabled and forbidden states.
5. Wire every visible control to an implemented action or explicitly mark as unavailable/placeholder.
6. Use API data, not hardcoded fake production metrics.
7. Implement responsive breakpoints at 390px, 1024px and 1440px plus intermediate widths.
8. Add E2E and accessibility tests; update traceability with evidence.

## 6. Mandatory quality gates
- PostgreSQL RLS and tenant isolation tests pass.
- IDOR/resource-scope negative tests pass for every resource family.
- Entitlement and RBAC negative tests pass.
- Payment signature, replay, idempotency, amount/currency, refunds and reconciliation tests pass.
- QR entropy/revocation/expiry/guest isolation/room folio tests pass.
- Inventory and accounting invariants pass.
- Migrations install/upgrade/rollback in clean test DB.
- OpenAPI validation and API contract tests pass.
- E2E workflows cover all 12 panels.
- No unresolved critical/high security findings at release.
- Accessibility is tested against WCAG 2.2 AA target; do not claim compliance by visual inspection.
- Backup restore drill succeeds; monitoring, alerts and incident owners exist.
- Staging smoke test passes before production authorization.

## 7. Progress files
Maintain `docs/IMPLEMENTATION-STATUS.md`, `docs/DECISION-LOG.md`, `docs/SCREEN-TRACEABILITY.csv`, `docs/REQUIREMENT-TRACEABILITY.csv`, `docs/RISK-REGISTER.md`, `docs/PHASE-REPORTS/PHASE-N.md` and `docs/TEST-EVIDENCE/`. Each phase report lists changed files, migration, endpoint, tests executed/pass/fail, known issues, next tasks and commit SHA.

## 8. Owner decisions required
Stop before hardcoding any production decision about payment provider/merchant, tax jurisdiction, cancellation/no-show/deposit/refund rules, room-guest verification, legal entity accounting, payroll statutory rules, retention/privacy, MFA/dual approval thresholds, offline mode, RPO/RTO/SLO, subscription pricing/quotas or scan analytics retention. Use configurable adapters meanwhile and mark unconfigured.

## 9. Definition of done
The product is not done when screens compile. It is done only when every real exported screen maps to implemented behaviour, all domain invariants have tests, cross-panel workflows pass, security/accessibility/performance evidence exists, providers are configured and verified, backups restore, monitoring/runbooks are ready, owner decisions are approved and production release gates are signed off.
