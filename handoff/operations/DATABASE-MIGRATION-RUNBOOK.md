# PostgreSQL Database and Migration Runbook

## 1. Database baseline
PostgreSQL is the recommended transactional system of record. Confirm actual version, extensions, hosting and connection mode during repository audit. Redis is not authoritative storage. Use UTC timestamps and property timezone/business-day configuration separately.

## 2. Migration creation
For each schema change:
1. State domain/table owner and business reason.
2. Identify tenant/property/outlet scope, data classification and RLS implications.
3. Define constraints, indexes, foreign keys and expected cardinality.
4. Assess lock duration, table size, index creation method and backfill volume.
5. Add migration with forward and safe rollback strategy.
6. Test on clean database and prior supported release schema.
7. Test RLS with real application DB role, background worker role and privileged migration role.
8. Verify indexes and query plans for high-volume access paths.
9. Document backfill, compatibility window and feature flag.
10. Record evidence and commit SHA.

## 3. Tenant RLS requirements
- Enable and FORCE ROW LEVEL SECURITY for tenant tables where feasible.
- Application role must not own tables or have BYPASSRLS.
- Set tenant context transaction-locally; use `SET LOCAL` or a secure connection-local mechanism cleared before pooled connection reuse.
- Every transaction starts with validated authenticated tenant context.
- Missing context returns no tenant rows/denies writes.
- Composite foreign keys ensure tenant consistency even if RLS is misconfigured.
- Test all queries through the actual runtime role, not only DB owner role.

## 4. Append-only ledgers
Audit, stock, folio and posted journal rows must reject UPDATE/DELETE through DB privileges and/or triggers. Corrections are new reversal/compensation rows with source reference and actor/reason.

## 5. Reservation and stock concurrency
- Room availability must use exclusion constraints or serialised allocation/locking to prevent overlap races.
- Stock reservation/issue must lock or atomically check item/location/batch balance.
- Table sessions require unique active session constraint per table under agreed session state semantics.
- Quota counters and unique idempotency keys must be transactionally enforced.

## 6. Connection and transaction hygiene
- Use bounded connection pools and transaction timeouts.
- Never hold database transaction open during remote payment/email/SMS/file provider call.
- Use outbox pattern for external effects.
- Retry only safe/idempotent operations; distinguish serialization failure/deadlock/constraint conflict from business rejection.

## 7. Backup/restore
Schedule encrypted database backups and PITR if selected. Restrict backup service identity. Test restore into isolated environment; validate extension versions, role grants, RLS policies, counts, ledger invariants and application smoke tests. Document measured restore duration and point-in-time loss. Never call backup complete solely based on provider green indicator.
