# Shared Engine 03 — Restaurant Order, KOT, Table Session and Billing

## 1. Canonical ownership
Restaurant domain owns order/bill facts. KDS owns preparation events. Payments owns payment intent/capture/refund lifecycle. Finance owns GL and settlement reconciliation. Inventory receives consumption events. CRM receives eligible customer/order facts for loyalty and segmentation.

## 2. Order lifecycle
Allowed transitions must be explicit and guarded by current state/version and role: `draft → placed → accepted → in_preparation → ready → served_or_handed_over → completed`; alternative transitions include rejected, cancellation requested, cancelled. Channel-specific paths must be documented. Completed/settled historical order facts are not edited in place.

## 3. Order line snapshot
Each line snapshots item identifier/version, visible name, selected modifiers, quantity, unit price, discounts, tax category/rate snapshot, notes, station routing snapshot and source menu version. Menu edits must not rewrite historic order details.

## 4. Idempotency and concurrency
- Every order create/submit requires idempotency key scoped to actor/tenant and operation.
- Same key + same payload returns same result; same key + changed payload returns conflict.
- Stock reservation, table session association, order creation, financial totals and outbox event must be transactionally coordinated.
- Use optimistic version for edits and state changes; stale request returns conflict and latest version.
- Replays must not create duplicate order, KOT, stock issue or loyalty earn.

## 5. KOT creation and routing
- Accepted order emits one logical KOT set via transactional outbox.
- Route each line by station/outlet/course; store routing snapshot.
- Multi-station lines can produce child station tickets linked to the same order line.
- Re-fire is an explicit revision referencing original item/ticket; never recreate revenue/order line accidentally.
- Cancel/modify event propagates to KDS and preserves history.

## 6. Table sessions and guest privacy
- Active table session groups multiple orders and diners.
- Session state is separate from individual order/bill state.
- Customer participant may read only own order/payment/bill data; authorised staff may inspect session aggregate.
- Closing session requires resolved balances or documented manager exception.

## 7. Billing and split rules
- Bill lines reference order lines and preserve tax/discount/service charge calculations.
- Split by item, equal shares or custom amount must prevent double allocation and over-allocation.
- Rounding algorithm must be deterministic and documented; sum of split bills equals original outstanding bill.
- Merge/split operations preserve lineage and cannot duplicate payable amounts.
- After settlement, adjustments are reversal/credit/refund records, not edits to original posted bill.

## 8. Cash and payment status
Cash tender selection creates `cash_pending_collection`. Only authorised collection confirmation changes it to `cash_collected`. Online payment status is provider-confirmed. POS payment capture and bank settlement are separate events.

## 9. Acceptance tests
- Duplicate order submit and webhook events are idempotent.
- Same table session supports multiple orders and separate customer views.
- Bill split values sum exactly to parent bill after defined rounding.
- Cancelled item cannot remain in active KDS queue without visible cancel/ack event.
- Re-fire does not create another sale.
- Order item prices remain stable after menu changes.
