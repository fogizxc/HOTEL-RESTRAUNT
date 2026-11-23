# 07 — Supporting Screens and Global Application Utilities

These are cross-workspace experiences, not additional business panels. Implement shared versions and reuse them; do not create 12 incompatible copies of login, notifications or account security.

## A. Authentication and account security

### A1 Login
Fields: identifier/email, password, optional tenant/account hint where required. Controls: show/hide password, submit, forgot password, help. States: invalid credentials (generic), rate limited, account locked, tenant suspended, MFA required, network failure, session routing.

Success resolves authorised tenant memberships and permitted workspaces. If multiple memberships exist, show only memberships the user may access. Never trust a tenant ID supplied by the browser as authority.

### A2 Forgot password and reset
- Request accepts identifier and returns generic confirmation.
- Reset token is single-use, hashed at rest, short-lived and invalidated after use.
- New password confirmation and password policy validation.
- Expired, reused, malformed token state.
- Successful reset revokes or rotates existing sessions per security policy and triggers security notification.

### A3 Email/phone verification and OTP
- OTP has expiry, attempt limit, resend cooldown and rate limit by account/network/risk.
- Do not reveal account existence.
- Verify destination and purpose; a login OTP cannot be replayed as a password-reset token.
- Handle invalid, expired, exhausted attempts and delivery failure.

### A4 MFA
- Enrol supported authenticator/passkey/provider methods after reauthentication.
- Confirm setup challenge before activation.
- Recovery codes shown once; store verifier/hash only.
- Require MFA for privileged roles. Account recovery is separately audited and may require support approval.

### A5 Invitations
- Invitation is single-use, expiring, scoped to tenant and intended identity/role.
- Invitee verifies destination, accepts terms/policy where configured, sets credential and MFA as required.
- Revoke/resend creates a new token; old link invalid.
- Do not send an existing password or pre-create a reusable shared staff credential.

### A6 Sessions and devices
- Show active sessions with device/browser metadata, approximate location only if permitted, last activity and created time.
- Revoke individual/all sessions; show confirmation and current-session result.
- Session rotates after authentication, MFA, privilege change and sensitive account recovery.

## B. First-time organisation onboarding

### B1 Welcome/setup checklist
Display required steps and completion state; persist progress; allow resume. Distinguish required setup blockers from optional enhancements.

### B2 Business capability selection
Organisation capability must align with purchased modules and platform grants. Hotel-only must not see required restaurant setup. Restaurant-only must not see hotel rooms. Combined can configure both. Do not enable unpurchased capability.

### B3 Profile and property
Collect legal/display organisation information, locale/timezone/currency defaults, address, contact, logo and first property data. Do not imply a tax/legal registration is verified unless external evidence is integrated.

### B4 Outlets and departments
Create outlet under a property with unique tenant-scoped code. Configure departments and initial operating hours. A property may have multiple outlets. A hotel room-service kitchen may be operational destination but not a public restaurant outlet.

### B5 Staff setup
Invite users and choose role templates and assigned scopes. Show exactly what access is being granted. Do not grant all modules by default.

### B6 Module review and setup completion
Show available modules, plan grants, platform restrictions, quota usage and optional configuration. Completion page shows configured, pending and skipped setup tasks and directs user to appropriate workspace.

## C. Global utilities

### C1 Notification centre
Unread/read, category, timestamp, safe message, action link. Mark read/unread, mark all read, filter, preference link. Recheck permission when opening linked object. Avoid embedding confidential record payloads in push/email preview.

### C2 Global search
Search only indexed resources the principal is permitted to access. Server-side tenant/property/outlet/RBAC filters apply before results return. No-results, loading, provider/index outage and clear-search states. Search result preview must use safe DTOs.

### C3 Profile/preferences
Edit own profile; choose locale/timezone/theme, supported accessibility preferences and notification channels. Profile changes requiring verification must use a verification workflow. Never allow a user to change tenant identity by editing a profile field.

### C4 Property/outlet switcher
List only assigned and enabled locations. Switching changes active context and refreshes navigation/data scope. Do not retain stale cross-property data in client cache. Reevaluate role and entitlement after switch.

### C5 Help/support
Create a tenant-scoped ticket, category, priority, safe attachment and contact preference. Show ticket status and customer-safe conversation. Internal support notes remain private. Do not attach secrets or payment card data.

### C6 Announcements/release notes
Draft, approval, schedule, target audience, publish, expire and acknowledge. Audience targeting must be scope-safe. No secret/tenant operational data in global announcement.

### C7 Status/maintenance
Show platform incidents and planned maintenance. Only present live state if connected to monitoring/status provider. Otherwise label as informational/static.

## D. Subscription lifecycle supporting pages
- Current subscription/plan/add-ons/renewal/usage view.
- Plan comparison with exact included features and quota units.
- Upgrade/downgrade preview including effective date and proration if applicable.
- Add-on enable/disable request.
- SaaS invoices and payment history.
- Provider-managed payment method update; no card data stored by HospitalityOS.
- Failed payment/retry/update payment method.
- Grace-period warning and restricted/suspended subscription page.
- Usage threshold and quota exceeded state.
- Feature not included/disabled by platform/entitlement expired explanation.

Tenant subscription billing is distinct from guest reservation/order payments.

## E. Universal error/recovery pages
403 access denied, 404 not found, 500 internal error, 429 rate limited, 503 maintenance/unavailable, expired session, offline/network interruption, no permission for action, no results, import/export failure and unsaved changes.

Every state must tell the user what happened at a safe level, what they can do next, and how to contact support where appropriate. Never disclose whether a private resource exists when the actor lacks permission.

## F. Acceptance criteria
- Shared page has consistent shell, language, navigation and responsive behaviour.
- Authentication errors do not enumerate accounts.
- Tenant/property context switch purges/refetches scoped cache and rechecks server permissions.
- Search and notifications never bypass resource authorisation.
- Subscription grace/suspension state is reflected consistently but does not delete operational history.
- A 403/404 response cannot be used to infer private record existence.
- Global controls support keyboard navigation, visible focus and accessible labels.
