---
title: "Product Brief: Power Market Insight Platform"
status: draft
created: 2026-09-27
updated: 2026-09-28
---

# Product Brief: Power Market Insight Platform

## Executive Summary

Nordic power prices move on a dense, fast-changing set of fundamentals — hydro balance and water values, wind and solar output, thermal costs, cross-border flows, grid availability — cleared through an auction mechanism that most commercially available forecasts approximate by rebuilding supply and demand from the bottom up. This platform takes the opposite route: it starts from a *realised* historical bid curve, published by Nord Pool, and adjusts it forward for what has changed since. That top-down methodology is the experimental core of the whole project.

The platform is a web dashboard built around three purpose-specific tools. **Module 1** produces a short-term fundamental forecast for the Nordic system price, expressed not as a single number but as a distribution across a weather forecast ensemble (primarily the ECMWF 15-day ensemble, with room to incorporate other weather models and horizons later), compared against what the financial derivatives market is already pricing for the same short-term contracts: once the forecast's skill and reliability are tested and verified, a persistent divergence from market pricing is treated as a signal of exploitable mispricing, not just an unexplained discrepancy. **Module 2** is a rolling statistical model that explains how the SYS forward price should move — from the W+3 weekly contract out through months, quarters, and calendar years — as fundamentals-driving variables change, and tracks how its own sensitivities evolve over time. **Module 3** is a grid and availability module: a Nordic transmission and production-asset map with a scrubbable availability timeline, paired with an AI assistant that helps answer questions about grid bottlenecks and Flow-Based Market Coupling.

This is being built solo, as a group-project deliverable for IBE160 (Programming with AI, Høgskolen i Molde, autumn 2026, hand-in mid-December), but the ambition is not to satisfy a minimum grading bar. The floor is a methodologically functioning Module 1 forecast — genuinely implementing the top-down approach, validated in-sample and out-of-sample, not just a feasibility sketch. Above that floor, the intent is to keep improving it for as long as the semester allows, with the realistic best case being a tool good enough to actually use professionally, not just to submit.

## The Problem

Understanding *why* the Nordic system price moved — or anticipating how it will — currently means manually stitching together sources that don't talk to each other: historical aggregated bid curve data from Nord Pool, fundamentals data from vendors (wind/solar output and forecasts, hydro inflow and reservoir levels, thermal fuel and carbon costs, availability and revision/UMM data), and a mental model of how each of those shifts the supply or demand curve. Doing this rigorously, consistently, and fast enough to be useful for trading or hedging decisions is hard.

Several commercially available fundamental forecasts already do a reasonable job of this — the problem isn't that they're built bottom-up, which is most likely done for good reason. The problem is that they're resource-heavy, complex, and run externally: that limits how well the author can actually understand *why* a given forecast says what it says, and makes them impractical to re-run in real time as new information arrives intraday, rather than on the vendor's own schedule.

The same problem repeats at longer horizons — explaining forward price moves (W+3 through CAL contracts) requires tracking a different set of explanatory variables (inflow, temperature, availabilities, fuel/EUA prices) and understanding how sensitive the price actually is to each, which shifts over time and is rarely made visible. And a third layer sits underneath both: area-level pricing and grid bottlenecks under Flow-Based Market Coupling are hard to reason about without a live view of network and production-asset availability.

The cost of the status quo: slower, less rigorous, less repeatable analysis than a lightweight, internally-owned, real-time-rerunnable tool could provide, with no fast way to check whether a view is already priced into the financial market.

## The Solution

A single dashboard hosting three connected, purpose-built tools:

**Module 1 — Short-term SYS forecast.** A fundamental Nordic system price forecast with weather outcome space primarily across the ECMWF (EC) 15-day forecast ensemble — one forecast per weather ensemble member, not a single point estimate — with room to incorporate other weather forecast models, potentially with different horizons, later. Built top-down: take the historical aggregated bid curve (sourced via the Nord Pool API) as a reference state, then adjust the supply and demand curves for what has changed since, using fundamentals data sourced primarily from the Volue API, with unavailability data from the dedicated NUCS API (see addendum for the full source list). Output is compared against the price expectations implied by short-term financial derivatives (daily and weekly contracts, up to two weeks forward). This top-down approach is the project's central methodological bet: rather than rebuilding supply and demand from assumptions the way most commercial forecasts do, it starts from what the market actually cleared and adjusts from there — a lighter-weight approach the author can build, run, and re-run internally, including in real time as new information arrives.

**Module 2 — Mid-term statistical model.** A rolling statistical model over recent history — with user-configurable history length, explanatory variables, and a recency-decay weighting — that explains SYS forward price *changes* for tradable contracts from the W+3 weekly contract through months, quarters, and calendar years. It serves two purposes: producing sensitivities usable out-of-sample to estimate expected price moves as new information arrives (compared live against actual market moves), and visualising how each coefficient evolves through time per contract — making the market's changing sensitivity to each driver visible instead of implicit.

**Module 3 — Grid & availability module.** A Nordic geographic map of transmission network elements and production assets, with availability shown on a timeline the user can scrub through, to build intuition for where grid bottlenecks are likely and how they relate to area-level pricing under Flow-Based Market Coupling. An AI-powered assistant answers user questions about bottlenecks and FBMC dynamics.

All three modules share one dashboard, and Modules 1 and 2 are compared against what the financial market already believes. This is not a scoring benchmark the model is judged against — it's the trading value proposition: a forecast that is genuinely skillful and reliable should, over time, sometimes disagree with the market, and in most cases that persistent disagreement is a sign the market is mispriced. That only holds once skill has actually been tested and verified against realised outcomes, in-sample and out-of-sample — until then, divergence is diagnostic, a prompt to investigate the model, not something to act on.

## What Makes This Different

The differentiator is a specific, named methodological bet, not a vague domain-expertise claim: building the short-term forecast top-down from a realised historical bid curve and adjusting it for fundamentals change, rather than rebuilding supply and demand from assumptions the way most commercially available forecasts do. This isn't a claim that bottom-up forecasts are wrong — several are reasonable quality — but they tend to be resource-heavy, complex, and run externally, which limits transparency and makes them impractical to re-run in real time as new information arrives. A lighter-weight, internally-owned top-down approach can be understood, run, and re-run on the author's own terms. This is explicitly experimental: the project doesn't yet know if it matches or outperforms existing bottom-up forecasts, and part of Module 1's job is to find out.

No fabricated data or technology moat is claimed: the bid curve and fundamentals data (Nord Pool API, Volue API) are inputs a well-resourced market participant could also obtain. The edge, if it exists, is in the adjustment technique itself — how curve changes are attributed to the right driver and the right part of the curve — and in what lightweight ownership enables: once a module's skill and reliability are tested and verified against realised outcomes, its disagreement with the market becomes an actionable trading signal, not just a discrepancy to explain away.

## Who This Serves

**Primary users:** the author and professional colleagues in Nordic power trading/hedging roles, who need a fast, defensible, driver-attributed view of short-term and forward Nordic system price formation — something to actually inform trading and hedging decisions, not just a course artifact.

**Secondary audience:** this is also a graded IBE160 deliverable at Høgskolen i Molde (hand-in mid-December 2026). The course professor will review this brief directly and may give feedback that reshapes scope or priorities — this document should be read as the author's own honest plan, provisional to that review, not a final contract. Teaching staff may also have visibility inside the working repository for follow-up.

## Success Criteria

- **Floor (non-negotiable minimum):** Module 1 is methodologically functioning — the top-down bid-curve-adjustment approach is genuinely implemented end-to-end, calibrated in-sample against observed historical curve changes, and tested out-of-sample as an actual forecast, not left as an untested sketch.
- **Stretch (as far as time allows):** quality is pushed beyond that floor for as long as the semester permits — the explicit best case is a Module 1 forecast good enough that the author would use it professionally this semester, not only submit it. Time (mid-December hand-in), not a fixed quality target, is what ends this iteration.
- Module 2 and Module 3 are attempted after Module 1 clears its floor, in that priority order, time permitting — Module 1 and 2 take priority over Module 3 unless the professor's feedback on this brief says otherwise.
- The platform makes it visible *which* fundamentals drove a forecast or forecast change (attribution), for both Module 1 and Module 2 — not just an output number.
- Module 1 and Module 2 outputs are shown alongside the corresponding financial market pricing: once a module's skill is tested and verified, a persistent disagreement with the market can be read as a mispricing signal worth acting on, not just logged as a discrepancy.
- Course-facing: the IBE160 grading criteria for whatever scope is actually reached are met (working software, documented methodology, evidence of AI-assisted development practice) — treated as compatible with, not separate from, the professional-usefulness bar above.

## Scope

**In for this semester, in priority order:**
1. Dashboard shell able to host multiple tools.
2. Module 1 (short-term SYS forecast) — full top-down methodology: bid curve baseline selection, supply/demand curve adjustment for all listed fundamentals factors (see addendum for the full factor list), weather-ensemble output, comparison against short-term financial contracts. This is the floor described above.
3. Module 2 (mid-term statistical model) — rolling model, sensitivity outputs, coefficient-evolution visualisation, comparison against forward financial contracts. Attempted after Module 1's floor is reached.
4. Module 3 (grid & availability module) — network/asset map, availability timeline, AI assistant for grid bottleneck / FBMC questions. Lowest priority of the three; attempted with whatever time remains.

**Explicitly out of scope for this semester** (per the user's own notes — see addendum for full detail):
- Module 1: Nordic bid identification/disaggregation into area-level curves, extension to CWE and Baltic areas, full area-level clearing.
- Module 2: using live sensitivity coefficients to real-time-adjust computationally heavy vendor fundamental models, and comparing that adjusted output against the live market to flag mispricing.
- Module 3: historical shadow-price analysis tools and FB-variable-to-fundamental-variable relationship tools (the author has partial code for these from professional work already); extending the AI assistant to reason over historical shadow prices and FB variables directly.
- A previously-considered "news synthesizer" concept has been dropped entirely — not part of this project's scope at any phase.

**Not yet decided:**
- Exact tech stack. The course's own setup materials assume Python (FastAPI-style backend, managed with `uv`), a Vercel/Netlify-deployed frontend, Supabase for Postgres/auth/realtime, Docker, and GitHub Actions — but this is the course's *default* toolchain, not a confirmed mandate for this specific project. Python is a strong independent fit regardless, given the modeling work (regression, statistics) is naturally Python.
- Frontend interaction design beyond "interactive figures of curves, cleared prices, and weather outcome space, with high user configurability of reference state and forecast input" — not detailed yet.

## Vision

If the top-down methodology proves out, this becomes a genuinely professional-grade tool: Module 1 and 2 running as daily-use inputs to real trading and hedging decisions, continuously reconciled against the financial market's own view. Module 3 matures from a bottleneck-awareness map into deeper shadow-price and FB-variable analysis, drawing on the author's existing professional work in that area. Longer-term, the platform's reach extends from the Nordic system price to full area-level pricing across the Nordics, CWE, and the Baltics — requiring flow-based clearing and bid-to-area attribution that are explicitly out of scope for now, but are the natural next chapter if the core methodology earns its place.
