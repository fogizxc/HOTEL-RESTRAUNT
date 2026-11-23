# Panel 11 — Reports & Business Intelligence

## 1. Ownership and read-only rule
BI is a read-oriented analytical layer. It does not become the source of truth for reservations, orders, stock, employees, GL or CRM ledgers. Any corrective action links to the owning workspace. Analytics data must expose metric definition, scope, filters, freshness and lineage.

## 2. Pages and behaviour

### 2.1 Executive dashboard
- Organisation and property comparison with revenue, operating margin, occupancy, ADR/RevPAR, restaurant sales, covers, labour cost, inventory/waste and guest metrics where data is available.
- Allow period, property, outlet, legal entity and currency selection according to permission.
- Comparisons must normalise date ranges/timezones and disclose FX conversion method if multi-currency is enabled.

### 2.2 Property comparison
- Compare selected properties/outlets on consistent metrics and periods.
- Exclude inaccessible properties from selector and API results.
- Highlight missing/stale data rather than silently treating it as zero.

### 2.3 Hotel analytics
- Occupancy, room nights, ADR, RevPAR, booking pace, channel/source mix, cancellation/no-show, length of stay, rate-plan performance and guest-request SLA.
- Define occupancy denominator and treatment of out-of-order rooms.

### 2.4 Restaurant analytics
- Gross/net sales, discounts, tax, covers, average check, item/category performance, table utilisation, channel, void/refund, prep time and menu engineering.
- Clearly distinguish item sales, tax, service charge, tips and collected payment from recognised net revenue.

### 2.5 Finance analytics
- P&L/BS/CF, cash, receivable/payable aging, budget variance, profitability and settlement reconciliation.
- Official financial reports query posted Finance records only; draft operational estimates must be separately labelled.

### 2.6 Inventory/procurement analytics
- Stock valuation, consumption, purchase price variance, supplier lead time, stockout, expiry/waste, recipe cost and variance.
- State valuation basis and data snapshot time.

### 2.7 Workforce analytics
- Headcount, attendance, overtime, payroll cost, leave, turnover and coverage.
- Apply access restrictions and small-group suppression to avoid exposing individual employee information.

### 2.8 CRM/marketing analytics
- New/returning guests, repeat rate, retention, loyalty liability, membership, campaign delivery/conversion and consentable audience.
- Attribution windows and identity deduplication rules must be visible.

### 2.9 Housekeeping/maintenance analytics
- Cleaning duration, room-ready SLA, re-clean rate, preventive maintenance completion, open work orders, MTTR and downtime.

### 2.10 Custom report builder
- Step 1: select approved dataset/domain.
- Step 2: choose dimensions and metrics from metric catalogue.
- Step 3: configure date range, filters and grouping.
- Step 4: select table/chart visualization and sort/limit.
- Step 5: preview sampled/aggregated output.
- Step 6: save private/shared report with explicit audience and permission scope.
- Do not allow raw SQL or arbitrary data-source access to general users.

### 2.11 Report library and saved dashboards
- Search, filter, pin, clone and archive reports according to owner permissions.
- Shared report permissions are rechecked at execution time; sharing a report does not grant access to its underlying data.

### 2.12 Forecasts and scenarios
- Forecast pages disclose model version, input dates, assumptions, confidence interval/error where available and training cutoff.
- Scenario edits are hypothetical and never write operational changes.

### 2.13 Scheduled reports and exports
- Configure schedule, recipient, format, filters and delivery channel.
- Recipients are authorised at send time; export links are short-lived, access-controlled and audited.
- Exports avoid PII unless necessary and approved.

### 2.14 Data freshness, quality and metric dictionary
- Show source refresh timestamps, late/missing event counts, reconciliation gaps and definition version.
- Metric dictionary: name, plain definition, formula, grain, timezone, currency, source domain, exclusions and owner.

## 3. Acceptance tests
- BI user cannot query a property outside assigned scope by editing request parameters.
- Financial statements use posted GL and reconcile to Finance report source.
- Missing data is not presented as zero without an explicit rule.
- Shared reports recheck viewer permissions when opened/exported.
- Scheduled report recipient access is revalidated at send time.
- Custom builder only exposes approved fields and datasets.
- Forecast output is labelled hypothetical and never mutates source systems.
