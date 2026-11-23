# Open Decisions Register

This register prevents an implementation agent from silently inventing legal, financial, provider or product policy. Each item needs an owner-approved answer or explicit temporary decision before the affected production capability is enabled.

| ID | Decision | Why it matters | Recommended default until approved | Owner/status |
|---|---|---|---|---|
| ADR-001 | Cloud provider and deployment region | Hosting, data residency, managed services, DR | Vendor-neutral modular deployment; no production provision | Open |
| ADR-002 | Launch countries/legal entities | Tax, privacy, consumer law, guest registration, payroll | Do not enable jurisdiction-specific filings until approved | Open |
| ADR-003 | Payment gateway(s) and merchant account model | Capture, refunds, webhooks, settlements, payouts | Provider adapter; test mode only | Open |
| ADR-004 | Currency policy | Multi-currency, FX, reporting currency | Property currency fixed per transaction snapshot | Open |
| ADR-005 | Tax/invoicing policy | GST/VAT/service charge, invoice numbering, credit notes | Configurable tax engine; no statutory assumptions | Open |
| ADR-006 | Accounting basis and chart of accounts | GL mapping, inventory valuation, revenue recognition | Double-entry design, mapping not posted until approved | Open |
| ADR-007 | Inventory valuation | FIFO vs weighted average, costing treatment | One configured method per legal entity/inventory book | Open |
| ADR-008 | Room QR guest authorisation method | Protect room/folio privacy | QR alone grants no identity; add verified guest/service session | Open |
| ADR-009 | QR quotas and retention | Plan pricing and QR lifecycle | Configurable plan quota; no hardcoded count | Open |
| ADR-010 | Table session timeout and reopening policy | Guest arrival/table turnover | Owner-configurable timeout, staff-controlled closure | Open |
| ADR-011 | Offline POS/KDS capabilities | Network failure and idempotency | Online-only baseline; offline mode only after separate design/test | Open |
| ADR-012 | Guest checkout/account policy | Friction vs identity/loyalty | Guest checkout where lawful; verify contact for sensitive flows | Open |
| ADR-013 | Reservation deposit/cancellation/overbooking | Revenue and guest policy | Explicit rate-plan policy; overbooking disabled by default | Open |
| ADR-014 | Hotel registration/ID document rules | Local hotel law and privacy | Collect only legally required fields; retention decision required | Open |
| ADR-015 | Payroll statutory engine | Tax, social contributions, filings | Jurisdiction-specific reviewed adapter; no invented statutory rates | Open |
| ADR-016 | Biometric device/provider and privacy model | Workforce privacy | Store attendance punches only; avoid raw biometric template storage | Open |
| ADR-017 | Email/SMS/WhatsApp provider | Consent, delivery, cost, sender approval | Adapter contracts; no live dispatch until approved | Open |
| ADR-018 | CRM identity merge policy | Duplicate profile handling | Human-reviewed verified-identity merge; never name-only auto merge | Open |
| ADR-019 | Data retention, erasure and backups | Privacy/legal record keeping | Per-data-class retention schedule required | Open |
| ADR-020 | RPO/RTO and SLO targets | Infrastructure cost and availability | Set from business impact and expected traffic before launch | Open |
| ADR-021 | Support access/impersonation policy | Insider risk and privacy | No impersonation by default; scoped read-only support assist only by approval | Open |
| ADR-022 | Custom domains and branding | DNS validation, TLS, tenant routing | Platform-controlled mapping and domain ownership verification | Open |
| ADR-023 | SaaS plans/prices/trial/grace | Commercial lifecycle | Versioned plan catalogue; exact pricing left unset | Open |
| ADR-024 | Feature/usage quotas | Tenant contract and performance | Configurable quotas per plan version; no fixed global assumptions | Open |
| ADR-025 | Legal document templates and e-signature | Contracts and records | No e-signature claim until provider/legal method approved | Open |

## Decision record format
For each item record: decision ID, decision statement, context, alternatives considered, selected option, consequences, security/privacy impacts, approver, date, implementation phase, review date and evidence. Decisions affecting financial history, identity, privacy or tenant isolation require explicit owner approval.
