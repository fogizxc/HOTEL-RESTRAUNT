# Panel 12 — Customer & Guest Portal

## 1. Audience and scope
Customer-facing, mobile-first portal for public hotel discovery/booking, restaurant menu/order, table QR, room QR room service, customer account, loyalty and guest service requests. No staff/admin workspace or internal operational notes. The portal adapts to property capabilities and effective module entitlements.

## 2. Public discovery and property context
- Branded landing page, property/outlet selector, property detail, services, photos, contact and policies.
- Hotel-only properties show booking actions; restaurant-only show menu/order actions; combined show both when enabled.
- Tenant/property branding and domain routing must be resolved server-side; never accept arbitrary `tenant_id` from URL as authority.
- Customer must never enumerate other tenants or properties from selectors/search.

## 3. Hotel booking journey
Pages: destination/property search; dates/guests/rooms; availability; room detail; rate selection; guest details; booking review; hosted checkout handoff; confirmation; manage booking; cancellation/change request; booking history.
- Availability and final price are server-calculated and revalidated before confirmation.
- Reservation quote expires; if price/availability changes, show updated amount and require renewed consent.
- Display taxes/fees, deposit, inclusions, cancellation window, occupancy and policies before payment.
- Payment redirect success is not proof of payment; use provider/webhook-confirmed state.
- Guest can manage only reservations linked to authenticated identity or verified booking access token.

## 4. Restaurant menu and ordering journey
Pages: outlet landing; category/menu; search; item detail; modifier selection; cart; checkout; payment choice; order confirmation; order tracker; bill/receipt; feedback.
- Menu includes price, availability, variants, add-ons, dietary/allergen information where configured.
- Revalidate price, modifiers, availability, service hours, delivery zone and taxes at submission.
- Support dine-in QR, takeaway/pickup, delivery and room service only when configured.
- Guest checkout may be allowed; account creation is not forced unless the selected workflow requires verified identity.

## 5. Table QR guest flow
- Opaque unique QR token resolves to tenant/property/outlet/table and valid active table session.
- Context header can show property/outlet/table label but no internal database ID.
- Multiple diners can place separate orders in one shared table session.
- Each diner sees only their own cart/order/receipt and own payment details; do not disclose another diner's name, items, contact or payment status.
- Guest can request bill/split help; any customer-facing split flow must operate on authorised share scope and cannot read/alter another diner's private payment method.
- Invalid, revoked, expired, inactive-session and closed-outlet screens provide safe help/refresh path.

## 6. Room QR and room service
- Room QR resolves room/service target only; token itself does not authenticate the occupant or disclose guest/folio/stay data.
- Customer sees safe room-service context, available outlets/menu, cart, notes, order status and support.
- Payment methods: hosted online payment, cash if configured, charge-to-room only after active stay eligibility and authorised guest/service session check.
- If folio charge unavailable, explain and present allowed alternatives.
- Restaurant order remains revenue source; folio posting is a linked receivable/settlement route, not duplicate revenue. Customer wording must avoid implying double charge.

## 7. Payment and cash status
- Use provider-hosted checkout or approved payment SDK; never collect/store raw card data in app database.
- Statuses: initiated, pending_provider, authorised/captured according to provider, failed, cancelled, cash_pending_collection, cash_collected, refunded/partially_refunded.
- Cash selection remains pending until staff confirms cash collection.
- Safe retry must use idempotency and not create duplicate order/reservation/charge.
- Room-folio payment is not offered on QR scan alone.

## 8. Accounts, loyalty and memberships
- Sign-in/register, OTP/email verification, recovery, optional guest checkout, profile, bookings, orders, saved contact/delivery details, loyalty points, rewards, memberships, preferences, communications and privacy requests.
- Link CRM identity only after verified identity flow; do not auto-merge profiles based on similar details.
- Marketing consent separate from transactional communication and not preselected.
- Points are pending until eligible completed/settled source transaction; final balance comes from CRM ledger.

## 9. Guest service and support
- Request towels/amenities/cleaning, report room issue, concierge help, late checkout request, contact property, feedback/complaint.
- Each request has reference, submitted time, customer-safe status and message channel.
- Internal employee notes/task routing are never shown.

## 10. Privacy and security
- QR token is unguessable, revocable, rate-limited and never logged in raw form.
- Guest access tokens are scoped, hashed, expiring and single-purpose where possible.
- Prevent IDOR across order, booking, receipt, room, table and customer profile resources.
- Use anti-CSRF and secure session policy as applicable; no PII in analytics by default.

## 11. Acceptance tests
- Customer A cannot read Customer B's order, booking, bill or profile by changing route IDs.
- Table QR shared session supports independent orders without cross-customer data leakage.
- Room QR cannot reveal guest name, stay date, folio amount or balance.
- Charge-to-room denied without eligible active stay and authorised guest/service session.
- Cash remains pending until staff collection confirmation.
- Payment redirect alone does not mark order paid.
- Repeated order submit with same idempotency key produces one order.
- Marketing sends require current consent; guest can withdraw consent.
- Disabled subscription module is absent/unavailable and APIs deny access.
