# Panel 10 — CRM & Guest Relationships

## 1. Ownership
CRM owns canonical customer/guest identity mapping, profiles, preferences, verified contacts, communication consent, segments, loyalty ledger, membership records, campaigns and customer feedback workflows. PMS and POS retain their own transaction/stay/order records and reference CRM identity. CRM must not rewrite historic operational transaction ownership.

## 2. Pages and behaviour

### 2.1 CRM dashboard
- Show customer count, new/returning customers, loyalty liability/points summary if configured, active memberships, consentable audience size, campaign outcomes and unresolved feedback.
- Metrics disclose attribution window, channel, scope and source freshness.

### 2.2 Customer directory and unified profile
- Search/filter by verified email/phone, customer reference, segment, status, property, last activity and consent state.
- Profile includes contact verification, preferences, customer-safe booking/order history references, communication timeline, loyalty, membership, feedback and privacy request status.
- Keep staff-only internal notes separate from customer-visible communications.

### 2.3 Identity matching, duplicates and merge
- Match candidates using verified contact/identity evidence and explicit matching rules.
- Never auto-merge based solely on name, address, device, or weak similarity.
- Merge preview shows source and target fields, conflicting contact values, transaction references, consent provenance and reversible mapping plan.
- Authorised merge creates canonical identity link/audit; preserve original IDs and transaction history. Merge is not destructive deletion.

### 2.4 Corporate customers and segments
- Corporate account includes legal/display details, contacts, billing identifiers and credit terms (Finance authorises credit exposure).
- Segmentation builder supports explicit consent/eligibility predicates and preview counts.
- Suppress unsubscribed, bounced, blocked, privacy-restricted and ineligible customers.

### 2.5 Preferences and consent centre
- Store consent purpose, channel, source, wording/version, timestamp, region, evidence and withdrawal.
- Transactional communication and marketing consent are distinct.
- Consent can be withdrawn; suppression applies to future marketing sends and propagates to campaign jobs.
- Respect purpose limitation, retention and data-subject request workflows.

### 2.6 Loyalty programme and points ledger
- Programme, tier rules, earn/burn rules, expiry, reward catalogue, outlet/property eligibility and effective periods.
- Points ledger is append-only: pending earn, confirmed earn, redemption, expiry, reversal/adjustment, with source transaction reference.
- Earn only on eligible completed/settled transaction; pending/failed/refunded transactions follow approved policy.
- Redemption is atomic and protects against concurrent double spend.
- Reversal/refund handling uses compensating points entries; never overwrite balance.

### 2.7 Membership plans
- Plan, benefits, eligibility, subscription interval, renewal, pause/cancel, membership status and member entitlement.
- Membership purchase/payment is handled through payment adapter; membership benefit access is server-validated by effective status.
- Do not imply a membership is active on payment pending.

### 2.8 Campaign builder and audience
- Campaign draft, objective, audience query, channel, message/template, schedule, frequency cap, budget where configured, approval, send, pause, cancel and result.
- Preview recipient count and sample; enforce suppression and consent immediately before dispatch, not only at draft time.
- Avoid exposing full contact list to users without campaign permission.

### 2.9 Promotions and coupons
- Coupon code, validity, usage limit, customer segment, channel/outlet/property, min basket, stacking, budget and redemption rules.
- Atomic redemption and idempotency prevent oversubscription or double use.
- Coupon eligibility is validated in POS/checkout backend, not trusted from CRM UI.

### 2.10 Communication and automation
- Provider adapters for official email/SMS/WhatsApp only where approved and configured.
- Journey builder: trigger, condition, delay, action, exit condition, frequency cap and stop rules.
- Delivery states: queued, sent, delivered, failed, bounced, read where provider supports.
- Retry with idempotency and avoid sending marketing after consent withdrawal.

### 2.11 Feedback, complaints and service recovery
- Collect rating, category, narrative, transaction reference, channel, visibility and consent for public review publication.
- Complaint states: new, triaged, assigned, waiting_customer, resolved, closed/reopened.
- Service recovery offer/compensation requires financial or manager authority; CRM cannot post arbitrary refunds.
- Guest can see customer-safe status, not internal case notes.

### 2.12 Privacy requests and reporting
- Data access/export, correction, deletion/erasure assessment, restriction/objection and consent withdrawal where law requires.
- Deletion workflow must evaluate legal retention and financial/audit references; use pseudonymisation where erasure conflicts with statutory record retention.
- Reports include retention, repeat visits, campaign conversions and loyalty liability subject to approved definitions.

## 3. Acceptance tests
- Two profiles with similar names do not auto-merge.
- Consent withdrawal blocks queued marketing at send time.
- Loyalty earning occurs once and only after eligible transaction state.
- Concurrent redemption cannot spend more points than available.
- Refund creates compensating points entry under configured rule.
- Customer cannot see internal case notes.
- Campaign operator cannot export unrestricted PII without permission.
- Privacy deletion does not erase legally required financial/audit records without approved lawful process.
