# Multi-Timeframe Volume / VROC Monitoring

A Thinkorswim/ThinkScript portfolio case study focused on **QA/UAT, multi-timeframe metric design, aggregation-aware logic, troubleshooting, and explainable decision-support workflows**.

## Project Overview

This project separates several volume-related questions that are easy to confuse when compressed into one score:

- **Current volume** — how much is trading on the active bar
- **Average volume** — a recent baseline for comparison
- **Volume intensity** — how busy the current bar is relative to normal
- **VROC** — how quickly volume has changed versus a prior bar
- **Multi-timeframe VROC** — 5m / 15m / 1h context shown together
- **Directional proxy** — a range-weighted close-location estimate used as context, not true order-flow classification

The goal is to keep the individual metrics visible and interpretable rather than hide them inside an opaque composite score.

## Working Example

![Working Multi-Timeframe Volume VROC Dashboard](Screenshot%202026-10-16%20034847.png)

*Working SPY 5-minute chart showing current volume context together with multi-timeframe VROC labels. The visible 5m, 15m, and 1h readings provide short- and broader-intraday participation context without turning the dashboard into an automatic trade signal.*

## Configurable Monitoring

![Multi-Timeframe VROC Configuration](Screenshot%202026-10-16%20034924.png)

*Configuration view showing the multi-timeframe VROC component with 5-minute, 15-minute, and 1-hour monitoring enabled, VROC length set to 10, display clipping enabled, and unavailable-label handling exposed as a user setting.*

## QA Finding: Secondary-Aggregation Safety

A later source review identified an important mismatch between documentation and executable behavior. The recovered historical version directly requested 5m, 15m, and 1h secondary aggregations while comments stated that invalid lower secondary periods were guarded.

The corrected version makes chart-aggregation availability explicit before requesting the intended timeframe. When a requested timeframe would be lower than the active chart aggregation, the study avoids treating that value as valid context and can surface the requested period as unavailable instead.

![Aggregation-Safe ThinkScript Logic](Screenshot%202026-10-16%20035005.png)

*Corrected ThinkScript logic using `GetAggregationPeriod()` plus explicit 5m, 15m, and 1h validity checks before selecting effective secondary aggregations.*

## Why the Metrics Stay Separate

A major design lesson in this project is that **volume intensity** and **VROC** answer different questions.

- Intensity compares current volume with a recent average.
- VROC compares current volume with volume from a prior bar.
- A large VROC can therefore occur even when absolute participation is still small if the comparison bar had very little volume.

Keeping these readings separate makes the dashboard easier to interpret and reduces the risk of treating a dramatic percentage change as proof of unusually heavy participation.

## Directional Proxy Boundary

The BUY/SELL reading is a **range-weighted close-location proxy**. A bar closing near its high receives more weight on the BUY side; a bar closing near its low receives more weight on the SELL side.

It is **not** bid/ask trade classification and is not presented as true order flow.

## QA / Development Lessons

- Compile success is a necessary gate, not proof of correct runtime behavior.
- Secondary-aggregation constraints need to be reflected in executable logic, not only in comments.
- Extreme rate-of-change values can be mathematically valid but still require contextual interpretation.
- Raw metrics and their limits are often more useful than one compressed score.
- Unavailable timeframe context should be explicit rather than silently presented as valid data.
- Metric names should not imply more precision than the underlying formula supports.

## Current Validation

The corrected multi-timeframe study compiled in Thinkorswim and produced visible 5m, 15m, and 1h VROC output on a SPY 5-minute chart.

This portfolio case does not claim exhaustive cross-instrument validation, predictive accuracy, trading alpha, or P&L improvement.

## Skills Demonstrated

- Quality Assurance
- User Acceptance Testing (UAT)
- Requirements Analysis
- Troubleshooting
- Data / Metric Interpretation
- Systems Thinking
- ThinkScript
- Technical Documentation
- Decision-Support Design
- Platform-Aware Debugging

## Scope and Limitations

This is a technical portfolio and decision-support case study. It is not a trading recommendation or a claim of predictive performance.

The public repository focuses on the design, QA findings, corrected aggregation-aware behavior, and selected evidence rather than publishing the full private development archive.
