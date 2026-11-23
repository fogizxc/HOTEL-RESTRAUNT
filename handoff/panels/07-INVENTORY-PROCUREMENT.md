# Panel 07 — Inventory & Procurement

## 1. Ownership
Inventory owns stock item master, units, locations, batches, on-hand/reserved/available balances, immutable stock ledger, recipes/BOMs, consumption, wastage and procurement receiving. Finance owns supplier invoice liabilities, payment and official accounting entries. Procurement must not silently pay suppliers or write GL journals.

## 2. Pages and behaviour

### 2.1 Inventory dashboard
- Property/outlet/location-scoped stock value, low stock, expiry alerts, pending purchase orders, goods awaiting inspection, stock variance and high-wastage items.
- Show valuation method, as-of timestamp and currency.

### 2.2 Item catalogue
- Create/edit item: SKU, name, category, description, base unit, conversion units, tracking mode (quantity/batch/serial where needed), reorder policy, storage rules, tax/product reference and active status.
- Unit conversion is explicit and immutable once referenced by transactions; changes require new conversion version.
- Prevent deleting referenced items; archive.

### 2.3 Locations and stock ledger
- Manage warehouse, store room, kitchen location, outlet store and other scoped stock locations.
- Every stock change is a ledger transaction: receipt, issue, consumption, transfer, count adjustment, return, waste, reservation/release.
- Ledger rows are append-only; corrections are reversing entries.
- Balances are derived/materialised from ledger and reconciled by periodic consistency checks.

### 2.4 Stock overview and movement
- Show on hand, reserved, available, quarantined, in transit and reorder threshold.
- Transfer: source/destination, item, quantity, unit, batch, dispatch, receive/shortage/damage confirmation.
- A transfer must not create stock at destination before receipt unless the in-transit bucket is represented.
- Adjustments require reason, evidence for configured variance, permission and approval threshold.

### 2.5 Batch, expiry and valuation
- Track batch/lot, supplier, received date, expiry, quantity, QA status and location.
- Use FEFO for expiry-sensitive issues unless documented policy overrides; record chosen batch allocation.
- Valuation method (e.g. FIFO/weighted average) is an owner decision and must be consistent; don't mix within one valuation book.

### 2.6 Stock count and reconciliation
- Create count session by location/category, freeze or record movement window, count blind/visible quantity per policy, submit variance, investigate, approve adjustment.
- Counts cannot edit ledger directly. Approved variance posts a count-adjustment transaction.
- Concurrent movements during count are reconciled using snapshot/cutoff strategy.

### 2.7 Recipes, BOM and recipe costing
- Recipe versions contain ingredients, quantities, units, yield, preparation loss, approved substitutes and effective dates.
- Costing calculates ingredient cost using selected valuation snapshot and yield; preserve version used for sale costing.
- Menu item links to approved recipe version; recipe edits do not recost historical sales unless explicitly running a restated analytical view.
- Order completion/defined consumption trigger emits a consumption event; event must be idempotent by order-item reference.

### 2.8 Menu availability and production
- Projected stock and recipe dependencies may recommend menu item availability; manager can pause item with reason.
- Do not guarantee stock based solely on a stale cache. At order acceptance, configured stock policy validates/reserves ingredients.
- Production batch records input quantities, output yield, loss and batch traceability where needed.

### 2.9 Purchase requisitions and suppliers
- Requisition captures requesting outlet/location, item, quantity, need-by date, reason and approval route.
- Supplier directory contains verified contact, terms, tax identity, status, quality history and bank details under restricted roles.
- Supplier bank account changes require independent verification and dual control.

### 2.10 RFQ, quote comparison and purchase orders
- RFQ records line items, supplier invites, quote deadlines and responses.
- Compare price, tax, delivery, terms, quality, lead time and substitutions.
- PO requires approval by threshold; once issued, version and supplier acknowledgement are tracked.
- PO cancellation/change after supplier acceptance must be audited and may require approval.

### 2.11 Goods receipt, QA and return
- GRN records PO reference, received quantity, batch, expiry, damages, invoice/delivery note and receiver.
- Separate received, quarantined, rejected and accepted stock.
- QA release moves only accepted quantity to available stock.
- Purchase return references original GRN/batch and reduces stock through ledger transaction.

### 2.12 Supplier invoice handoff and three-way match
- Present PO, GRN and supplier invoice references/variance for Finance review.
- Inventory does not authorise supplier payment or book AP liability.
- Finance owns payable recognition and payment approval.

### 2.13 Wastage, expiry, forecasting and reports
- Waste record includes item/batch/quantity/reason/employee/evidence/approval.
- Expiry alerts and recommended FEFO consumption; expired goods blocked from normal issue.
- Forecast uses demand history, lead time, seasonality and minimum stock; label forecast assumptions and confidence.
- Reports: stock on hand/value, usage, recipe variance, wastage, supplier lead time, purchase price variance, expiry loss.

### 2.14 Mobile barcode/stock count
- Scan known barcode or enter SKU; unresolved barcode goes to safe item match flow.
- Offline count entries remain pending sync; conflict resolution never silently overwrites ledger.

## 3. Acceptance tests
- Stock cannot become negative unless explicit backorder/negative stock policy is approved.
- Duplicate order-consumption event posts one issue transaction.
- Transfer leaves quantity in transit until destination receipt.
- Expired/quarantined stock is unavailable for normal consumption.
- Ledger history cannot be edited/deleted by UI or API.
- Stock count creates an approved adjustment transaction, not a balance overwrite.
- Inventory cannot mark supplier invoice paid or post GL journal.
- Supplier bank change requires dual approval and audit.
