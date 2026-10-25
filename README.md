# DK1 Day-Ahead Power Price Forecasting

Forecasting tomorrow's hourly electricity prices in western Denmark (DK1) before the noon day-ahead auction, and measuring what the forecast is worth in a battery trading backtest.

> **Status:** work in progress. Phase 0 (setup) complete. Results will appear here as they are produced.

## The question

Can public data available at 11:00 on the day before delivery predict tomorrow's 24 DK1 prices better than simple rules, and how much money does that difference make?

## Success criteria

These were set before any results were seen, so the goalposts cannot move.

| Criterion | Target |
| --- | --- |
| Beat the best naive baseline on MAE | At least 15% lower |
| 80% prediction interval coverage | 75% to 85% of actual prices inside the interval |
| Battery backtest | Clearly more profit than the naive forecast, reported as % of perfect foresight |
| Reproducibility | Anyone can clone the repo and rerun everything |

## Approach (planned)

1. **Data:** day-ahead prices and wind and solar forecasts from [Energi Data Service](https://www.energidataservice.dk/) (Energinet), free and without login.
2. **Information cutoff:** every feature must be known at 11:00 CET on the day before delivery. No look-ahead.
3. **Baselines:** same hour yesterday, same hour last week, and the 4-week same-weekday average.
4. **Model:** LightGBM with calendar, lagged price, neighbouring market and renewable forecast features, plus quantile models for uncertainty.
5. **Evaluation:** walk-forward backtest over the most recent 12 months.
6. **Trading backtest:** a 1 MW / 2 MWh battery that charges in the cheapest forecast hours and discharges in the most expensive, settled at actual prices.

## Setup

Requires [uv](https://docs.astral.sh/uv/).

```bash
git clone https://github.com/henriquemsclaudino/dk1-price-forecast.git
cd dk1-price-forecast
uv sync
```

`uv sync` creates the virtual environment and installs the exact package versions from `uv.lock`.

## Repository layout

```
data/        raw and processed data (not tracked by git)
notebooks/   exploration
src/         pipeline: fetch, features, baselines, training, evaluation, battery backtest
app/         Streamlit dashboard
tests/       tests for features and evaluation
```

## Data source

Data from Energi Data Service, Energinet, licensed CC BY 4.0.
