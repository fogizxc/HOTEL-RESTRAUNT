# 06 — Cross-Panel Workflows and State Machines

## 1. Tenant provisioning
Super Admin creates tenant + owner invitation + plan/entitlements + permitted quotas. Provisioning must be atomic or resumable with visible state. Owner accepts, verifies identity, sets credentials/MFA, configures business type, properties, outlets, branding, employees, roles and operational defaults. Failed partial provisioning is visible and retryable; no partially active tenant without explicit state.

## 2. Hotel reservation-to-checkout
`QUOTE → INVENTORY_HOLD → PAYMENT_PENDING → CONFIRMED → ARRIVAL_DUE → CHECKED_IN → IN_HOUSE → CHECKED_OUT → CLOSED`.
Alternative explicit outcomes: `HOLD_EXPIRED`, `CANCELLED`, `NO_SHOW`, `PAYMENT_FAILED`, `EARLY_CHECKOUT`. Revalidate room/rate at confirmation; use bounded holds; check-in validates reservation, room readiness, identity policy and deposit; creates stay/folio; guest requests hand off to Housekeeping/Maintenance; checkout resolves folio according to policy.

## 3. Room readiness
Occupancy and room operational status are separate. Example room readiness states: `DIRTY`, `CLEANING_IN_PROGRESS`, `INSPECTION_REQUIRED`, `INSPECTED_READY`, `OUT_OF_ORDER`, `MAINTENANCE_IN_PROGRESS`. Assignment alone never makes a room ready. Required cleaning/inspection must pass and no blocking maintenance may remain. PMS consumes readiness, Housekeeping owns status.

## 4. Restaurant order
`DRAFT → SUBMITTED → ACCEPTED → IN_PREPARATION → READY → SERVED/PICKED_UP/DELIVERED → COMPLETED`. Alternative outcomes: `REJECTED`, `CANCEL_REQUESTED`, `CANCELLED`, `PARTIALLY_CANCELLED`, `PAYMENT_PENDING`, `REFUND_PENDING`, `REFUNDED` where appropriate. Menu/price/tax/modifier snapshot at submission/acceptance policy point. KDS owns station item execution, not payment. Cancellation/void/discount needs permission and reason.

## 5. Shared table session
First eligible order opens/joins one active table session per table. Every guest gets separate opaque capability. Guests can submit separate orders but only see their own order/payment details. Staff POS sees the full operational session within role scope. Consolidated, separate and split bills are supported under staff permissions; customer can request staff assistance. Close only when outstanding orders/payment/bills meet configured policy.

## 6. Room-service folio
Room QR reveals only room-service destination context. `CHARGE_TO_ROOM` must validate current active stay, authorised guest/service session, correct property/room, open eligible folio, property policy and credit/approval checks. If validation fails return safe `FOLIO_CHARGE_UNAVAILABLE` and do not create folio entry. Restaurant order remains sales source; folio entry is settlement/receivable route, never duplicate revenue. Source order ID must be idempotently linked.

## 7. Procurement-to-payment
`REQUISITION_DRAFT → SUBMITTED → APPROVED → RFQ/QUOTES → PO_APPROVED → GOODS_RECEIPT → QA_ACCEPTED/REJECTED → SUPPLIER_BILL_MATCHED → AP_APPROVED → PAYMENT → RECONCILED`. Inventory owns receipt/stock movement; Finance owns supplier payable and payment. Three-way match PO/GRN/invoice within approved tolerance.

## 8. HR-to-Finance
HR calculates and approves payroll; locks a payroll snapshot; emits posting batch. Finance validates mapping, approves and posts journal/payment. Post-lock changes require adjustment/reversal.

## 9. CRM and loyalty
Only verified identities link hotel/restaurant activity. CRM owns points ledger. Eligible completed/settled transactions generate points based on versioned rules. Refund/chargeback creates compensating loyalty entry. No automatic merge by name similarity.

## 10. Event reliability
Use at-least-once delivery, outbox and idempotent consumers. Pending integrations show pending/error status to authorised staff. User-facing success is only displayed after authoritative operation succeeds.
