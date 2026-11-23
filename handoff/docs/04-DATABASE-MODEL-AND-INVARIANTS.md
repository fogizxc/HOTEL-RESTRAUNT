# 04 — Database Model and Invariants

This is the logical schema baseline, not an executable migration set. The implementation agent must map it to the actual ORM, create migrations, constraints and indexes, and prove clean install/upgrade/rollback.

## 1. Global database rules
- PostgreSQL is the transactional source of truth.
- UUID primary keys for externally addressable records; never rely on sequential IDs for access control.
- Every tenant-owned row carries `tenant_id`. Use composite FKs or equivalent constraints to prevent references across tenants.
- Property/outlet-owned rows carry the corresponding scope fields.
- Use UTC timestamps; store property timezone and business-day boundaries separately.
- Money is integer minor units plus ISO currency, or exact NUMERIC with declared scale. Choose one approach consistently; never use float/double.
- Quantities use exact NUMERIC and explicit unit precision.
- Posted accounting, stock, folio and audit records are append-only; correct via reversal/compensating entry.
- Soft-delete only where domain semantics permit. Never hard-delete ledger, audit or financial history.

## 2. Platform / organisation
- `tenants(id, legal_name, display_name, status, timezone, default_currency)`
- `tenant_settings(tenant_id, settings_json, version)`; validate schema; no secrets.
- `properties(id, tenant_id, code, name, property_type, timezone, address, status)` unique `(tenant_id,code)`.
- `outlets(id, tenant_id, property_id, code, name, outlet_type, timezone, status)` unique `(tenant_id,property_id,code)`.
- `departments(id, tenant_id, property_id nullable, code, name, status)`.
- `legal_entities(id, tenant_id, legal_name, protected_registration_ids, currency, status)`.

## 3. Identity / RBAC
`users`, `tenant_memberships`, `roles`, `permissions`, `role_permissions`, `membership_role_assignments`, `scope_assignments`, `invitations(token_hash only)`, `sessions(session_hash only)`, `mfa_methods`, `mfa_recovery_codes`. Enforce uniqueness for normalised identities per approved account-linking model. Platform admin roles are distinct.

## 4. Subscription
`plans`, `plan_entitlements`, `subscriptions`, `subscription_items`, `platform_entitlement_overrides`, `tenant_feature_settings`, `usage_counters`, `subscription_invoices`, `subscription_payment_attempts`, `billing_payment_methods(provider_token_reference_only)`.
Effective feature evaluation is: platform tenant/status gate → platform override → plan entitlement → tenant configuration → runtime feature policy. Employee permission remains separate.

## 5. Hotel
- `room_types(id, tenant_id, property_id, code, name, capacity, amenities_json, status)`
- `rooms(id, tenant_id, property_id, room_type_id, room_number, floor, operational_status, version)` unique room number per property.
- `rate_plans`, `room_rates` with per-date price, inventory and restrictions snapshot.
- `reservations(id, tenant_id, property_id, reference, status, arrival_date, departure_date, adults, children, source, guest_id, currency, totals, version)`.
- `reservation_rooms`, `stays`, `folios`, `folio_entries`, `hotel_service_requests`.
- `folio_entries` append-only; require unique source-domain/source-ID/idempotency reference.
- Prevent overlapping confirmed assignments for same room/date range via exclusion constraint or serialised availability allocator.
- Create bounded inventory holds during payment; release on expiry/failure.

## 6. Restaurant / KDS
- `floors`, `dining_tables`, `table_sessions` (at most one active session per table), `menu_categories`, `menu_items`, `menu_item_variants`, `modifier_groups`, `modifier_options`, `menu_schedules`, `outlet_item_availability`.
- `orders(id, tenant_id, property_id, outlet_id, channel, table_session_id, room_stay_id, customer_id, status, currency, totals, idempotency_key, version)`.
- `order_items` store immutable item/price/tax/modifier snapshots accepted at order time.
- `bills`, `bill_allocations` for consolidated/separate/item/equal/custom split.
- `kitchen_tickets`, `kitchen_ticket_items`; station child items fan out from order lines without duplicating parent sales.

## 7. QR
- `qr_destinations(id, tenant_id, property_id, outlet_id nullable, destination_type, target_id, display_label, status)`.
- `qr_tokens(id, tenant_id, destination_id, token_hash, token_version, status, issued_at, expires_at, revoked_at, rotated_from_id)`; raw token is never stored after issue.
- `qr_scan_events(id, tenant_id, token_id, scanned_at, channel, coarse_device_metadata, outcome)`; privacy-minimised.
- `qr_sessions(id, tenant_id, destination_id, table_session_id nullable, stay_id nullable, status, capability_hash, expires_at)`.
- Destination types initially `TABLE`, `ROOM`; add POOLSIDE/BANQUET/LOUNGE/PICKUP_POINT only after their workflows exist.

## 8. Inventory / procurement
`inventory_items`, `units_of_measure`, `unit_conversions`, `stock_locations`, `stock_batches`, `recipes`, `recipe_versions`, `recipe_ingredients`, `inventory_ledger_entries`, `stock_reservations`, `stock_counts`, `stock_count_lines`, `transfers`, `purchase_requisitions`, `suppliers`, `purchase_orders`, `purchase_order_lines`, `goods_receipts`, `goods_receipt_lines`, `supplier_returns`, `wastage_records`.
Stock-on-hand is derived from ledger and reservations; any cached total must reconcile. FEFO applies where expiry tracking is enabled.

## 9. Finance
`chart_of_accounts`, `accounting_periods`, `journal_entries`, `journal_lines`, `accounts_receivable_documents`, `accounts_payable_documents`, `cash_accounts`, `bank_accounts`, `bank_statement_lines`, `reconciliation_matches`, `tax_codes`, `cost_centres`, `budgets`, `fixed_assets`, `asset_depreciation_entries`.
Posted journal entries are immutable. Enforce balanced debits/credits per legal entity/currency and unique source posting reference.

## 10. HR
`employees`, `employment_contracts`, `employee_documents`, `departments`, `designations`, `attendance_records`, `time_clock_devices`, `shift_templates`, `employee_shifts`, `leave_policies`, `leave_balances`, `leave_requests`, `pay_components`, `salary_structures`, `payroll_runs`, `payroll_lines`, `payslips`, `performance_reviews`, `training_records`.
Do not store raw biometric templates by default; use approved device event references and legal/privacy review.

## 11. CRM
`customer_profiles`, `customer_identity_links`, `customer_consents`, `customer_preferences`, `segments`, `loyalty_programs`, `loyalty_accounts`, `loyalty_ledger_entries`, `reward_catalogue`, `reward_redemptions`, `membership_plans`, `customer_memberships`, `campaigns`, `campaign_deliveries`, `feedback_records`, `complaints`, `communication_threads`.
Identity merge requires verified evidence and audit. Consent history and points ledger are append-only/versioned.

## 12. Cross-cutting records
`audit_events`, `outbox_events`, `idempotency_records`, `api_clients`, `webhook_receipts`, `notification_messages`, `notification_preferences`, `support_tickets`, `support_messages`, `file_objects`, `export_jobs`, `import_jobs`, `data_subject_requests`.

## 13. Required constraints and indexes
- Composite tenant indexes `(tenant_id,id)` and tenant/property/outlet/status/date indexes for common queries.
- Tenant-scoped business codes unique.
- Idempotency key unique within tenant + operation scope.
- Composite FKs prevent cross-tenant references.
- One active table session per table.
- Reservation overlap prevention.
- Provider event ID/transaction ID unique.
- Journal and folio source posting unique.
- Stock movement source/idempotency unique.
- Ledger/audit update/delete blocked through DB roles/triggers as appropriate.

## 14. Migration acceptance
Each migration must apply on empty DB, upgrade supported previous schema, preserve data, include index/locking plan and roll back where safe. Large tables require online/non-blocking migration strategy. Backfills and dual writes must be documented and verified.
