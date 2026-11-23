# Panel 06 — Housekeeping & Maintenance

## 1. Ownership and boundaries
This workspace owns room-cleaning tasks, inspections, housekeeping execution, maintenance work orders, asset maintenance schedules, linen/laundry workflows and property service task assignment. PMS owns reservation and occupancy status. Room readiness is a derived state from occupancy policy, cleaning/inspection and maintenance constraints; no single user action should falsify another domain's state.

## 2. Pages and behaviour

### 2.1 Operations dashboard and room board
- Filter by property, floor, building, room type, cleaner, task priority, occupancy and physical condition.
- Show separate badges/fields for occupancy and physical room condition.
- Status examples: occupied/vacant/reserved are occupancy; dirty/clean/inspected/maintenance/out-of-order are physical condition.
- Show last status timestamp and source task.

### 2.2 Task board and assignment
- Create individual, recurring or bulk cleaning tasks from checkout, stay-over schedule, manual request or configured trigger.
- Assign employee/team, due time, priority, room/area, checklist and source reference.
- Prevent assigning the same exclusive task to multiple owners unless explicitly configured as team task.
- Bulk actions preview exact affected rooms and require confirmation.

### 2.3 Housekeeper mobile task view
- Mobile-first task list with assigned, accepted, in-progress, blocked, completed states.
- Checklist items, safe notes, supplies used, issue reporting and completion action.
- Completion records actor, timestamps and checklist version; cannot mark inspected unless employee has inspector role.
- Offline capture, if implemented, is marked pending sync and reconciles conflicts; never show unsynced action as final room-ready state.

### 2.4 Inspection and room readiness
- Supervisor inspection with pass/fail by checklist item, photo evidence where policy permits, failure reason and re-clean task.
- Room becomes `inspected/ready` only when required cleaning and inspection pass and no blocking maintenance work order exists.
- PMS consumes readiness event; housekeeping cannot check in guest or change occupancy.

### 2.5 Public areas and guest service requests
- Public area task templates and schedules for lobby, washrooms, pool, corridors, meeting areas.
- Guest request categories: towels, amenities, cleaning, late service, room issue.
- Guest request intake creates a task linked to stay/room; internal notes are not exposed to guest portal.
- Show acknowledgement and request status; close requires resolution outcome.

### 2.6 Maintenance request and work order
- Log issue with asset/room/location, category, severity, description, evidence, reported by, safety impact and downtime.
- Triage, assign technician, estimate, schedule, parts used, work notes, repair/test, close and reopen.
- High-risk safety issue can block room readiness or asset use.
- Work order states: `reported`, `triaged`, `assigned`, `in_progress`, `waiting_parts`, `completed_pending_verification`, `closed`, `cancelled`.
- Closure requires verification for configured asset/severity category.

### 2.7 Preventive maintenance and assets
- Asset register with unique property-scoped asset code, location, model/serial (sensitive), warranty, vendor, criticality, service schedule and history.
- Preventive schedule by time or usage; generate idempotent tasks; missed service alerts.
- Decommissioning requires status and retention/audit; historical work orders remain linked.

### 2.8 Linen, laundry and supplies
- Record linen stock movement, laundry batch, clean/dirty count, vendor handoff, loss and discard; inventory ledger owns stock quantity where integrated.
- Housekeeping may record usage/consumption events; inventory validates/resolves stock ledger.

### 2.9 Lost & found and incidents
- Lost item record includes location, found date, custodian, secure description, evidence and chain-of-custody events.
- Incident record has restricted visibility, severity, involved records, investigation and closure; personal/security incident details hidden from general staff.

### 2.10 Team, shift handover and analytics
- Team roster/shift view sourced from HR schedules; show task load and skill/role eligibility.
- Handover captures incomplete task, room blockers, safety information and acknowledging actor.
- Analytics: cleaning duration, re-clean rate, room readiness SLA, maintenance MTTR, preventive completion, linen variance.

## 3. Acceptance tests
- Room cannot become ready while a blocking maintenance work order remains open.
- A cleaner completing a task does not bypass required inspection.
- Housekeeping cannot alter reservation dates or occupancy state.
- Guest sees only their own requests and customer-safe notes.
- Duplicate checkout event creates one cleaning task.
- Maintenance work order closure retains immutable history and verifier when required.
- Staff without incident permission cannot view restricted incident details.
