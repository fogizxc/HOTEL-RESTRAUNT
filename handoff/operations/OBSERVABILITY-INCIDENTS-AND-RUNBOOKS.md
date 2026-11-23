# Observability, Incident Management and Runbooks

## 1. Observability baseline
Instrument structured logs, metrics and traces with shared `request_id`, `correlation_id`, `tenant_context` (opaque internal identifier, access controlled), `property_id` when safe, service, version and environment.

Never log passwords, OTPs, access/session tokens, raw QR tokens, card data, full identity document numbers, private file content or provider secrets.

## 2. Required metrics
- API request count, latency p50/p95/p99, error rate, saturation, rate-limit blocks.
- Authentication failure and lockout rates; authorization denials; cross-tenant probe alerts.
- DB connection pool, query latency, lock contention, replication lag, RLS errors.
- Queue depth, oldest job age, retries, dead-letter count, consumer lag, outbox age.
- Payment intent/webhook success/failure/latency, mismatch count, settlement unmatched value.
- QR resolution success/failure, rate-limit events, invalid token volume (without raw token).
- Reservation conflicts, order acceptance latency, KOT dispatch/ack latency, KDS overdue rate.
- Inventory ledger projection lag and reconciliation difference.
- Finance source event backlog, journals waiting approval, reconciliation exceptions.
- Storage upload/scanning failures, export job age, email/SMS provider delivery status.

## 3. Alerts and severity
Define per environment thresholds from agreed SLOs. Suggested severity categories:
- SEV1: confirmed cross-tenant exposure, payment/ledger corruption, platform-wide outage, credential compromise.
- SEV2: major workflow unavailable for a property/tenant cohort, payment processing degraded, queue backlog threatens fulfilment.
- SEV3: isolated functional issue with workaround.
- SEV4: low-impact defect or request.

Each alert has owner, runbook link, impact scope, threshold, deduplication and escalation route. Do not invent SLO targets until scale/hosting assumptions are approved.

## 4. Runbook — payment provider outage
1. Confirm provider status and internal error rate.
2. Disable unsafe retries that could duplicate charges; preserve idempotency keys.
3. Leave uncertain payments pending; query provider before marking failed/captured.
4. Inform affected tenant support contacts using approved status template.
5. Reconcile pending intents after recovery; route mismatches to finance exception queue.
6. Record incident and provider reference; do not manually flip payment status without evidence.

## 5. Runbook — queue/outbox backlog
1. Check DB outbox age, queue availability, worker health and downstream limits.
2. Pause noncritical workload if critical payment/order jobs are starved.
3. Scale workers within provider/database capacity.
4. Replay idempotently; never delete pending messages just to reduce queue length.
5. Inspect dead-letter jobs and correct schema/config before replay.
6. Verify domain reconciliation after drain.

## 6. Runbook — suspected tenant data leakage
1. Declare security incident and preserve logs/traces.
2. Restrict affected endpoint/token/role; do not wipe evidence.
3. Identify affected tenant/resource/time window using correlation IDs.
4. Revoke exposed sessions/tokens/QR tokens as appropriate.
5. Notify security owner and legal/privacy owner; determine legal notification duty by jurisdiction.
6. Patch and run tenant isolation suite before restoring access.
7. Perform documented postmortem and access audit.

## 7. Runbook — database restore/recovery
1. Declare recovery event and freeze risky writes/deployments.
2. Identify approved backup and target recovery point.
3. Restore to isolated environment; validate checksums, schema, RLS policies, extensions and application compatibility.
4. Replay transaction logs/CDC if supported and authorised.
5. Verify financial/operational invariants and tenant counts.
6. Obtain owner approval before production cutover.
7. Preserve evidence and report achieved RPO/RTO; never claim target met without measurement.

## 8. Runbook — key/secret compromise
1. Identify secret scope and access window.
2. Rotate/revoke via secret manager/provider console.
3. Update adapter config and restart/refresh safely.
4. Revoke sessions/tokens if affected.
5. Review access logs and outbound events.
6. Verify old key no longer authenticates; record evidence.

## 9. Incident postmortem fields
Incident ID, severity, start/detection/mitigation/resolution time, affected services/tenants, impact, timeline, cause, contributing factors, data/financial impact, communications, remediation owner/due date, verification evidence and lessons. Blameless, factual, no secrets or unnecessary PII.
