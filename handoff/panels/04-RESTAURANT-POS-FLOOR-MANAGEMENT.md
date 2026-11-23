# Panel 04 — Restaurant POS & Floor Management

## 1. Ownership
Restaurant domain owns menus, outlets, dining areas, table layout, table sessions, orders, order items, KOT creation, customer bills, service channels, payments collected at POS, shift/cash drawer records, restaurant reservations and restaurant operational configuration. KDS owns kitchen execution state; Inventory owns stock ledger; Finance owns official GL; CRM owns canonical customer and loyalty data.

## 2. Pages and exact behaviour

### 2.1 Restaurant dashboard
- Outlet-scoped sales, open tables, open orders, average preparation time, covers, channel mix, refunds/voids, cash shift state and item availability.
- Clearly label business date/timezone and whether figures are live, delayed, or estimated.

### 2.2 Floor plan and editor
- Support multiple floors/areas (indoor, outdoor, bar, private dining, etc.) and arbitrary configurable tables.
- Table fields: code/label, capacity, shape, location, status and outlet. Status is independent from QR token state.
- Editor supports add/move/resize/rotate/merge visual zones, but save validates unique table code within outlet and avoids unintended overlapping positions.
- Floor plan is a visual projection; table session state remains authoritative in database.

### 2.3 Table sessions
- Open session, attach one/many orders, track covers, server, start time, table and status.
- A table can have one active bill/session under normal policy; authorised transfer/merge must preserve all order and payment references.
- Several QR diners can attach separate customer principals/anonymous guest references and separate orders to the same active session.
- Private customer view must not expose other diners' contact, cart, payment or individual orders. Staff sees combined table operations based on permission.
- Session closure requires all bills resolved, or an authorised comp/void/settlement exception.

### 2.4 Touch POS
- Product grid/search, categories, modifier sets, variants, quantity, notes, discounts subject to permission, customer/table selection, order channel, send-to-kitchen action.
- Price, tax, modifier availability and discounts revalidated server-side at submit.
- Prevent duplicate submissions with idempotency keys and unique client request reference.
- POS should tolerate brief network interruption only through a defined safe offline queue design; never claim an offline order was accepted by server until reconciled.

### 2.5 Menus, items, modifiers, combos and schedules
- Menu/category/item CRUD with versioning, images, SKU, description, tax category, price, availability, channel applicability, station routing, allergen/dietary labels, prep time, modifier groups and recipe reference.
- Modifier constraints: minimum/maximum selections, required group, priced options, allowed combinations.
- Item availability can be manually paused or derived from Inventory ingredient availability; source/reason visible.
- Historical order item name, description/price/modifiers/tax are snapshots and must not change with menu edits.

### 2.6 Order list and order detail
- Filter by status, channel, table, time, order reference, guest and payment status.
- Order state machine: `draft`, `placed`, `accepted`, `in_preparation`, `ready`, `served_or_handed_over`, `completed`, `cancel_requested`, `cancelled`, `rejected` (transitions may vary per channel).
- Each transition checks actor permission, current state, optimistic version and required reason.
- Item-level states support partial availability, station preparation and course timing.

### 2.7 KOT dispatch and KDS handoff
- On confirmed placement, create KOT/outbox event in same transaction or reliable outbox flow.
- KOT routing by item station, outlet, course, dining mode and configured overrides.
- Retries must not create duplicate kitchen tickets. KDS acknowledgement and preparation events are idempotent.
- Reprints are labelled `COPY/REPRINT`, audited, and do not create a second order.

### 2.8 Universal QR manager
- Create QR target per table with opaque random token and hashed server-side lookup.
- Bulk generate based on selected tables; no fixed baked-in total count, only entitlement quota.
- Download each target as PNG/SVG; bulk export PDF/ZIP; print studio with property/outlet/table branding and test-scan preview.
- Assign, replace, revoke, regenerate; revoke prior token immediately and preserve assignment history.
- Analytics: scan count, successful resolutions, conversion, errors, time bucket; avoid collecting unnecessary device fingerprinting/PII.
- See `engines/02-UNIVERSAL-QR-ENGINE.md` for canonical token rules.

### 2.9 Billing and split/merge
- Create bill from session/order; include item snapshots, discounts, tax/fees, service charge, round-off policy and payments.
- Split by item, equal shares, custom amount; prevent over-allocation and rounding mismatch.
- Merge bill transfers references and balances atomically; original bill remains traceable.
- Discounts/voids/refunds require role-based limits and reason; after settlement use reversal/refund, not mutation.

### 2.10 Payment and cash shifts
- Payment methods include configured online gateway, UPI/card terminal integration, cash and approved account/room-folio methods.
- Cash is `pending_collection` until staff confirms collection; selection alone never means paid.
- Shift open/close, opening float, cash movement, expected vs counted cash, variance reason and manager approval.
- Gateway settlements are reconciled through Finance; POS must not assume capture equals bank settlement.

### 2.11 Reservations, waitlist and promotions
- Restaurant table reservation includes outlet, area/table preference, party size, date/time, contact, source and status.
- Waitlist offers queue position and quoted wait only as estimate; seating rechecks table/session availability.
- Coupons/promotions validated server-side for window, outlet/channel, minimum, maximum redemption, customer eligibility and stacking.

## 3. Acceptance tests
- Table QR from Outlet A cannot resolve to a table in Outlet B/Tenant B.
- Two QR customers can submit separate orders to one active table session without seeing each other's private data.
- Duplicate submit retry produces one order and one KOT set.
- Menu price/modifier change after cart creation is revalidated and clearly communicated.
- Split bill sums exactly to original outstanding total with defined rounding.
- Cash selected but uncollected remains pending.
- Reprint does not create a new ticket/order.
- Closing a session with unresolved balance is blocked unless an authorised exception is logged.
- Restaurant user cannot change GL journal directly; Finance consumes approved sales events.
