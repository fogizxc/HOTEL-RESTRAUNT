# Test Plan, Security Matrix and Release Gates

## 1. Test levels
1. Unit: pure business rules, validation, calculation, state transition.
2. Database: migrations, constraints, RLS, transactions, locking, indexes, rollback.
3. Service integration: domain-to-domain events, outbox, queue retries, provider adapters.
4. API contract: OpenAPI schema, auth, validation, pagination, errors, idempotency.
5. UI component: form validation, loading/error/empty states, permission rendering.
6. End-to-end: critical journeys across panels and role profiles.
7. Security: tenant isolation, IDOR, RBAC, injection, session, file, webhook, QR abuse.
8. Performance: load, soak, concurrency, queue backlog, slow queries.
9. Recovery: backup restore, worker restart, provider outage, failed migration rollback.

## 2. Mandatory tenancy matrix
Create tenants A and B with multiple properties and outlets each. Create users scoped as tenant owner, property manager, outlet manager, front desk, server, kitchen, housekeeping, inventory, finance, HR, CRM, BI, guest A, guest B, platform support and platform owner.
For every list/detail/create/update/delete/export/download/search/report endpoint, attempt:
- Tenant A user accesses Tenant B resource ID.
- Property A user accesses sibling property resource.
- Outlet A user accesses sibling outlet resource.
- Guest A accesses Guest B order/booking/bill/profile.
- Staff accesses unassigned room/order/employee.
- Background job runs with missing/wrong tenant context.
Expected: deny without data leakage; audit sensitive attempts; no side effects.

## 3. Domain-specific mandatory tests
### Platform and entitlements
- Platform deny beats plan grant and tenant preference.
- Feature entitlement does not bypass RBAC.
- RBAC does not grant unavailable module.
- Concurrent quota reservations do not exceed cap.
- Plan version changes preserve active subscription snapshots.

### Hotel PMS
- Overlapping confirmed room allocation rejected under concurrency.
- Cancel/no-show/checkout transitions are guarded.
- Rate/tax snapshot immutable.
- Room cannot be checked in while out of order without audited exception.
- Folio balance and deposit allocation cannot double count.
- Night audit idempotent by property business date.

### Restaurant/POS/KDS
- Duplicate submit creates one order/KOT.
- Table QR participants cannot access other diners' orders.
- Item-level prep states aggregate correctly across stations.
- Cancellation/re-fire event preserves history.
- Split bill totals reconcile to parent total.
- Cash remains pending until collection.

### QR/room service
- Entropy/guessing; revoked/expired token; rate limit; token not in logs.
- Table token scope mismatch rejected.
- Room QR alone reveals no occupant/folio data.
- Room charge denied for inactive stay/unverified guest/room mismatch.
- QR bulk generation respects quota under race.

### Inventory
- Duplicate consumption event posts once.
- Negative stock denied by default.
- Expired/quarantined batch unavailable.
- Transfer in-transit state and receiving.
- Recount adjustment is ledger-based.
- Recipe version edit doesn't rewrite prior COGS snapshot.

### Finance/payments
- Journal debits=credits; closed period denied.
- Posted journal immutable; reversal traceable.
- Webhook signature/amount/currency/replay validation.
- Provider redirect alone does not confirm payment.
- Refund cannot exceed remaining captured amount.
- Capture vs settlement discrepancy enters exception queue.
- Folio charge does not duplicate restaurant revenue.

### HR/CRM
- Employee cannot access another payslip/documents.
- Offboarding revokes sessions/roles promptly.
- Attendance correction preserves original.
- Consent withdrawal blocks queued campaign.
- Duplicate identity is not auto-merged.
- Loyalty earn/redeem is idempotent and concurrency safe.

### BI
- BI query scope cannot be broadened through client filters.
- Financial reporting reconciles to GL.
- Shared/scheduled report permissions are reevaluated at execution/send.
- Stale/missing data is disclosed.

## 4. Frontend/UI QA
After actual Stitch export is added, create exact route/screen inventory and test every screen at 390px, 1024px and 1440px. Check navigation, keyboard flow, focus, contrast, semantic labels, overflow, dialogs, loading/empty/error/success/denied states. Target WCAG 2.2 AA; test with automated tooling and manual keyboard/screen-reader sampling.

## 5. CI required checks
- Formatting and lint.
- Type check.
- Unit tests.
- Database migration up/down on clean ephemeral database.
- API/contract validation.
- Integration tests.
- Tenant/RBAC negative test suite.
- SAST/dependency/secret scans.
- Build and artifact scan.
- E2E smoke tests on staging.

Never mark a check passed unless an actual command ran and returned success. Store logs/artifacts and environment/version details.

## 6. Release gates
### Gate 0 — inputs confirmed
- Stitch export inspected; route inventory and design tokens captured.
- Stack, region, legal entity/tax, payment provider, messaging providers and retention decisions approved.

### Gate 1 — architecture foundation
- Tenant scope, identity, RBAC, entitlement engine, migration framework and audit/outbox implemented and tested.

### Gate 2 — core operational integrity
- PMS, POS, KDS, housekeeping, inventory, finance and HR critical workflows implemented with source-of-truth boundaries.

### Gate 3 — customer and engagement
- Customer portal, CRM, QR, payment and privacy workflows implemented; real provider sandbox tests pass.

### Gate 4 — production hardening
- Performance/load targets agreed and met; backup restore, DR, monitoring, incident runbook, vulnerability remediation and external penetration test completed.

### Gate 5 — go-live authorisation
- Production secrets, provider merchant accounts, legal terms/privacy notices, data migration, support rota, rollback plan and owner sign-off.

## 7. Definition of Done
A feature is done only when UI route, API contract, domain service, persistent model/migration, authorization, audit/event handling, error states, unit/integration/E2E tests, documentation and observability are implemented. Design completion alone is not implementation completion.
