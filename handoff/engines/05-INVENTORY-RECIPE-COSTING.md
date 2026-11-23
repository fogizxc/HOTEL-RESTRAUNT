# Shared Engine 05 — Inventory Ledger, Recipes, Consumption and Costing

## 1. Purpose
Provide one authoritative stock ledger across hotels, kitchens, outlets and warehouses. Operational applications request stock actions; only Inventory service posts stock ledger transactions. Finance consumes approved valuation and purchase/payable events.

## 2. Units and item identity
- Each stock item has a base unit and explicit conversion definitions (e.g. kg↔g, litre↔ml, pack↔each).
- Conversions are versioned; historical transactions keep the unit/conversion snapshot used.
- Never compare quantities in different units without valid conversion.
- Item identity is tenant-scoped unless a platform catalogue is explicitly implemented; tenant cannot see another tenant's private item master.

## 3. Ledger transaction types
`opening_balance`, `purchase_receipt`, `purchase_return`, `transfer_out`, `transfer_in`, `production_input`, `production_output`, `recipe_consumption`, `waste`, `count_adjustment`, `reservation`, `reservation_release`, `supplier_return`, `correction_reversal`.
Each entry stores source reference, idempotency key, item, location, batch, quantity in base unit, unit cost/value where relevant, actor, occurred/recorded times and reason. Ledger is append-only.

## 4. Stock balance semantics
Track on-hand, reserved, available, in-transit, quarantined and blocked/expired separately. Available = eligible on-hand - reservations, subject to quality/expiry. Materialised balances are caches; ledger is authoritative and reconciled. Negative stock is denied by default unless an explicit approved policy supports it.

## 5. Reservations and issue
- Sales can reserve stock/ingredients when configured; reservation has expiry and order reference.
- Reservation is not consumption. Consumption posts when configured fulfilment event occurs.
- Cancellation releases reservation; already consumed stock requires a compensating return/waste policy, not a silent release.
- Duplicate source event must not issue stock twice.

## 6. Batch and FEFO
- Batch has received date, expiry, supplier, quantity, quality status and location.
- FEFO allocation applies to expiry-managed items unless a permitted override is recorded.
- Expired/quarantined batch cannot be reserved/issued for normal use.
- Recall/contamination blocks batch across all locations and identifies affected recipes/orders where traceability exists.

## 7. Recipe/BOM version and costing
- Recipe version includes components, quantities, units, yield, loss, substitutions, effective date, approval and outlet applicability.
- Ingredient cost derives from selected valuation policy and valuation snapshot.
- Menu item references approved recipe version; historical sales/COGS keep their recipe/ingredient cost snapshot.
- Recipe edit produces a new version and previewed cost variance; does not rewrite historical transactions.

## 8. Costing method decision
FIFO vs weighted average (and other methods) remains an owner/accounting decision by legal entity and inventory book. Configure one approved method per valuation scope. Tax/accounting treatment must align with jurisdiction and auditor advice. Do not silently combine methods.

## 9. Reconciliation and controls
- Daily/periodic balance-to-ledger checks.
- Stocktake cutoff strategy records movements during count.
- Purchase receipt is matched to PO/GRN; invoice recognition and supplier payment remain Finance-owned.
- Wastage and adjustments need reason, actor and approval thresholds.

## 10. Acceptance tests
- Ledger is append-only through API and DB permissions.
- Duplicate order event posts one consumption entry.
- Expired/quarantined batch cannot be issued.
- Transfer remains in-transit until destination receives.
- Unit conversion is consistent and historical value is stable.
- Recipe edit affects future cost only.
- Stock balance reconciles from ledger projection.
