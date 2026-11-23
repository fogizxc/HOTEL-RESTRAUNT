# Shared Engine 06 — CRM Identity, Consent, Loyalty and Membership

## 1. Purpose
Provide canonical customer identity and relationship data across hotel stays, restaurant orders, digital portal, loyalty, campaigns and support. Transaction domains retain their transaction facts and reference CRM identity.

## 2. Identity resolution
- Customer profile can originate as guest/anonymous order contact, reservation guest, registered portal account or corporate contact.
- Use verified email/phone/account evidence to link records; store provenance for each match.
- Do not auto-merge on name, approximate address, device ID or other weak signals.
- Provide duplicate review/merge preview, conflict resolution, audit and retained source ID mapping.
- Profile merge must not rewrite order/reservation/folio primary keys.

## 3. Consent ledger
Record purpose (marketing/email/SMS/WhatsApp/profile personalisation), channel, source, timestamp, wording/version, region, actor/subject, evidence reference and withdrawal. Transactional service messages must be distinguished from optional marketing.

At dispatch time, re-evaluate current consent, suppression, bounce/block and frequency caps. Withdrawal invalidates future queued marketing. Provider delivery receipts update delivery state but do not create consent.

## 4. Loyalty ledger
- Immutable point transactions: pending earn, confirmed earn, redemption, expiry, reversal, adjustment, bonus.
- Each transaction references source order/receipt/campaign and rule version.
- Earn only after eligible transaction reaches configured settled/completed state.
- Refund/cancel triggers policy-defined reversal, not mutation.
- Redemption uses atomic balance reservation/debit to prevent concurrent double spend.
- Expiry is a ledger event; balance is projection from transactions.

## 5. Memberships
Membership product includes price/term, entitlement, renewal, grace/cancel/pause policy, property/outlet scope and benefit limitations. Active benefit status is derived from verified payment/contract state; pending payment never grants full benefit unless explicit trial/comp policy exists.

## 6. Campaign and segmentation safety
- Audience queries use approved dimensions and consent predicates.
- Preview count and sample; avoid unrestricted contact export.
- Suppression applied at send time.
- Campaigns require approval based on channel/volume/risk threshold.
- Use provider adapters and idempotent jobs; track queued/sent/delivered/failed/bounced/opt-out.

## 7. Privacy requests
Provide access/export/correction/erasure/restriction/withdrawal workflow subject to governing law. Preserve mandatory financial/audit data as required and use pseudonymisation where full erasure is not lawful. Keep request case, decision, legal basis/reason, scope, completion proof and audit.

## 8. Acceptance tests
- No profile merge based only on similar names.
- Opt-out before send prevents campaign delivery.
- Double webhook/campaign retry does not duplicate loyalty earn.
- Concurrent redemption cannot overdraw points.
- Pending payment does not activate paid membership.
- Guest/customer APIs do not disclose internal CRM notes.
- Erasure workflow handles retention exceptions with logged decision.
