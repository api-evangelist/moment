---
name: Look up and price a bond
description: Resolve a bond, read its reference data, and get live pricing, order-book, and yield analytics from Moment's Fixed Income API.
api: Moment Fixed Income API
operations: [listinstruments, retrieveinstrument, getmarks, gettopoforderbook-1, gettradeprice-1, pricetoyield-1]
---

# Look up and price a bond

Use this skill to find a fixed-income security in Moment's universe and pull the
reference data, live market, and analytics an agent needs to quote it.

## Auth & conventions
- Authenticate with your Moment API key (organization is resolved from the key).
- Securities are keyed on **ISIN**; most endpoints auto-detect CUSIP/ticker and
  resolve to the ISIN.
- On error, branch on `error_type` (`invalid_request`, `not_found`, `closed`,
  `rate_limited`, `server_error`) — not the human-readable `error` string. On
  `rate_limited`, honor `Retry-After` and `X-RateLimit-*` headers.

## Steps
1. **Find the instrument** — `listinstruments` with filters (issuer, type,
   rating, maturity), or `retrieveinstrument` if you already have the ISIN.
2. **Read the latest mark** — `getmarks` for up to 100 ISINs at once to mark the
   position (clean/dirty price, accrued interest, YTM, YTW).
3. **Read the live market** — `gettopoforderbook-1` for the best bid/offer with
   trade sizes and yields.
4. **Cost the trade** — `gettradeprice-1` for principal, accrued interest, fees,
   and total trade cost at a size, price, and risk group.
5. **Convert price/yield** — `pricetoyield-1` to compute YTM and YTW for a
   candidate price.

## Notes
Prices and yields are fee/markup-adjusted per your organization's risk group.
Test the full flow against Prod Paper (UAT) mock instruments first
(see `sandbox/moment-sandbox.yml`).
