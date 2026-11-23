# 00 — Product Scope and Glossary

## Product definition
HospitalityOS is a cloud-only multi-tenant SaaS for hotel and restaurant operations. Tenant means the customer organisation, not one property. A tenant can buy Hotel Management, Restaurant Management or both. A tenant owns multiple properties; each property may contain multiple restaurant outlets and service kitchens.

Illustrative tenant: Royal Hospitality Group — Royal Grand Hotel Meerut; Royal Business Hotel Delhi; Royal Fine Dine Noida; Royal Rooftop Restaurant Meerut. This is sample context, not seeded real data.

## Business configurations
- Hotel-only organisation: hotel workflows and shared services; no restaurant-only screens unless outlet exists.
- Restaurant-only organisation: restaurant workflows; no hotel reservations/room folios.
- Combined organisation: hotel and restaurant in one tenant, with property/outlet boundaries.
- Multi-property: org-level analytics only for authorised locations.
- Multi-outlet: each outlet belongs to one property and tenant; quotas are entitlement-controlled.

## Glossary
| Term | Meaning |
|---|---|
| Tenant / organisation | SaaS customer boundary and primary data-isolation scope. |
| Property | A hotel, restaurant or mixed hospitality site owned by one tenant. |
| Outlet | A restaurant/food-service outlet within a property. |
| Workspace | Role/domain-oriented UI surface. |
| Module | Subscription-controlled product area, e.g. Hotel Management. |
| Feature entitlement | Individual capability/limit inside a module. |
| Platform override | Super Admin allow/deny/limit independent of plan. |
| Tenant configuration | Owner-managed settings constrained by platform/plan. |
| Employee permission | User-level action permission scoped to tenant/property/outlet/department. |
| Table session | Active shared dining context; contains separate guest orders. |
| QR destination | Physical target such as table, room, poolside or banquet location. |
| Guest folio | Stay-linked hotel account of charges/payments. |
| KOT | Kitchen Order Ticket for kitchen preparation. |
| GL | Official general ledger, owned by Finance. |
| Inventory ledger | Immutable stock movements, owned by Inventory. |
| Outbox | Event record committed atomically with a domain change. |

## Product invariants
1. No cross-tenant access.
2. Every property/outlet operation validates scope.
3. Entitlement, tenant configuration and employee permission are independent.
4. Each domain has one authoritative owner.
5. No floating-point monetary arithmetic.
6. Stock and accounting changes are ledger-backed and auditable.
7. Payment success is provider/server verified, never inferred from browser redirect.
8. QR tokens are opaque, high-entropy and revocable; a QR does not identify a guest.
9. Multiple table guests can place separate orders in one table session without seeing each other's private data.
10. Room-folio charge requires active eligible stay plus authorised guest/service session.
11. BI is read-only relative to operational/finance source domains.
12. A prototype does not prove backend functionality.

## Scope exclusions unless formally approved
- One separate database or microservice per panel.
- Public access to staff workspaces.
- Customer access to internal POS/KDS/finance/HR data.
- Hardcoded plan prices, taxes, payroll rules or cancellation terms.
- Offline financial posting without a conflict/settlement design.
- Generic future QR destination types without corresponding workflows.
