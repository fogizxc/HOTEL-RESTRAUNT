# Panel 01 — SaaS Super Admin

## 1. Purpose and access boundary
The Super Admin workspace is the platform operator's control plane. It manages tenant lifecycle, platform plans, subscription entitlements, provider configuration, platform staff, support escalation, platform health, and global security. It is not an organisation's day-to-day hotel or restaurant workspace.

**Authoritative scope:** platform-level tenant and subscription records, platform-level entitlement overrides, platform administrator identities, platform audit events, global provider configuration. It must not directly write tenant operational records such as reservations, restaurant orders, employee attendance, stock movements, or tenant GL journals.

**Roles:** `platform_owner`, `platform_super_admin`, `platform_support`, `platform_finance`, `platform_security`, with least-privilege scopes. Only `platform_owner` may perform high-risk owner-only operations such as changing another Super Admin's privileges, disabling core security controls, or transferring platform ownership. Support staff get scoped, audited, time-limited access to tenant support data.

## 2. Page inventory and exact behaviour

### 2.1 Platform overview
- Display tenant counts by lifecycle state, active subscriptions, trial tenants, suspended tenants, MRR/ARR if the finance source is configured, recent platform events, service health, queue health, and security alerts.
- Every metric must declare its definition, currency, timezone, period, and data freshness.
- Clicking a metric opens the corresponding filtered list with the same date range and scope.
- Do not fabricate live telemetry or revenue. Show `Unavailable` or `Last updated` when the provider/source is disconnected or stale.

### 2.2 Organisation directory
- Search by organisation legal/display name, owner email, tenant slug, status, plan, creation date, and assigned platform support owner.
- Filters: status, plan, enabled business modules, region, onboarding completion, and risk flags.
- Columns: name, tenant reference, business type, property count, active modules, plan, subscription status, owner, created date, last activity, action menu.
- Actions: open details, start onboarding support, suspend/reactivate subject to authority, initiate owner reset workflow (never view or set an existing password), export filtered records with permission and audit.
- Search and exports must be server-side and tenant-safe; platform list access must not leak credentials or unnecessary personal data.

### 2.3 Create organisation wizard
Required steps:
1. Legal/display name, tenant slug, country/region, legal entity reference (when applicable), primary owner email, contact, locale/timezone/currency.
2. Business capability: hotel, restaurant, or combined.
3. Initial properties; property type, address, timezone, currency, contact. Do not force outlets on hotel-only organisations.
4. Restaurant outlets per property; support zero/many outlets within subscription constraints.
5. Plan, billing period, trial dates if permitted, and initial entitlement snapshot.
6. Optional add-ons and quantitative quotas (properties, outlets, users, QR targets, storage, etc.). Quotas are configurable product policy, never hardcoded global counts.
7. Invite owner via single-use, expiring invitation. Never generate a password and display it to Super Admin.
8. Review, explicit confirmation, provisioning status, and audit reference.
- Creation must be atomic or resumable through a provisioning state machine; do not expose a partially provisioned active tenant.
- Idempotency key is required for provisioning retries.

### 2.4 Organisation detail
Tabs: overview, properties/outlets, subscription, entitlements, usage/quotas, owners/admin contacts, support, security events, platform audit, provisioning history.
- Show tenant status and reason; suspend with reason, actor, time, scope and effective time.
- Reactivation requires permission and records an audit event.
- Support impersonation is prohibited by default. If a controlled support-assist feature is later approved, it must require tenant-owner consent, narrow read-only scope, short expiry, visible session banner, full audit, and explicit prohibition on financial or destructive actions.

### 2.5 Entitlement manager
- Show subscription grants, platform allow/deny overrides, effective feature state, source/reason, start/end time, and last recalculation.
- Deny overrides take precedence over grants; platform emergency deny must be auditable and reversible by authorised owner.
- Never conflate feature entitlement with employee RBAC permissions or organisation preferences.
- Preview effective features before save; show affected properties/workspaces and user-visible impact.
- Changes are versioned and emit `entitlements.changed`; cache invalidation and access enforcement must be server-side.

### 2.6 Plan and add-on catalogue
- Create/edit plan versions, module catalogue, feature catalogue, pricing, billing intervals, quotas, trial policy, and availability dates.
- Published plan versions are immutable; create a new version for future sales and preserve existing contract snapshots.
- Prevent deletion of plans/features referenced by active subscriptions; archive instead.
- Provide comparison preview and validation for impossible combinations.

### 2.7 Subscription and platform billing
- List subscriptions, invoices, payment attempts, renewals, failed payments, grace period, cancellations, credits, and provider references.
- Platform billing records must be distinct from tenants' operational payments (guest bills, hotel deposits, restaurant POS payments).
- Do not store raw payment card data. Use provider tokens/references only.
- Manual credit/adjustment requires reason, permission, dual control above configured threshold, and audit.

### 2.8 Platform administrator directory and role management
- Invite, suspend, reactivate platform users; assign platform roles and scoped permissions.
- Enforce MFA for privileged platform accounts.
- A support role cannot inherit owner permissions. No wildcard permission by default.
- Display permission effective state and why access is granted.

### 2.9 Support and announcements
- Support queue, severity, tenant, requestor, SLA target, assignment and safe tenant-scoped read context.
- Announcements support draft, scheduled, published, expired, target audience, acknowledgement requirement, and version history.
- Never include tenant secrets or private operational data in announcements.

### 2.10 Platform health and integrations
- Health status for database, cache, queue, object storage, email/SMS, payment provider, and critical jobs.
- Per integration: configured state, environment, last successful check, last error (redacted), test connection, rotate secret via secret manager, disconnect with impact warning.
- Do not expose secret values after saving.

### 2.11 Security centre and immutable audit
- Display authentication anomalies, rate-limit events, suspicious cross-tenant attempts, privileged actions, webhook failures, and security incident records.
- Audit fields: actor type/id, tenant context when applicable, action, resource type/id, timestamp, outcome, reason, request/correlation ID, IP/device metadata subject to privacy policy, before/after diff with sensitive fields redacted.
- Audit store must be append-only to application roles; corrections use compensating events, never update/delete prior audit rows.

## 3. Required state handling
Tenant states: `draft`, `provisioning`, `trial`, `active`, `past_due`, `grace_period`, `suspended`, `cancelled`, `deleted_pending_retention`. State transitions require explicit policy, authorised actor/system event, reason, effective time and audit. Distinguish a tenant being suspended from an individual user being disabled.

## 4. Acceptance tests
- A platform support user cannot edit plans, entitlements, tenant suspension or platform security settings.
- Creating the same organisation twice with the same idempotency key produces one tenant and one owner invitation.
- A disabled entitlement is rejected by API even if a frontend route is manually opened.
- Plan version changes do not rewrite existing subscription contract snapshots.
- Super Admin cannot view passwords, OTPs, raw card data or secret values.
- Tenant suspension blocks tenant access according to documented policy without deleting records.
- All privileged changes create immutable audit events.
- Platform dashboard metrics disclose source, scope, period and freshness.
