# Panel 02 — Organisation Owner / Management

## 1. Purpose and authority
This workspace lets the tenant owner and delegated managers configure their own organisation, properties, outlets, business users, roles, preferences, and subscription visibility. It is the tenant's management control plane, not the SaaS platform control plane.

It cannot create platform plans, override a platform deny, bypass subscription quotas, or grant permissions beyond the owner's authority. All reads/writes are scoped to the authenticated `tenant_id`; property and outlet actions require explicit assigned scope.

## 2. Pages and exact behaviour

### 2.1 Management overview
- Organisation-level summary: properties, outlets, operational module availability, active users, outstanding setup tasks, subscription usage, high-priority alerts.
- Filters by property, outlet, and reporting period where metrics exist.
- A metric opens the correct source workspace rather than editing data from the dashboard.

### 2.2 Organisation profile
- View/edit legal name, display name, contact details, billing contact, address, registered identifiers (jurisdiction-specific), locale, timezone defaults, and logo.
- Show audit and effective settings; immutable transaction currency/timezone snapshots are not rewritten by future profile edits.
- Restrict sensitive legal/tax fields to authorised roles.

### 2.3 Property directory
- Search/filter properties by status, type, location, timezone, operational readiness.
- Create/edit/archive property; archive is blocked if active reservations, open orders, unsettled funds, inventory or legal records require resolution.
- Property has unique tenant-scoped code, name, type, address, timezone, currency, contact, local settings and status.
- A change of timezone affects future operations only; historical timestamps remain stored in UTC and displayed using the record's applicable context.

### 2.4 Property setup wizard
Steps: profile/address; business capabilities; hotel settings if hotel enabled; restaurant outlet setup if restaurant enabled; tax/pricing defaults; operating hours; branding; staff scope; review.
- Hotel-only tenant must not be forced to configure restaurant outlets.
- Restaurant-only tenant must not be forced to configure hotel rooms.
- Combined tenant can configure each capability independently.

### 2.5 Outlet directory and editor
- Create any configured number of outlets subject to active subscription quota.
- Outlet belongs to one property and tenant; fields include outlet code, name, type, timezone inherited/overridden, hours, service modes, menu configuration, contact, status.
- Room-service kitchens can be configured as service outlets/kitchen destinations without being exposed as public dining outlets unless explicitly configured.
- Prevent outlet deletion while orders, sessions, QR assignments, stock, or accounting references remain; use archive/deactivation and reassignment workflows.

### 2.6 Departments and organisational structure
- Manage departments, reporting units, titles and manager assignments.
- Department record is not itself an application permission.
- Changes to department structure do not silently change existing access; show proposed access impact separately.

### 2.7 Employee and access overview
- Employee directory is linked to HR's authoritative employment record; owner can invite/deactivate within permitted authority and view role/property access.
- Do not duplicate payroll, attendance or contract data into this workspace.
- Employee invitation is single-use, expiring and scoped. First login requires account setup/verification.

### 2.8 Roles and permission assignments
- Display role templates, custom roles if enabled, permissions grouped by domain/action, property/outlet scopes, and user assignments.
- Use deny-by-default. Check permissions server-side and at object scope.
- Prevent owner from granting platform-only capabilities or permissions the owner does not hold.
- Show effective permission preview: user × role × property/outlet × module entitlement × organisation restriction.
- High-risk role changes require reauthentication and audit.

### 2.9 Module and feature availability
- Show effective available modules/features, plan inclusion, platform restrictions, organisation preferences, and usage quota.
- Organisation may configure optional module preferences only within granted entitlements.
- Disabled module navigation is hidden or replaced with a clear unavailable notice; API remains denied.

### 2.10 Subscription and usage
- View current plan, add-ons, renewal, invoice links, usage and limit warnings.
- Plan changes are routed through authorised subscription workflow; owner cannot directly modify platform entitlement records.

### 2.11 Branding and customer-facing configuration
- Upload logo/brand assets, select supported theme tokens, property-specific presentation and customer portal content.
- Validate image formats/sizes; store private uploads until approved/publicly published.
- Provide live preview separated from published state.

### 2.12 Operating policies and settings
- Separate settings pages for hotel policies, restaurant service settings, currencies, locale, tax configuration, notifications, QR rules, guest checkout and privacy preferences.
- Configuration edits are versioned; preserve historical transaction snapshots.
- Tax/legal settings remain provisional until country/jurisdiction-specific advice and configuration are approved.

### 2.13 Integrations, audit, notifications, support and security
- Tenant-scoped integration setup with secrets stored via a secret manager; test connection, redacted status, disconnect confirmation.
- Business audit shows only tenant-authorised events; no ability to alter audit history.
- Notification preferences cannot disable legally/operationally required transactional or security alerts.
- Support tickets do not expose credentials or full payment instruments.
- Account security: MFA, sessions, recovery, sign-in history, owner-sensitive action reauthentication.

## 3. Required invariants
- `tenant_id` is server-derived from authenticated identity, never trusted from arbitrary client body/query.
- Every property belongs to the same tenant; every outlet belongs to its parent property and tenant.
- An employee may be assigned multiple allowed properties/outlets but cannot access unassigned scope.
- Platform entitlements are enforced independently of owner settings and employee roles.

## 4. Acceptance tests
- Owner from Tenant A cannot enumerate or edit Tenant B's property/outlet IDs.
- Owner cannot enable a module denied by platform override.
- Role assignment cannot exceed assigner's grantable permission set.
- Deleting a property/outlet with financial/operational references is blocked and yields a remediation path.
- Property timezone changes do not rewrite historical transaction timestamps.
- Employee department change does not silently grant permissions.
- Branding preview is not public until published.
- All changes are tenant-scoped, versioned where needed, and audited.
