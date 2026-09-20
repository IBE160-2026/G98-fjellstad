---
title: "Product Brief: Power Market Insight Platform"
status: draft
created: 2026-09-20
updated: 2026-09-20
---

# Product Brief: Power Market Insight Platform

## Executive Summary

Power market prices are notoriously hard to explain in real time. Traders and analysts watch the Nordic system price move and are left reconstructing *why* after the fact, stitching together bid curve data, weather and hydro balance updates, and forward curve moves from separate tools and vendor feeds. The Power Market Insight Platform is a web-based dashboard that hosts a growing suite of self-developed analytical tools aimed at making price formation in the Nordic power market (and eventually wider Europe) transparent and forecastable, rather than something to be reverse-engineered after the fact.

The platform's anchor capability is a **short-term fundamental price forecast for the Nordic system price**, built top-down from Nord Pool and other NEMOs' published historical aggregated bid curves, adjusted forward using changes in the underlying fundamentals (wind, solar, run-of-river hydro, must-run generation, water values, thermal short-run marginal costs, availabilities, and net export flows) since the baseline curve was observed. Alongside it sits a **statistical model for forward-period price expectations** (e.g. DEC, Q4, CAL27) driven by changes in inflow, wind, availability, and fuel/EUA forecasts. Both are benchmarked continuously against what the financial forward market is already pricing in, turning the platform into both a forecasting tool and a market-expectations sanity check.

This is being built as a group project for IBE160 (Programming with AI, Høgskolen i Molde, autumn 2026), but the design bar is a real one: it is built to be genuinely useful to the author and professional colleagues in trading and hedging roles, not a toy demo. The course delivery will necessarily be a scoped-down first slice of a much larger vision.

## The Problem

Nordic system price movements are driven by a large, fast-moving set of fundamentals (hydro balance and water values, wind and solar output, thermal costs, cross-border flows) that interact through an auction-based clearing mechanism (aggregated bid curves) that is not straightforward to reconstruct or adjust in real time. Today, a trader or analyst wanting to understand or anticipate a price move has to:

- Pull historical aggregated bid curve data from Nord Pool/NEMOs manually.
- Separately track fundamentals data from multiple vendors (wind/solar output and forecasts, hydro inflow and reservoir levels, thermal fuel and carbon costs, availability data).
- Mentally (or in ad hoc spreadsheets) combine the two to estimate how the curve — and therefore the clearing price — has shifted since the last observed baseline.
- Do this all over again, with different techniques and a different set of drivers, for anything beyond the prompt/day-ahead horizon (months, quarters, calendar years out).
- Have no fast way to check whether their own view is already priced into the financial forward market.

The cost of the status quo is slower, less rigorous, and less repeatable price-formation analysis — reasoning that lives in individual analysts' heads and spreadsheets rather than in a consistent, auditable model.

## The Solution

A single web platform that houses purpose-built, connected tools rather than one monolithic model:

1. **Short-term fundamental forecast (Nordic system price).** Takes the most recently published aggregated bid curve as a baseline and adjusts it forward using observed/forecast changes in fundamentals since that baseline was struck. Key novel techniques to be developed: a method for selecting which historical baseline curve lead time to use (and the patterns that drive that choice), and a regression-based approach for deriving water value changes from observable market and hydrological data.
2. **Forward-period statistical model.** Estimates expected price changes for standard forward products (DEC, Q4, CAL27, etc.) as a function of changes in key explanatory variables: inflow forecasts, wind forecasts, availability, and fuel/carbon (gas, coal, EUA) prices.
3. **Market-expectation benchmarking.** Both models' outputs are compared against what is implied by traded financial contracts (short-term and long-term), surfacing where the model's view and the market's view diverge.

Both elements are additive to a single dashboard experience, versioned and comparable over time, so a user can see not just "what is the forecast" but "how has the forecast and its drivers evolved."

## What Makes This Different

The differentiation is methodological, not infrastructural: the platform is not a generic price-viewer or a black-box ML forecaster, it encodes the author's own top-down, fundamentals-driven approach to price formation — including bespoke techniques (baseline lead-time selection, regression-derived water value changes) that are not off-the-shelf. The moat, such as it is at this stage, is domain expertise translated into a repeatable tool, plus the explicit discipline of always checking model output against what the financial market already believes. No fabricated technical or data moat is claimed — vendor fundamentals data and Nord Pool/NEMO bid curve publications are third-party inputs available to any well-resourced market participant.

## Who This Serves

**Primary users:** the author and professional colleagues in the power trading/hedging industry — people who need a fast, defensible view of *why* the Nordic system price is where it is and where it is likely headed, across both short-term (day-ahead/near-term) and forward (monthly to calendar-year) horizons. Success for this user looks like: trusting the platform's fundamental adjustment logic enough to use it as an input to real trading and hedging decisions, and being able to explain a price move by pointing at specific driver changes rather than a black box.

**Secondary context:** this is also a graded group deliverable for IBE160 at Høgskolen i Molde. `[ASSUMPTION]` The course-facing success bar (working demo, documented methodology, defensible use of AI-assisted development) is a subset of, and compatible with, the professional-usefulness bar above — the course version is a real first slice of the product, not a separate throwaway.

## Success Criteria

`[ASSUMPTION]` — not yet validated with the user/group; revisit at first revision pass:

- The short-term fundamental forecast (Element 1) runs end-to-end on real historical Nordic bid curve data and produces a system price forecast that is directionally defensible against realized outcomes for a backtest window.
- The platform clearly shows *which* fundamentals drove a given forecast change (attribution), not just the forecast number itself.
- Forecast output (both Element 1 and Element 2) is shown alongside the corresponding financial forward market price for comparison.
- The dashboard is usable enough that the author would actually open it to check the Nordic price view during a real workday.
- Course deliverable: the IBE160 grading criteria for the group's chosen scope are met (methodology documentation, working software, evidence of AI-assisted development practice).

## Scope

**In for v1 (course delivery):**
- Dashboard/web app shell that can host multiple tools.
- Element 1: short-term fundamental forecast for the Nordic system price, using historical aggregated bid curves + fundamentals adjustment, including the baseline-lead-time-selection and water-value-regression techniques.
- Comparison of Element 1's output against short-term financial market pricing.

**Explicitly out of v1 (later phases, in the user's own stated order of expansion):**
- Element 2: the forward-period (DEC/Q4/CAL) statistical model — planned as the next build slice after Element 1 is working.
- Expansion beyond the Nordic system price to area prices, which requires flow-based clearing and techniques to dissolve aggregated bid curves into area-level curves (including attributing individual bids to areas so fundamentals changes, e.g. water values, can be assigned correctly).
- Expansion beyond the Nordic market to the wider European market.
- The news synthesizer (Element 3, "possible") that explains price changes using both news text and fundamental model observations.

**Not yet decided (flagged for the revision pass):** target tech stack specifics beyond "full stack"; which vendor(s) supply the fundamentals data feeds; hosting/deployment approach; team task split.

## Vision

If this succeeds, it grows from a Nordic system-price tool into a full European power market insight platform: short-term and forward-looking price formation explained, area by area, across the interconnected European market, with model output continuously reconciled against what the financial market is pricing in. The news synthesizer layer turns it from a quantitative tool into a narrative one — able to say not just "the forecast moved," but "here is why, in plain language, backed by the same fundamentals data driving the model." The long-term ambition is a tool the author and colleagues rely on daily for trading and hedging decisions, not a course artifact that stops being used after grading.
