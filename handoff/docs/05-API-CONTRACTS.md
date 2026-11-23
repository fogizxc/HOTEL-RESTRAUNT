# 05 — API Contracts and Conventions

## General conventions
- Base path `/api/v1` until versioning is decided.
- JSON payloads; UTC ISO-8601 timestamps; UUID internal resource IDs; opaque public references.
- Browser uses secure session cookie; integrations use narrowly scoped credentials.
- Tenant scope comes from authenticated membership or validated opaque guest capability, never a trusted body field.
- Consistent Problem Details-style errors with safe user message, stable code and request ID; no SQL/stack/provider secret leakage.
- Paginate lists (default 25, max 100 unless export job); allowlist filters/sort fields.
- Reject unknown protected fields; validate all inputs.
- Customer DTOs and employee/staff DTOs are separate where private fields exist.

## Idempotency and concurrency
Require `Idempotency-Key` for order creation, payment intent/capture, refunds, journal posting, inventory adjustment/transfer/receipt, folio posting and similar retryable writes. Same key + same payload returns same logical result; same key + different payload returns conflict. Use aggregate `version` or `If-Match` for state updates; stale write returns 409.

## Endpoint catalogue
### Auth
`POST /auth/login`, `/auth/logout`, `/auth/password/forgot`, `/auth/password/reset`, `/auth/mfa/challenge`, `/auth/mfa/enrol`, `/auth/invitations/{token}/accept`; `GET /me`, `GET /me/sessions`, `DELETE /me/sessions/{sessionId}`.

### Platform and subscriptions
`GET/POST /platform/tenants`, `GET /platform/tenants/{tenantId}`, `PATCH /platform/tenants/{tenantId}/status`, `GET /platform/tenants/{tenantId}/entitlements`, `PUT /platform/tenants/{tenantId}/entitlement-overrides/{featureCode}`, `GET /tenant/subscription`, `GET /tenant/usage`, `POST /tenant/subscription/change-request`.

### Organisation
`GET/POST /tenant/properties`, `GET/PATCH /tenant/properties/{propertyId}`, `GET/POST /properties/{propertyId}/outlets`, `GET/POST /tenant/employees`, `GET/POST /tenant/roles`, `PUT /tenant/roles/{roleId}/permissions`.

### Hotel
`GET /properties/{propertyId}/availability`, `POST /properties/{propertyId}/reservations`, `GET/PATCH /reservations/{reservationId}`, explicit command routes `/confirm`, `/cancel`, `/check-in`, `/check-out`, `GET /stays/{stayId}`, `GET /stays/{stayId}/folio`, restricted `POST /stays/{stayId}/folio/entries`.

### Restaurant / KDS
`GET /outlets/{outletId}/menu`, `POST /outlets/{outletId}/orders`, `GET /orders/{orderId}`, explicit accept/cancel/complete routes, `GET /outlets/{outletId}/floor-plan`, `POST /table-sessions/{sessionId}/orders`, `GET /kitchen/stations/{stationId}/tickets`, explicit ticket-item start/ready routes, customer-safe `POST /bills/{billId}/split-request`.

### QR
`POST /qr/destinations`, `/qr/destinations/bulk-create`, `POST /qr/tokens/{tokenId}/revoke`, `/rotate`, public `GET /public/qr/{opaqueToken}`, `POST /public/qr/{opaqueToken}/guest-sessions`, `POST /public/guest-sessions/{capability}/orders`.

### Inventory / procurement
`GET /inventory/items`, `/inventory/stock`, `POST /inventory/movements/adjustments`, `/inventory/transfers`, `/procurement/requisitions`, `/procurement/purchase-orders`, `/procurement/goods-receipts`, `POST /inventory/counts/{countId}/submit`.

### Finance
`GET /finance/chart-of-accounts`, `POST /finance/journal-entries`, explicit `/post` and `/reverse`, `GET /finance/trial-balance`, `/profit-loss`, `/balance-sheet`, `/cash-flow`, `POST /finance/reconciliations/{id}/match`.

### HR
`GET/POST /hr/employees`, `POST /hr/attendance/events`, `/hr/shifts/assignments`, `/hr/leave-requests`, `/hr/payroll-runs/{id}/process` with approvals.

### CRM
`GET /crm/customers`, `GET/PATCH /crm/customers/{id}`, identity-verification and consent routes, loyalty ledger and redemption routes.

### BI and shared services
`GET /bi/dashboards/{dashboardCode}`, `POST /bi/reports/preview`, `POST /exports`, `GET /exports/{id}`, `GET /notifications`, `PATCH /notifications/{id}`, `POST /support/tickets`, `GET /support/tickets/{id}`.

## Security requirements
Authorize every resource and action. Public endpoints rate-limited. Validate tenant scope on nested resources. Restrict CORS. Apply CSRF where cookie auth needs it. Verify webhook signature/timestamp/account/environment. File download re-checks authorization and entitlement and uses short-lived signed access. Never rely on collection filtering alone.

## OpenAPI
`contracts/openapi.yaml` is a starter only. Expand all paths, parameters, DTOs, errors, scopes, state transitions and webhook contracts; validate using the project’s selected OpenAPI tool and run contract tests. Do not describe starter as complete.
