# 03 — Domain Ownership and Events

## Source-of-truth matrix
| Domain | Owner | Consumers |
|---|---|---|
| Tenant/properties/outlets | Organisation | all modules |
| Plans/entitlements/platform overrides | Platform Subscription | every feature gate |
| Employee/employment | HR | access links, schedules, Finance payroll posting |
| Application access | Identity/RBAC | all workspaces |
| Reservation/stay/occupancy | Hotel PMS | Housekeeping, CRM, Finance, Guest Portal |
| Room clean/maintenance readiness | Housekeeping | PMS, guest request status |
| Menu/order/table session | Restaurant POS | KDS, Inventory, Finance, CRM, BI, Guest Portal |
| KOT/station execution | KDS | POS order status, BI |
| Stock/recipe movements | Inventory | POS availability, Finance, BI |
| GL/AR/AP/journals | Finance | BI and operational summaries |
| Customer/consent/loyalty | CRM | PMS, POS, Guest Portal, BI |
| QR token/target lifecycle | QR engine | POS, PMS, Guest Portal |
| Provider payment state | Payment domain | POS, PMS, Finance, Guest Portal |
| Analytics/read models | BI | authorised readers only |

## Event envelope
Required fields: `event_id` UUID, stable versioned `event_type`, `occurred_at` UTC, `tenant_id`, optional `property_id`/`outlet_id`, aggregate type/id/version, correlation/causation IDs, actor type/id and schema version. Payload is minimal. Never include secrets, card data, OTP, raw QR token, full guest documents or unnecessary PII.

## Core event catalogue
`organisation.created`, `property.created`, `outlet.created`, `subscription.entitlement_changed`, `tenant.suspended`, `reservation.confirmed`, `reservation.cancelled`, `stay.checked_in`, `stay.checked_out`, `folio.charge_posted`, `room.cleaning_completed`, `room.inspection_passed`, `maintenance.resolved`, `table.session_opened`, `table.session_closed`, `order.placed`, `order.accepted`, `order.cancelled`, `kot.started`, `kot.ready`, `order.completed`, `inventory.stock_movement_posted`, `inventory.item_unavailable`, `purchase_order.approved`, `goods_receipt.posted`, `payment.captured`, `payment.failed`, `payment.refunded`, `payment.reconciled`, `journal.posted`, `accounting_period.closed`, `employee.created`, `employee.terminated`, `payroll.processed`, `customer.identity_verified`, `customer.consent_changed`, `loyalty.points_posted`, `loyalty.reward_redeemed`, `guest_request.submitted`, `support.ticket_created`.

## Cross-domain posting rules
- POS sale may trigger inventory consumption using recipe version captured at sale.
- Restaurant order is revenue source even when settled via room folio; folio is a settlement route, not duplicate revenue.
- Hotel owns folio records; Finance owns official journal.
- Inventory owns stock; Finance receives approved valuation postings.
- HR calculates payroll; Finance posts accounting journals/payments.
- CRM owns loyalty entries and only awards on qualifying completed/settled transaction event.
- BI consumes only; it does not mutate source transactions.

## Delivery guarantees
Assume at-least-once delivery. Use transactional outbox, unique event ID, idempotent consumers, retry/backoff, dead-letter queue, alerting and audited replay. A consumer must prove idempotency under duplicate event delivery.
