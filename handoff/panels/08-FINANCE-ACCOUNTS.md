# Panel 08 — Finance & Accounts

## 1. Ownership and accounting invariants
Finance owns the official accounting ledger, chart of accounts, journals, accounts receivable/payable, receipts, payments, reconciliations, statutory configuration and financial statements. Operational workspaces publish source transactions/events; Finance validates mapping and posts balanced journals. Operational modules must not directly mutate GL.

Use double-entry accounting. Posted journals are immutable; corrections use reversals/adjustments. Every financial record has legal entity, accounting period, currency, source reference, actor, posting date, effective date and audit trail.

## 2. Pages and behaviour

### 2.1 Finance dashboard
- Display cash/bank balances, receivables, payables, revenue, expense, gross margin, reconciliation status and period close checklist.
- Each metric links to report/ledger and shows entity, currency, period, source and freshness.

### 2.2 Legal entities, fiscal years and periods
- Manage legal entities and accounting periods per jurisdiction.
- Period states: open, soft-closed, closed, locked. Posting to closed period requires approved controlled reopen or adjustment period.
- Preserve original accounting date and record date separately.

### 2.3 Chart of accounts
- Account code, name, type, parent, normal balance, control-account flag, currency restrictions, tax mapping and active date.
- Referenced accounts are archived, not deleted; code changes maintain historical mapping.

### 2.4 Journal entries and general ledger
- Manual journal creation with date, entity, lines, account, debit/credit, currency, cost centre, property/outlet, source description and attachments.
- Validate total debit equals total credit in functional currency; validate period and account status.
- Draft → submit → approve → post; maker-checker where threshold or policy requires.
- Prevent self-approval for controlled journals.
- Posted journals immutable; reverse with reason and linked original journal.

### 2.5 Trial balance and reconciliation
- Trial balance by entity, period, account/cost centre, currency.
- Drill down to posted journal lines and source document.
- Reconciliation detects unbalanced/suspense/unmapped/unposted source events; do not silently make balancing entries.

### 2.6 Accounts receivable
- Create invoices/credit notes/receipts against valid customer, corporate account, hotel folio or approved receivable source.
- Allocation supports partial payments and multiple invoices with clear open balance.
- Credit note and refund are distinct: credit note adjusts receivable/invoice; refund moves funds through payment/refund workflow.

### 2.7 Accounts payable
- Supplier bills, debit notes, approval, due dates, tax, PO/GRN match, payment batch and remittance.
- Three-way match exceptions include quantity/price/tax variance and approver resolution.
- Supplier bank details changes use verified change workflow and dual control.

### 2.8 Expenses and claims
- Employee/vendor expense claim, category, cost centre, property/outlet, attachment, policy validation, approval and reimbursement.
- Receipts may include sensitive data; restrict and retain by policy.

### 2.9 Bank and cash reconciliation
- Bank account records provider identifiers and masked details only.
- Import statement through approved adapter; map transactions, match deposits/payments, create reviewed adjustment suggestions.
- Cash/POS shift close: compare expected cash, counted cash, cash movement and approved variance.
- Do not assume gateway capture equals net bank settlement; reconcile gross, fees, refunds, reserves and net settlement separately.

### 2.10 Gateway settlement reconciliation
- Match provider settlement batch to payments/refunds and bank payout.
- Unmatched items go to exception queue; duplicate provider references rejected or linked idempotently.
- Support partial settlements, fees, reserve, chargeback and settlement delay.

### 2.11 Tax configuration and reporting
- Configure tax jurisdictions, registration, tax codes, inclusivity, rates, exemptions, place-of-supply and document rules only after jurisdiction decision.
- Historical transaction tax snapshot cannot change when rates update.
- GST-specific UI/reporting may be activated only after India tax/legal rules and filing integration are confirmed. Do not assume all organisations are in India.

### 2.12 Fixed assets and depreciation
- Asset register, acquisition cost, class, useful life, residual value, depreciation method, location, custodian and disposal.
- Depreciation batch preview → approval → journal posting, idempotent by period and asset.
- Disposal requires proceeds, accumulated depreciation and gain/loss reconciliation.

### 2.13 Budgets, cost centres and approvals
- Define budgets by entity/period/account/cost centre/property and version.
- Variance reports disclose budget version and actual source.
- Approval matrix based on action, amount, entity and role; maker cannot approve own high-risk transaction.

### 2.14 Financial statements and profitability
- P&L, balance sheet, cash flow, outlet/property profitability and cost allocation.
- Every report links back to ledger and states accounting basis, entity, period, currency and allocation methodology.
- Do not include unposted operational estimates in official statements.

### 2.15 Period close and audit
- Checklist includes bank rec, AR/AP, inventory valuation interface, payroll posting, depreciation, tax, inter-entity, suspense, source event completeness.
- Close is guarded by unresolved exceptions; reopen requires elevated approval and reason.
- Retain audit trail for every approval, posting, reversal and export.

## 3. Acceptance tests
- Journal cannot post unless debits equal credits and period is open.
- Posted journal cannot be edited/deleted; reversal creates linked compensating entries.
- Finance can trace a restaurant sale to originating order/bill/payment without creating a second sale.
- Room-service folio posting is not recognised as a second restaurant revenue event.
- Payment capture and bank settlement are reconciled as different events.
- Closed-period posting is denied without authorised reopen/adjustment process.
- Finance report totals reconcile to posted GL for same scope.
- User lacking finance permissions cannot view bank account or supplier payment details.
