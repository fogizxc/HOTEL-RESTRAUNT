# Shared Engine 04 — Payment, Refund, Cash Collection and Hotel Folio

## 1. Core rules
- Provider payment state is authoritative only after verified server/provider response or webhook; frontend redirect is not proof.
- Store provider references/tokenised method references, never raw PAN/CVV or secrets.
- Idempotency is mandatory for payment creation, capture, refund and webhook processing.
- Distinguish authorisation, capture, settlement, refund and bank payout.
- Guest transaction payments are separate from SaaS subscription billing.

## 2. Payment records and state
Logical entities: `payment_intent`, `payment_attempt`, `provider_event`, `payment_allocation`, `refund`, `cash_collection`, `settlement_batch`, `settlement_line`, `payment_reconciliation_exception`.

Suggested statuses: created, requires_action, pending, authorised, captured, failed, cancelled, partially_refunded, refunded, disputed. Exact mapping is provider adapter-specific; preserve raw provider status safely alongside canonical mapping.

## 3. Checkout protocol
1. Client requests quote/order/reservation; backend calculates amount/currency and persists snapshot.
2. Backend creates payment intent with merchant/provider reference, idempotency and metadata minimised.
3. Customer completes hosted checkout/provider UI.
4. Provider sends signed webhook or verified server callback.
5. Verify signature, amount, currency, merchant account, reference, event age/replay rules.
6. Apply state transition once and allocate captured amount to invoice/bill/deposit.
7. Publish result through outbox; notify customer only after confirmed state.
8. Reconcile later against provider settlement and bank statement.

## 4. Cash collection
- Selecting cash does not equal payment.
- Create pending cash tender linked to bill/order and cash shift.
- Staff confirms amount actually collected, tender currency, change given, collection actor/time and till.
- Under/over payment requires explicit policy and variance record.
- Shift close compares expected/count; variance cannot be silently zeroed.

## 5. Refunds
- Refund references original captured payment and source bill/order/folio allocation.
- Full/partial refund amount cannot exceed refundable balance after previous refunds.
- Refund request may need approval/dual control; record reason, actor, amount, currency and provider response.
- Refund may be pending/failed; do not mark complete before provider confirmation.
- Operational cancellation/credit note and cash movement are separate linked records.

## 6. Hotel folio charging
- Only after QR/customer service session verifies active stay and authorised guest/room relation.
- Verify target room matches active stay; stay not checked out/cancelled; folio allows charge; amount within configured credit/deposit rules; property accepts room-charge method.
- Create a folio debit referencing originating restaurant order/payment allocation, not duplicate revenue.
- Restaurant remains source of sale/order and item taxes; Finance mapping must avoid duplicate revenue across restaurant and hotel books.
- Folio posting is idempotent by source order/payment reference.
- On checkout, reconcile all linked folio items and settlement status.

## 7. Security/reliability requirements
- Webhook signature validation and secret rotation.
- Event replay protection and idempotency key.
- Amount/currency mismatch rejection.
- Out-of-order event handling with state machine and provider query reconciliation.
- PII/payment payload redaction in logs.
- Refund/chargeback exception queue.

## 8. Acceptance tests
- Browser success redirect without signed server confirmation does not mark payment captured.
- Duplicate webhook creates one allocation.
- Incorrect amount/currency/merchant reference is rejected.
- Refund over remaining captured balance is rejected.
- Cash remains pending until staff confirmation.
- Room folio charge denied for unverified guest or inactive stay.
- Repeating room folio posting creates one debit and no duplicate revenue.
- Payment captured but settlement missing appears in reconciliation queue, not as bank cash.
