---
name: Optimize a portfolio
description: Submit accounts and strategies to Moment's optimizer, poll for status, and retrieve the generated orders.
api: Moment Fixed Income API
operations: [postoptimization-1, getoptimizationstatus, getoptimizationsorders, postpreview, postportfoliostats]
---

# Optimize a portfolio

Use this skill to generate optimized fixed-income portfolios for managed
accounts with Moment's optimization engine.

## Auth & conventions
- Authenticate with your Moment API key.
- Optimization is asynchronous: submit, then poll status until terminal.
- Status values: `queued`, `running`, `success`, `failed`, `unknown`. A missing
  optimization id returns `error_type: not_found`.

## Steps
1. **(Optional) Preview** — `postpreview` for a single account/strategy to get a
   synchronous preview of expected orders (uses cached market data; the `Trade`
   constraint is not enforced).
2. **Submit** — `postoptimization-1` with the set of accounts and their
   strategies. Capture the returned optimization id.
3. **Poll status** — `getoptimizationstatus` until `success` or `failed`.
4. **Retrieve orders** — `getoptimizationsorders` to read the orders the
   optimization generated.
5. **Check portfolio stats** — `postportfoliostats` to compute aggregate
   statistics (average YTW, duration, weights by rating/sector, total return)
   for the resulting holdings.

## Notes
Generated orders can then be placed and tracked with the
"Place and track a bond order" skill. Test against Prod Paper (UAT) first.
