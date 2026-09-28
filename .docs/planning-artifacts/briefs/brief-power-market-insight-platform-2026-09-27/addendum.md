---
title: "Addendum: Power Market Insight Platform"
status: draft
created: 2026-09-27
updated: 2026-09-28
---

# Addendum: Power Market Insight Platform

Supporting technical depth from the brief conversation and the user's source notes (`Draft IBE160 app idea - updated.md`). Source material for downstream PRD/architecture work, not brief content itself.

## Module 1 — Short-term SYS forecast: full methodology detail

**Data sources:** historical aggregated bid curves from the Nord Pool API; most fundamentals data (to model change from reference state) from the Volue API, including net export flow forecasts (Volue also forecasts cross-border flows); unavailability/revision (UMM) data primarily from the NUCS API (Nordic Unavailability Collection System).

**Reference-state selection (open research question):** whether to use the most recent trading day's curve as the reference state, or something like the same day last week, is explicitly unresolved and needs experimentation/testing — this is the "which baseline lead time" question. Hour-in-day patterns are captured by matching the reference-state hour.

**Supply-curve adjustment factors** (each extends/contracts the relevant part of the curve, or shifts it):
- Seasonality: day-of-week and public-holiday patterns in market participation.
- Wind production changes.
- PV (solar) production changes.
- Unregulated (run-of-river) hydro production changes.
- Balancing capacity market (CM) changes: more CM-down → more must-run / less marginal-cost bidding behaviour; less CM-down → less must-run / more MC bidding; more CM-up → reduces availability; less CM-up → increases availability. Key challenge: first determine the change, then correctly assign it to the production technology behind the bid.
- Revision/UMM-related availability changes across all technologies, including nuclear.
- Nuclear bidding behaviour within available capacity.
- CHP must-run production.
- Water value changes — derived via regression/sensitivity methodology, shifting the regulated-hydro part of the curve. Called out as needing its own dedicated technique.
- Thermal SRMCs beyond nuclear (gas, coal, biomass, CHP marginal-cost bidding).

**Demand-curve adjustment factors:**
- Seasonality: day-of-week, public-holiday, hour-in-day pattern matching (same as supply side).
- Temperature-related consumption — open question: where in the curve do bids actually respond to temperature.
- Revision/UMM-related availability of large consumers.

**Calibration approach:** work with actual observed values for all determining factors and supply/demand curves; aim for best combined fit to the observed curve changes. Start with assumed change-models per factor, then test/diagnose/calibrate/improve iteratively. Once the fit works acceptably in-sample, attempt it out-of-sample as an actual forecast.

**Output:** the forecast spans the EC (ECMWF) 15-day weather forecast range, deriving supply/demand curves *per weather ensemble member* — i.e. a distribution/outcome space, not a point forecast. Compared against price expectations implied by financial derivatives at the same short-term horizon (daily and weekly contracts, up to two weeks forward).

**Frontend needs (not detailed yet):** interactive figures of the curves themselves, and of cleared prices with the weather outcome space per time step. High degree of user configurability: choice of reference state, and possibly choice of weather forecast input if the vendor API supports alternatives.

**Explicitly beyond this project's scope, if the MVP succeeds:** Nordic bid identification and disaggregation into area-level curves; extending to CWE and Baltic areas; full area-level clearing.

## Module 2 — Mid-term statistical model: detail

Rolling statistical model based on recent history, with three user-configurable parameters: history length, choice of explanatory variables, and a decay parameter that down-weights older observations. Explanatory-variable thinking (inflow, temperature, thermal SRMCs, etc.) exists in more detail in the user's own work notebook — not elaborated fully here.

Explains SYS forward market price *change* across the full range of tradable contracts, starting at the W+3 weekly contract and covering months, quarters, and calendar years (a wider range than "DEC/Q4/CAL" as originally stated in the first draft brief).

**Twofold purpose:**
1. Output sensitivities usable out-of-sample to calculate expected price movement (intraday, or longer-horizon if an earlier reference baseline is selected) as new information arrives after the most recent market close — compared against live market changes.
2. Visualise how each coefficient evolves over time, per contract, to understand how the market's sensitivity to each driver is changing.

**Explicitly beyond this project's scope, if the MVP succeeds:** using the live coefficients to adjust computationally expensive vendor fundamental models in real time as new information arrives (those models are not re-run continuously because of the computational cost); using the resulting real-time-adjusted fundamental forecast to compare against the live financial market and flag apparent mispricing.

## Module 3 — Grid & availability module: detail

Nordic geographical map showing all transmission network elements and production assets. Availability shown as a timeline the user can travel/scrub along — useful for building intuition about likely grid bottlenecks, which matter for area-level Nordic pricing.

An AI-powered assistant helps the user investigate questions related to grid bottlenecks and Flow-Based Market Coupling (FBMC).

**Data sources:** unavailability data mainly from the NUCS API (Nordic Unavailability Collection System); flow-based (FB) data from the JAO API (Joint Allocation Office).

**Explicitly beyond this project's scope, if the MVP succeeds:** analysis tools on historical shadow prices; tools to investigate the relationship between FB (flow-based) variables and fundamental variables (the user already has a fair amount of code for this from professional work); extending the AI assistant to reason over historical shadow-price observations, FB variables, and forward-looking grid availability information together.

## Dropped concept: news synthesizer

The original (2026-09-20) draft brief included a possible third element: a news synthesizer explaining price changes using both news text and fundamentals model observations. The user has confirmed this is dropped entirely and should not appear in this brief or its later-phase scope, even as a parked idea.

## Course context and tech stack signal

Source: `appendix-A.md` (the course's tool installation/setup guide, IBE160). This describes the course's general case-study toolchain, not a confirmed requirement for this specific project:

- **Primary agent:** Claude Code (this project already has BMAD Method installed on top of it).
- **Backend:** Python, managed with `uv`; example dependencies given are FastAPI + SQLAlchemy.
- **Frontend:** a Node.js-based toolchain, deployed to a Vercel/Netlify-style hosting platform.
- **Database/auth/realtime:** Supabase (Postgres).
- **Containers:** Docker, for reproducible builds/runs.
- **VCS/CI:** Git + GitHub, with GitHub Actions for CI/CD.
- **MCP servers:** used to connect the agent to databases, files, and external APIs (relevant here for Nord Pool API / Volue API integration).

This is flagged `[ASSUMPTION]` in the brief pending explicit confirmation — Python is independently well-suited to this project's modeling work regardless of whether the rest of the default stack is adopted as-is.

## Team and process context

Solo build. The user intends to run the BMAD Method process (PM, architect, dev, etc. role-based workflow) as though there were multiple contributors, rather than skipping the collaborative structure because it's a one-person team. Teaching assistants and/or the professor may have visibility inside the working repository for follow-up purposes over the course of the semester.

## Open items for future revisit

- Reference-state lead-time selection for Module 1's baseline curve is an open research question, not a settled design decision — expect this to need dedicated experimentation early in Module 1's build.
- Where in the demand curve temperature-responsive bids actually sit is also unresolved.
- Frontend interaction design (beyond "interactive figures, high configurability") is undecided.
- Tech stack beyond the course's implied default (see above) is unconfirmed.
- The professor's feedback on this brief itself may reprioritize Module 2 vs. Module 3, or adjust success criteria — revisit this document after that feedback lands.
