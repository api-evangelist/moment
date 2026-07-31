---
name: Place and track a bond order
description: Submit an order for an account and follow it through the execution lifecycle using Moment's Orders API and real-time events.
api: Moment Fixed Income API
operations: [postorder-1, getorderbyorderid-1, getallorders-1, deleteorderbyorderid-1, getevents-1, allocateorderbyorderid]
---

# Place and track a bond order

Use this skill to place a fixed-income order for an account and monitor it
through Moment's Execution Management System.

## Auth & conventions
- Authenticate with your Moment API key; the caller's organization and access
  are resolved from the key.
- The order lifecycle is asynchronous. Poll `getevents-1` (covers the last two
  days) or subscribe to the **Real-time Orders API** WebSocket
  (`wss://moment-api.com/v{X}/...`, see `asyncapi/moment-realtime-webhooks.yml`)
  for `order_update` / `system_update` events.
- Handle `error_type` programmatically; respect `Retry-After` on `rate_limited`.

## Steps
1. **Place the order** — `postorder-1` with the account, ISIN, side, size, and
   (for limit orders) price. Capture the returned `order_id`.
2. **Confirm acceptance** — `getorderbyorderid-1`, or watch the events stream for
   the acknowledgement.
3. **Track fills** — `getevents-1` or the real-time stream for partial/full
   fills, rejects, and cancels.
4. **Cancel if needed** — `deleteorderbyorderid-1`; a late cancel may return a
   cancel-reject event asynchronously.
5. **List/reconcile** — `getallorders-1` filtered by account/state.
6. **Allocate a block** — `allocateorderbyorderid` to split a filled block order
   across customer accounts.

## Notes
Validate order lifecycles against Prod Paper (UAT) deterministic mock CUSIPs
(e.g. `912810SX7` = fill after 1s, `912810UK2` = unsolicited venue cancel) in
`sandbox/moment-sandbox.yml` before trading live.
