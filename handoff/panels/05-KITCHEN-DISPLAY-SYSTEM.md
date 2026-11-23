# Panel 05 — Kitchen Display System (KDS)

## 1. Purpose and boundaries
KDS is a fast operational execution workspace for kitchen teams. It consumes accepted KOTs from Restaurant POS and owns kitchen ticket/item preparation state. It does not own menu price, customer bill, payment, inventory ledger or restaurant order acceptance.

## 2. Pages and behaviour

### 2.1 Live kitchen board
- Display active tickets in configurable columns: new, accepted, preparing, ready/pass, overdue; exact columns are configurable but state semantics remain fixed.
- Group by station, outlet, order channel, table/pickup reference and course.
- Each ticket shows elapsed time, target preparation time, priority, item count, allergy/dietary warnings, modifiers and customer notes that are operationally relevant.
- Colour is supplementary; show textual status and time.
- Automatically refresh via websocket/SSE or polling fallback; show connection status and last update time.

### 2.2 My Station
- Authenticated cook sees tickets/items routed to assigned station(s), with only permitted cross-station view.
- Actions: acknowledge, start item, pause with reason, mark ready, report unavailable, request expo attention.
- Item transition includes actor, station, time and optimistic version.
- Do not allow one station to mark another station's item ready unless role/config allows.

### 2.3 All Stations and station management
- Supervisor can inspect all station queues and reassign eligible items.
- Station configuration: name, outlet, item routing rules, device/display assignment, active state, fallback station, prep SLA.
- Routing changes are versioned and apply to new tickets by default; active tickets retain routing snapshot unless supervisor explicitly migrates with audit.

### 2.4 Ticket and item detail
- Ticket shows order reference, item details, modifiers, notes, fire/course timing, table/order channel and KOT revision.
- Item-level status must support one ticket containing items at different preparation stages.
- Cancelled/modified items are visibly struck/labelled with reason and event time; never silently disappear.
- Ticket updates create revision/event history rather than overwriting the previous state.

### 2.5 Expo/pass screen
- Consolidate ready items across stations for the same order/course.
- Show incomplete item count and hold/release controls.
- Mark course/order ready only according to configured gating rule; one station cannot imply whole order is ready unless all required items qualify.
- Handover to waiter/pickup is a separate event from kitchen preparation completion.

### 2.6 Delays, escalation and 86/unavailable item
- Overdue tickets are highlighted by elapsed vs target prep time.
- Escalate based on configurable thresholds to kitchen lead/manager; suppress duplicate alerts.
- Item unavailable action creates an availability event back to POS/Menu and may trigger customer communication/refund decision outside KDS.
- KDS cannot refund or alter customer bill.

### 2.7 Cancel, recall and re-fire
- Cancellation requires a source order event or authorised supervisor command; include reason and acknowledgement.
- Re-fire/reopen is a new ticket revision linked to original, not an accidental duplicate sale.
- Idempotency key prevents repeated KOT processing on retries.

### 2.8 Devices, shifts and performance
- Device setup includes station binding, display mode, sound preference, heartbeat and reconnect state; no secrets displayed.
- Shift/team view displays assigned cook/team where permitted.
- Performance: prep times by station/item/period, overdue rates, ticket throughput, re-fire/cancel counts; metric definition and source freshness required.

## 3. Acceptance tests
- Duplicate POS outbox event creates one logical KOT ticket.
- Item assigned to Station A does not appear in another tenant/outlet's station feed.
- A cook without supervisor permission cannot reassign station routing.
- Marking one item ready does not mark the entire multi-item order ready.
- Cancelled ticket remains visible in history with reason.
- Offline display reconnect does not duplicate item state transitions.
- Stale concurrent action receives conflict and refreshes authoritative ticket state.
