# 01 — System Architecture

## Baseline
Use a modular monolith with explicit bounded contexts unless the actual repository has a proven alternative. PostgreSQL is the authoritative transactional database. Redis is only for cache, sessions, short-lived coordination and rate limits. Private object storage holds documents/media/exports. Background workers plus a transactional outbox process notifications, exports, reconciliation and asynchronous workflows.

## Logical modules
Platform/Super Admin; Organisation; Identity/RBAC; Subscription/Entitlements; Property; Hotel PMS; Restaurant/POS; Kitchen/KDS; Housekeeping/Maintenance; Inventory/Procurement; Finance; HR; CRM/Loyalty; BI; Guest Portal; QR; Payments; Notifications; Support; Audit.

## Deployment path
Browser/mobile → CDN/WAF/TLS → Web/API ingress → application runtime → PostgreSQL/Redis/private object storage. Workers consume durable jobs/outbox. Provider webhooks enter dedicated verified endpoints. Keep dev/test/staging/production accounts and secrets separate.

## Request evaluation order
1. Authenticate principal/session (or validate public QR capability).
2. Resolve tenant context from trusted membership or opaque capability.
3. Validate property/outlet/resource scope.
4. Check platform tenant state and platform overrides.
5. Check subscription entitlement.
6. Check tenant feature configuration.
7. Check user permission and resource ownership/state.
8. Validate input, idempotency key and aggregate version.
9. Execute domain command transactionally.
10. Write audit/outbox with the state change; publish side effects after commit.

## Domain boundaries
Each domain owns its tables and rules. Other modules call its services or consume its events. Avoid direct cross-module table writes. Finance owns GL; Inventory owns stock ledger; Hotel owns folios; Restaurant owns menu/order/session; KDS owns station execution; HR owns employment/payroll calculation; CRM owns canonical customer/consent/loyalty; BI owns read models, not operational mutation.

## Consistency
- Use a DB transaction for changes within one domain.
- Use outbox with at-least-once delivery and idempotent consumers for integration events.
- Use compensating actions for payment/refund/folio/stock/journal workflows.
- Use optimistic concurrency for reservations, room assignments, table sessions and stock.
- BI/notifications can be eventually consistent; show freshness/status.
- Provider calls happen outside long DB transactions.

## Failure policy
Provider outage must leave payment pending/failed, never paid by assumption. Outbox lag must alert but not erase committed data. Redis outage cannot lose business facts. BI lag shows stale timestamp. Object-store failure keeps document/export pending or failed and private. DB outage fails writes closed.

## Architecture decision required
Inspect actual code/stack, database version/ORM, auth/session design, hosting, queue and storage before choosing concrete versions or rewriting. Record any deviation from modular monolith baseline in an ADR with evidence.
