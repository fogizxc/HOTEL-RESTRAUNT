# Integrations and Provider Adapter Contracts

## 1. General integration rule
All external providers sit behind internal adapters. Domain services call stable HospitalityOS interfaces; provider-specific payloads/statuses are mapped into canonical internal states. Credentials live in a secret manager, never in source code, database plaintext, frontend bundle or logs.

Every adapter must define: configuration schema, required secrets, environment, health check, request timeout, retry/backoff, idempotency, webhook verification, provider status mapping, failure modes, redaction policy, test/sandbox mode and operational owner.

## 2. Payments
- Internal interface: create intent, capture/authorise where supported, query intent, refund, verify webhook, reconcile settlement.
- Payment adapter must not own invoice/bill/folio business rules.
- Support provider account per legal entity/property where commercially and legally required.
- Keep test/live credentials strictly separated.
- Verify signature, merchant, currency, amount, provider reference, event timestamp/replay and idempotency.
- Do not store PAN/CVV or log payment secrets.
- Provider selection is an open owner decision; do not hardcode Razorpay/Stripe/PayU/other as final.

## 3. Email, SMS and WhatsApp
- Internal interface: send transactional message, send marketing message (consent gate), query delivery state, process delivery webhook, suppress/unsubscribe.
- Templates have version, locale, purpose and preview. Separate transactional from marketing templates.
- Provider webhook must authenticate and deduplicate. Failed delivery is not reported as sent/delivered.
- WhatsApp requires approved official business API and template/consent policies; do not automate personal WhatsApp sessions.

## 4. Object storage and file scanning
- Use private buckets/containers for IDs, HR documents, invoices, exports, QR source assets and other restricted records.
- Customer-public assets are separately published/served from approved public paths.
- Upload path: pre-signed upload with constrained size/type → quarantine → MIME/signature inspection → malware scan → metadata validation → approved object key.
- Download path authorises each request, produces short-lived signed URL or authenticated stream, logs access without file contents.
- No user-supplied raw object key or path traversal.

## 5. Biometric/time-clock devices
- Device adapter normalises punch events, device identity, employee mapping and event time.
- Require device authentication and replay protection.
- Default to retaining punch/attendance result, not raw biometric templates.
- Offline backlog import must deduplicate by stable device event reference and surface unmapped employees.

## 6. POS terminals and fiscal devices
- Terminal adapter maps payment status, terminal receipt, reversals and settlement references.
- Fiscal printer/device integrations are jurisdiction-specific; do not assume tax certification requirements. Keep printer failure separate from payment/transaction success.

## 7. Maps/geocoding
- Use only for property address validation, delivery zone or travel distance if enabled.
- Store provider place reference and owner-entered address; do not use geocoding as the only legal address source.
- Restrict API keys by origin/server and usage. Do not send unnecessary guest/employee PII.

## 8. Analytics and BI ingestion
- Prefer internal outbox/CDC/warehouse feed with documented schema version.
- Avoid sending restricted PII to analytics providers.
- Expose freshness, lag, failed ingestion and replay status.

## 9. Provider onboarding checklist
- Select provider and account ownership.
- Legal/data-processing review and supported countries/currencies.
- Sandbox credentials and test merchant.
- Callback/webhook endpoint and signature verification.
- Idempotency and retry validation.
- Rate limits, timeout and outage strategy.
- Monitoring, alert routing and reconciliation ownership.
- Secret storage and rotation.
- Production approval and rollback plan.

No provider integration is considered operational until sandbox and production verification evidence is recorded.
