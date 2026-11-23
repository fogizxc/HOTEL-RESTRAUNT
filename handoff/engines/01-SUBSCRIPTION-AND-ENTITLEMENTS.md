# Shared Engine 01 — Subscription, Modules, Entitlements and Quotas

## 1. Purpose
Resolve whether a tenant/property/outlet may use a module or feature at a given time, separately from whether an individual user has permission to perform a specific action.

## 2. Concepts that must remain distinct
1. **Product catalogue:** modules, features, plan versions, add-ons and quota definitions.
2. **Subscription contract:** tenant's purchased plan/add-on snapshot, billing cycle, term and status.
3. **Platform override:** platform-controlled allow/deny with reason, scope, effective dates and audit; emergency deny must be enforceable immediately.
4. **Tenant configuration:** tenant preference/settings for features already granted; cannot override platform deny or exceed quotas.
5. **RBAC permission:** actor permission to perform an action; cannot grant a feature not entitled.
6. **Quota:** numerical/usage limit such as properties, outlets, seats, QR targets, storage or API usage.

## 3. Effective access algorithm
For request `(tenant, resource scope, feature, user, action)`:
1. Authenticate identity; resolve tenant from trusted session/token mapping.
2. Confirm tenant state permits service.
3. Confirm subscription effective and not beyond grace/suspension policy.
4. Evaluate platform overrides for tenant/feature/scope. Explicit deny wins.
5. Evaluate plan and add-on grants at current time.
6. Evaluate feature availability for requested property/outlet capability.
7. Evaluate tenant-level setting, if feature supports tenant configuration.
8. Evaluate employee RBAC permission for requested action and resource scope.
9. Enforce quota on creation/consumption path transactionally.
10. Return effective result with non-secret reason code; log security-sensitive denials.

Do not make frontend route visibility the enforcement mechanism. Every API/service operation re-evaluates server-side.

## 4. Required records (logical)
`product_module`, `product_feature`, `plan_version`, `plan_feature_grant`, `plan_quota`, `subscription`, `subscription_item`, `subscription_contract_snapshot`, `platform_entitlement_override`, `tenant_feature_preference`, `usage_counter`, `usage_event`, `billing_invoice`, `billing_payment_attempt`, `entitlement_evaluation_audit`.

Plan versions are immutable once sold. Subscription has its own snapshot so later catalogue changes do not silently alter an active contract. Usage events are idempotent and auditable; counters may be materialised.

## 5. Quota enforcement
- Count current resources and reserved/pending resources consistently.
- Creation flow locks or atomically reserves quota, creates entity, then finalises usage.
- Concurrent creation must not exceed quota.
- Deletion/archive releases quota only when product policy says resource no longer consumes it.
- QR quota counts active targets or other explicitly defined measure; revoked tokens do not remain active quota if policy says otherwise.
- API usage/storage quotas must define measurement window and reset rules.

## 6. Lifecycle and failure policy
Statuses include trial, active, past_due, grace_period, suspended, cancelled, expired. Define feature-by-feature grace policy; never globally decide to delete or hide operational data on non-payment. Existing guest stays/orders/accounting records must remain accessible for lawful operations and export under controlled read-only rules where applicable.

## 7. Events
Emit versioned events such as `subscription.created`, `subscription.changed`, `subscription.past_due`, `subscription.suspended`, `subscription.reactivated`, `entitlements.changed`, `quota.threshold_reached`, `quota.exceeded`. Use transactional outbox and idempotent consumers. Cache invalidation is not the only security enforcement; API checks remain authoritative.

## 8. Required tests
- Plan includes restaurant but platform deny disables it: API rejects.
- Plan grants feature but employee lacks action permission: API rejects.
- Employee has permission but tenant subscription lacks feature: API rejects.
- Concurrent property/outlet/QR creation cannot exceed quota.
- Plan version update does not mutate an existing contract.
- Cancelled/suspended tenant cannot create new billable resources.
- Grace-period policy preserves allowed read/closeout functions and denies restricted creation as configured.
