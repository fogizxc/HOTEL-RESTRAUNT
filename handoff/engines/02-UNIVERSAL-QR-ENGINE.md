# Shared Engine 02 — Universal QR Target and Session Engine

## 1. Purpose
One subsystem creates and resolves QR destinations for table, hotel room, room-service, poolside, banquet and future service points. A QR is a pointer to a scoped destination, not an authentication credential for sensitive resources.

## 2. Core entities
- `qr_target`: id, tenant_id, property_id, target_type, target_resource_id, optional outlet_id, label, status, created_by, created_at, revoked_at.
- `qr_token`: qr_target_id, token_hash, token_version, issued_at, expires_at, revoked_at, key_version. Raw token is returned only once during creation/export and never stored/logged.
- `qr_assignment_history`: old/new resource, actor, reason, time.
- `qr_scan_event`: target id if resolved, outcome code, time bucket, minimal device/source metadata, request/correlation id. Never persist raw token.
- `table_session`: tenant/property/outlet/table, state, opened_at, closed_at, version.
- `table_session_participant`: participant ref/customer ref, joined time, privacy scope, optional anon session key hash.
- `room_service_authorisation`: stay/room link, verified guest/session reference, expiry, permitted actions, revocation.

## 3. Token generation and storage
- Generate at least 128 bits of cryptographically secure randomness; prefer 256 bits.
- Encode URL-safe random token. No sequential IDs, room numbers, table numbers, tenant IDs or JWT claims embedded as readable values.
- Store only a keyed hash/HMAC or cryptographic hash with key version and constant-time compare.
- Token resolution is rate-limited and generic for unknown/revoked/expired cases to prevent enumeration.
- Rotation revokes previous token immediately; preserve assignment history.
- Raw token must be redacted from application, proxy, analytics, error and tracing logs. Configure query-string scrubbing.

## 4. Creation and quota workflow
1. Authorise creator on tenant/property/outlet and `qr.manage` permission.
2. Verify module entitlement and QR quota.
3. Validate target belongs to same tenant and target type supports requested service.
4. Create target and token atomically with an idempotency key.
5. Return QR URL/asset once; save generated image only as a derived artifact, never as authority.
6. Record audit and outbox event.

Bulk generation requires a preview, target count, quota estimate, exact target list, confirmation and resumable job. Export links are short-lived and permission checked.

## 5. Resolution workflow
1. Apply edge/API rate limit and token length/format bounds.
2. Hash token and resolve active target.
3. Check token/target status and effective tenant entitlement.
4. Confirm target resource still exists, active, correct type, same tenant/property/outlet.
5. For table: load/create or join permitted active session under transactional uniqueness rule; return only safe outlet/table/session context.
6. For room: return safe room-service landing context; do not return guest, stay, folio, or room occupant info.
7. Record minimal scan outcome in asynchronous/aggregated telemetry.
8. Return generic invalid/expired response that does not reveal resource existence.

## 6. Table shared-session privacy model
- A session is shared for restaurant operational grouping, not a shared customer identity.
- Each diner receives a separate participant/session reference and their own cart/order view.
- Staff roles may inspect all table orders; customers may only inspect resources linked to their own participant reference.
- A QR token alone does not authenticate a customer or authorise payment/guest profile access.
- Session close/timeout policy must handle diners arriving late, table turnover and stale QR scans. A new session should be opened only after old session is closed or authorised override is recorded.

## 7. Room QR authorisation
- QR resolves room target but cannot grant folio access.
- Guest must establish an authorised session using approved check-in PIN, verified registered contact, signed guest portal session, or another approved identity mechanism. Final method is an owner decision.
- Backend verifies active stay, room assignment, service window, stay status, guest authorisation and folio eligibility on each charge-to-room attempt.
- Do not reveal current occupant's identity or folio balance to another scanner.
- Room change, checkout, stay extension/cancellation or QR revocation invalidates relevant authorisation as policy requires.

## 8. QR lifecycle and analytics
States: active, disabled, revoked, expired, replaced. Disable is reversible; revoke is terminal for that token. Regenerate creates a new token version. Analytics aggregate scans/success/failure/conversion without collecting unnecessary identifiers.

## 9. Acceptance tests
- Guessing table number/room ID cannot derive a valid token.
- Token plaintext never appears in DB or logs.
- Revoked token fails immediately after cache invalidation and API recheck.
- Table QR cannot cross tenant/outlet boundaries.
- Customer participant A cannot read participant B order.
- Room QR cannot retrieve guest/folio details without separate authorisation.
- Room charge denied for checked-out/inactive stay or room mismatch.
- Bulk create under concurrent requests cannot exceed quota.
- Token rotation invalidates old asset while preserving historical target analytics.
