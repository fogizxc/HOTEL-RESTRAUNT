# Security Threat Model and Required Controls

## 1. Security objectives
1. Prevent cross-tenant data access and writes.
2. Prevent cross-property/outlet access where user scope does not permit it.
3. Prevent privilege escalation across platform admin, tenant owner, employee and guest identities.
4. Protect money movement, folio posting, accounting entries, payroll, supplier banking and loyalty value.
5. Protect personal/identity data, room occupancy, staff records and customer communications.
6. Protect QR tokens, sessions, API credentials and provider secrets.
7. Maintain integrity, availability, traceability and recoverability.

## 2. Trust boundaries
- Public internet/customer portal → edge/WAF/API gateway.
- Authenticated user → identity/session service.
- Tenant context → domain service and PostgreSQL/RLS.
- Domain service → outbox/queue/worker.
- Application → payment/email/SMS/WhatsApp/biometric integrations.
- Internal admin/support → privileged platform/tenant control planes.
- Application → private object storage/exports/backups.

## 3. Principal threats and controls

### Cross-tenant IDOR/data leakage
Threat: client changes tenant, property, outlet, booking, order, employee or file ID.
Controls: derive tenant from identity; scope every query; PostgreSQL RLS; service-layer object checks; composite foreign keys; unguessable public references; separate customer/staff DTOs; negative tests for every resource.

### Privilege escalation
Threat: employee grants own role, API accepts hidden admin fields, owner overrides platform deny, support impersonates tenant.
Controls: deny-by-default RBAC; permission grant ceiling; separate platform and tenant identity realms/claims; server-side policy checks; MFA; reauthentication for sensitive actions; maker-checker; audit.

### QR token theft/replay/enumeration
Threat: predictable IDs, token copied, query string logged, brute force, room guest data exposure.
Controls: CSPRNG high entropy, token hash, rate limit, log redaction, revocation, generic failures, no sensitive QR auth, separate room guest verification, scoped service session, token rotation.

### Payment/financial abuse
Threat: fake success redirect, replayed webhook, amount mismatch, duplicate refund, unauthorised cash confirmation, double revenue folio posting.
Controls: signed webhook, idempotency, amount/currency/merchant check, state machine, immutable ledger, dual approval, source reference, reconciliation exceptions, cash pending collection.

### Data injection and application attacks
Threats: SQL/NoSQL injection, XSS, CSRF, SSRF, unsafe file upload, path traversal, deserialisation, template injection.
Controls: parameterised queries, schema validation, output encoding, safe rich text, CSP, CSRF protection where cookie-based, SSRF allowlists, MIME/signature validation, quarantine/malware scanning, strict file keys.

### Account takeover
Threats: credential stuffing, OTP brute force, session theft, reset poisoning.
Controls: rate limit by account/IP/device risk; MFA; breached-password checks where permitted; secure HttpOnly/Secure/SameSite cookies; rotate session at login/privilege change; single-use short-lived reset; generic auth errors; anomaly monitoring.

### Sensitive data exposure
Threat: logs, exports, analytics or backups contain tokens, payment data, documents, guest occupancy or payroll.
Controls: data classification, field-level redaction, encryption in transit/at rest, private storage, least privilege, expiring download URLs, audit exports, retention limits, no secrets in logs.

### Insider abuse
Threat: platform support or staff browse guest/employee records or issue refunds without business need.
Controls: role scope, just-in-time access, reason capture, dual approval, immutable audit, anomaly alerting, periodic access review.

### Queue/webhook abuse
Threat: forged, duplicated, out-of-order events; poisoned payloads; retry storm.
Controls: signature validation, schema validation, idempotent consumer, dead-letter queue, bounded retries/backoff, event versioning, correlation IDs, source allowlist as supplemental only.

### Availability and ransomware
Threat: DDoS, resource exhaustion, destructive migration, credential compromise, backup compromise.
Controls: WAF/rate limits, quotas, queue backpressure, encrypted immutable backups, restore drills, least-privilege deploy identity, migration rollback/expand-contract, incident plan.

## 4. Mandatory authentication/session policy
- MFA for platform Super Admin, owner, Finance, HR/payroll admin and other privileged roles.
- Secure password hashing (Argon2id or current approved equivalent) with parameters benchmarked on production runtime.
- Secure cookies; no session IDs in localStorage for privileged web sessions.
- Rotate session after login, MFA, password reset, role or tenant context change.
- Revoke active sessions on disable/suspension, password reset and role reduction according to policy.
- Rate limit login, password reset, OTP, search, QR resolution, exports and high-risk mutations.

## 5. Tenant data isolation
- `tenant_id` must exist on all tenant-owned aggregate roots and key child tables where it materially strengthens constraints.
- Composite FKs enforce tenant/property/outlet parent relationships.
- PostgreSQL RLS enabled and forced on tenant tables for app roles where feasible.
- Use transaction-local tenant context and clear it reliably on pooled connections.
- Application DB role must not own tables or bypass RLS.
- Background jobs carry tenant context explicitly and reauthorise job scope.
- Cross-tenant admin tasks use a separate privileged service account with narrow scopes and audit.

## 6. Data classification and handling
Classify as public, internal, confidential, restricted. Restricted includes credentials/secrets, payment tokens, identity documents, HR/payroll, room occupancy/guest details, supplier bank details and security events. Apply least-privilege read, masking, encryption, retention, export and audit.

## 7. Security logging and incident response
Log authentication outcomes, authorization denials, privilege changes, money movements, QR revocations, data exports, sensitive record views, provider failures and suspicious cross-tenant IDs. Never log passwords, OTPs, access tokens, raw QR token, payment card data or private document contents.

Incident flow: detect → triage/severity → contain → preserve evidence → eradicate → recover → notify as legally required → postmortem/remediation. Maintain incident owner and timestamps. Legal notification periods are unresolved until jurisdictions are approved.

## 8. Release security gates
- No critical/high unresolved security findings without documented risk acceptance by authorised owner.
- SAST, dependency scan, secret scan, container/image scan, DAST/API tests.
- Tenant IDOR matrix passes.
- Payment/webhook/QR/f​​olio abuse tests pass.
- Backup restore and key rotation exercises completed.
- Independent penetration test before production launch; no certification claim without report.
