# Panel 03 — Hotel PMS & Front Desk

## 1. Ownership and boundaries
PMS owns hotel room inventory, reservation lifecycle, guest-stay lifecycle, room assignment, guest folios, deposits, check-in/check-out, hotel policies, and night audit. Housekeeping owns cleaning/maintenance task execution and room physical readiness; PMS consumes their statuses. Restaurant owns restaurant sales and order lifecycle; room-service orders are linked to a stay/folio through an explicit integration, not duplicated as hotel revenue.

## 2. Pages and behaviour

### 2.1 Hotel dashboard
- Show arrivals, departures, in-house guests, available/occupied/OOO rooms, expected occupancy, pending deposits, unresolved guest requests and night-audit status.
- Every KPI is property-scoped and date/timezone-aware; click opens filtered list.

### 2.2 Reservation calendar and timeline
- Views: day/week/month or configurable timeline, by room type/room/building.
- Search/filter by reservation status, source, arrival/departure, guest, room type, channel and rate plan.
- Prevent overlapping confirmed room assignments using database constraints/transactional availability check.
- Calendar drag/move must revalidate availability and permission before save; never rely on client-only conflict checking.

### 2.3 Reservation directory, create wizard and detail
- Capture guest, contact, dates, occupancy, rooms, room type, rate plan, inclusions, special requests, source/channel, tax/fee snapshot, deposit/cancellation terms.
- Quote availability and price server-side; quote has expiry and must be revalidated at confirmation.
- Reservation states: `inquiry`, `quoted`, `pending_payment`, `confirmed`, `checked_in`, `checked_out`, `cancelled`, `no_show`, `expired` (exact allowed transitions controlled by policy).
- Reservation detail shows immutable price breakdown, status history, guest contacts, room assignment, deposits, invoices/folio links, requests, notes with private/public distinction.
- Modification and cancellation must calculate policy effects and require confirmation; record actor/reason.

### 2.4 Group reservations and walk-ins
- Group block supports room allocation, pickup, cutoff dates, master/group contact and per-guest folios as configured.
- Walk-in uses the same availability, identity and payment validation as normal booking; do not bypass stay registration or occupancy constraints.

### 2.5 Room inventory and categories
- Manage room numbers, type/category, capacity, bed config, amenities, floor/building, features and operational status.
- Room status separates occupancy from physical readiness: occupancy (`vacant`, `occupied`, `reserved`) and room condition (`clean`, `dirty`, `inspected`, `maintenance`, `out_of_order`) are separate dimensions.
- Never infer guest checkout from a cleaning task or infer clean state from vacancy.

### 2.6 Rates and availability
- Rate plans, restrictions, inclusions, min/max stay, blackout dates, taxes and cancellation rules.
- Effective rates are computed server-side and snapshotted onto confirmed reservation.
- Historical reservation prices do not change when a rate plan changes.

### 2.7 Arrivals and check-in
- Arrival list with identity verification status, booking balance/deposit, room readiness and special requests.
- Check-in validates confirmed reservation, dates, room assignment, guest occupancy and property policy.
- Room not ready: allow authorised alternate-room reassignment or controlled exception with reason; do not mark room clean.
- Capture registration requirements per jurisdiction as an unresolved legal/configuration decision; minimise sensitive documents and set retention rules.

### 2.8 In-house guest and room transfer
- In-house list supports search, room, guest, ETA/departure, balance status and stay state.
- Room transfer checks new room availability/readiness and moves future room-service/housekeeping context without losing historical folio line references.

### 2.9 Departure and checkout
- Departure preview aggregates room charges, taxes, payments, adjustments, restaurant folio postings and outstanding items.
- Checkout requires folio settlement or an authorised exception/credit arrangement; close stay idempotently.
- Never duplicate a restaurant sale when posting room-service charge: folio entry references originating restaurant order/receivable and uses correct inter-department accounting mapping.

### 2.10 Guest directory and profile
- Show verified guest identity/contact, stay history, preferences, consents and support notes according to permission.
- Customer identity unification is CRM-owned; PMS references the canonical guest/customer ID and does not auto-merge duplicate guests.
- Hide passport/identity document values except for roles with explicit need.

### 2.11 Guest folio, deposits, invoice and payments
- Folio ledger has immutable debit/credit/settlement entries with references, currency, tax snapshot, actor/time and source domain.
- Corrections use reversal/adjustment entries, never destructive editing of settled charges.
- Deposit application and release are explicit; don't count a deposit twice as revenue.
- Invoice numbering and tax rules require jurisdiction decision.

### 2.12 Room service and guest requests
- Display restaurant-origin room-service tickets linked to eligible stay/room; PMS may view folio impact, but restaurant retains order/menu/sale ownership.
- Guest requests create service request records with category, priority, room/stay reference and visibility; tasks are handed to Housekeeping/concierge workflow.

### 2.13 Night audit, policies, reports and settings
- Night audit checks open folios, departures, room rates, revenue postings, unresolved transactions, date rollover and reconciliation before posting.
- Night audit is idempotent per property business date; corrections use reversal and rerun under privilege.
- Reports: occupancy, ADR, RevPAR, booking pace, cancellation/no-show, source mix, room revenue, stay length.
- Settings include check-in/out times, booking policies, overbooking policy (disabled by default), guest policies, tax settings and room assignment rules.

## 3. Acceptance tests
- Two reservations cannot confirm overlapping use of the same sellable room.
- Reservation rate snapshot remains unchanged after rate plan update.
- Front desk cannot check in a cancelled or unpaid-pending reservation unless policy explicitly permits and a reason is recorded.
- Room occupancy and cleanliness states cannot be conflated.
- Check-in cannot use room in maintenance/out-of-order state without authorised override.
- Checkout cannot silently discard unpaid folio balance.
- Room service posted to folio references one restaurant order and cannot create duplicate revenue.
- Night audit run repeated with same property date is idempotent.
- Front desk role cannot access restricted guest identity documents without permission.
