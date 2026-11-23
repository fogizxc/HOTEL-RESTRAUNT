# Deployment and Environment Runbook

## 1. Environment separation
Maintain separate local, test/CI, staging and production environments. Production data and provider credentials must never be copied into local/test. Test provider accounts and webhook URLs must not point to production.

## 2. Configuration and secrets
- Configuration is environment-specific and validated at startup.
- Secrets managed by cloud secret manager or equivalent; least-privilege service identities.
- Never commit `.env`, credentials, signing keys, database URLs, provider tokens or real customer exports.
- Rotate secrets with overlap procedure where provider supports it.
- Frontend only receives public configuration; server secrets never enter bundle/source maps.

## 3. Deployment architecture (vendor-neutral baseline)
- TLS-terminating edge/WAF/reverse proxy.
- Stateless web/API application instances.
- PostgreSQL managed database with backups, PITR if selected, private networking and restricted app role.
- Redis or equivalent for ephemeral cache/locks/session state where required; not source of truth.
- Object storage private buckets for sensitive assets.
- Durable queue + transactional outbox workers.
- Central logs/metrics/traces and alerting.
- CDN only for approved public assets; private guest/HR/financial documents remain protected.

Cloud provider/region is an open decision; do not assume AWS/Azure/GCP/Render/Fly/etc.

## 4. Database migration policy
- Migrations are version-controlled, reviewed and tested against clean DB and previous release DB.
- Use expand/contract for zero/low-downtime schema changes.
- No automatic destructive migration in production.
- Back up before high-risk schema changes.
- RLS policy changes tested for app and worker roles.
- Seed data is synthetic and never overwrites production.

## 5. Release process
1. Freeze scope and record commit SHA.
2. CI checks pass.
3. Deploy to staging.
4. Apply additive migrations.
5. Run smoke tests: login, tenant scoping, reservation quote, POS order/KOT, QR safe resolution, payment sandbox, finance read/report, HR permission, customer portal.
6. Confirm monitoring/alerts and provider health.
7. Owner authorises production release.
8. Deploy gradually/blue-green or rolling with health checks.
9. Run post-deploy smoke tests and monitor error/latency/queue metrics.
10. Keep rollback plan and compatible schema until release is stable.

## 6. Rollback
- Application rollback does not necessarily imply DB rollback.
- Prefer forward-fix for data migrations; destructive rollback only with tested procedure and owner approval.
- Reconcile jobs/webhooks after rollback; avoid duplicate processing.
- Preserve schema compatibility across application versions during deployment window.

## 7. Backup and disaster recovery
- Define RPO/RTO and retention by business/legal decision.
- Encrypt backups, restrict access, enable immutable/object lock where supported.
- Include database, object storage metadata/files, secret references/config and required audit/event records.
- Run periodic restore drill and document actual duration/data loss.
- Backup success notification is not proof of restorability.

## 8. Domain, TLS and customer portal
- Verify domain ownership, TLS certificate, secure cookies, canonical host and redirect allowlist.
- Tenant custom domains require domain verification and mapping to tenant/property; prevent domain takeover and cross-tenant host routing.
- QR URLs use stable owned domain, never expose internal hostnames.

## 9. Go-live checklist
- Jurisdiction, legal entity, tax, privacy/terms, data retention and consumer policy approved.
- Payment merchant account and webhook verified.
- Messaging sender IDs/templates approved.
- Support escalation/incident rota assigned.
- Backup restore test passed.
- Security assessment/penetration test complete with high risks remediated or explicitly accepted.
- Performance capacity/load test met agreed targets.
- Tenant/RBAC isolation suite passes.
- Monitoring, alerting, logs and audit review in place.
- Data migration validated and rollback/restore plan approved.
