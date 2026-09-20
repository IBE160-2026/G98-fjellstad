---
title: "Addendum: Power Market Insight Platform"
status: draft
created: 2026-09-20
updated: 2026-09-20
---

# Addendum: Power Market Insight Platform

Supporting technical depth captured during the initial brief conversation. This is source material for downstream PRD/architecture work, not brief content itself.

## Element 1 — Short-term fundamental forecast: methodology detail

- **Baseline curve selection.** Uses historical aggregated bid curves published by Nord Pool and other Nordic NEMOs as the starting point. A dedicated technique is needed to choose *which* historical baseline curve lead time to use as the reference point (e.g. how far back to anchor), including identifying patterns that should drive that choice (this is called out explicitly as a technique to be developed and researched, not a solved/off-the-shelf method).
- **Fundamentals adjustment.** From the chosen baseline, the curve is shifted forward to account for expected changes (sourced from third-party fundamentals vendors) since the baseline was struck, across:
  - Wind power production
  - Solar production
  - Run-of-river hydro production
  - Must-run/reserve power
  - Water values (reservoir hydro) — including *changes* in water values, not just levels
  - Availability across hydro, grid, nuclear, thermal, and other production types
  - Thermal short-run marginal costs (SRMCs)
  - Net export flows
- **Water value regression technique.** A regression-based approach is planned specifically to derive water value *changes* from observable data — called out as a special technique requiring its own development effort, distinct from simply sourcing a vendor's water value number.

## Element 2 — Forward-period statistical model: detail

- Targets standard forward products: DEC (December baseload-style monthly contract), Q4, CAL27, and similarly structured contracts further out.
- Explanatory variables: inflow forecasts, wind forecasts, availabilities, and fuel/carbon prices (gas, coal, EUA).
- Framed as a statistical (not fundamentals-simulation) model — i.e., it estimates price *change* as a function of driver *change*, rather than re-deriving a full supply/demand curve for forward horizons the way Element 1 does for the short term.

## Financial market benchmarking (applies to both elements)

Both Element 1 and Element 2 outputs are to be compared against what is implied by financial market contracts — explicitly both short-term and long-term instruments — so the platform functions as a check on the model versus a check on the market, in both directions.

## Later-phase: area price expansion (explicitly deferred past v1)

Expanding beyond the single Nordic system price to area-level prices, and beyond the Nordic market to wider Europe, was identified as requiring materially different techniques, not just "more of the same":

- **Flow-based clearing.** Wider European markets (and increasingly Nordic area pricing) are cleared via flow-based market coupling rather than a single aggregated curve, which is a different clearing mechanism than the system-price NTC-style approach Element 1 is built on.
- **Curve disaggregation.** A technique is needed to dissolve the aggregated (system-level) bid curves into area-level curves.
- **Bid-to-area attribution.** Within that disaggregation, individual bids need to be identified/attributed to the area they belong to — this matters specifically because fundamentals adjustments (e.g. a water value change) need to be applied to the *correct area's* bids, not spread uniformly across the system curve.

This is scoped as a distinct, later research and development effort, not an incremental extension of Element 1's method.

## Possible Element 3: news synthesizer (explicitly "possible," not committed)

A tool that explains observed/forecast price changes by combining two inputs: (1) news text/events, and (2) the same fundamentals data changes already feeding Elements 1 and 2. Positioned as a possible third element, later-phase, contingent on the first two elements working.

## Open items flagged for the revision pass

- User was not able to elaborate on success criteria at brief time — placeholder criteria in `brief.md` are `[ASSUMPTION]`-tagged and need explicit sign-off or replacement.
- No tech stack decision beyond "full stack" — language/framework, data storage for bid curve history, and hosting are all undecided.
- No named data vendor(s) for fundamentals feeds (wind/solar forecasts, hydro inflow, fuel/carbon prices) — likely a real constraint (cost, access, licensing) for a course project that a professional employer's data subscriptions would normally cover.
- Group task split / who owns which element is undecided.
- The "professional colleagues in the industry" user context implies data licensing and confidentiality constraints may exist around bid curve and vendor fundamentals data that haven't been discussed — worth raising before architecture, since it could affect what can actually be built/deployed/shared as a course project versus what stays conceptual.
