# 02 — Tenancy, Identity, Authentication and RBAC

## Scope hierarchy
`platform → tenant/organisation → property → outlet/department → resource`.

Every request must validate the complete relevant scope. A user assigned Property A cannot access Property B by changing a UUID. An outlet-scoped employee cannot access another outlet. Cross-tenant FKs must be impossible at the database level.

## Principal types
- Platform administrator: platform-wide rights explicitly assigned.
- Owner/tenant administrator: tenant-only rights.
- Employee: tenant membership with scoped roles.
- Guest/customer: external identity; anonymous browsing/ordering only where configured.
- Integration principal: non-human, limited credential with explicit resource scopes.

## Four independent permission layers
1. Platform tenant status/override.
2. Subscription plan entitlement.
3. Tenant feature configuration.
4. Employee/guest role, permission, scope and resource policy.

All layers must pass. A hidden button is never authorization. Recheck in API, export, file download, websocket and background jobs.

## RBAC data model
- `users`, `tenant_memberships`, `roles`, `permissions`, `role_permissions`, `membership_role_assignments`, `scope_assignments`, `invitations`, `sessions`, `mfa_methods`, `mfa_recovery_codes`.
- Platform roles are a distinct authorization context.
- Permission codes are action-oriented and stable, e.g. `hotel.reservations.check_in`, `restaurant.qr.manage`, `inventory.stock.adjust`, `finance.journals.post`, `hr.payroll.process`, `crm.customer.export`, `platform.entitlements.override`.
- Role grants are scoped at tenant/property/outlet/department, with grantor authority checked to prevent privilege escalation.

## Authentication controls
Secure password hashing; MFA for platform administrators and strongly recommended/required for Owner/Finance roles subject to approved policy; HttpOnly/Secure/SameSite cookies; CSRF protection where cookie auth requires; session rotation after login/privilege change; login/OTP/reset/invitation rate limits; device/session revoke; generic login errors; verified contact for account recovery and identity linking.

## Guest QR authorisation
QR token resolves target context only. It is not guest authentication. Guest order access uses separate opaque scoped capability. Room QR never reveals guest identity, stay details or folio balance. Room folio requires an active eligible stay plus an authenticated/authorised guest or service session.

## PostgreSQL RLS
Enable RLS for tenant-owned tables. Set tenant context transaction-locally from authenticated validated request context. Add property/outlet conditions where applicable. Worker service roles set explicit tenant scope per validated job. Unset tenant context must return no rows and deny writes. RLS is defence-in-depth; application checks remain mandatory. Normal app traffic must not use a DB superuser.

## Sensitive actions
Use step-up auth and/or dual approval for configured high-impact refunds, payroll release, journal post/reversal, period close, large discounts, entitlement overrides, tenant suspension, export, role escalation and secret rotation. Record actor, approver, reason and safe before/after diff.
