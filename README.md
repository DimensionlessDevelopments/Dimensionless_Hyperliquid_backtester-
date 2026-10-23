# Dimensionless_Developments
# Hyperliquid RSI Trading Bot — Backtesting Framework

A Python-based quantitative research project for evaluating an RSI-driven trading strategy on historical cryptocurrency market data, with configurable execution costs, portfolio accounting, risk metrics, and strategy sensitivity analysis.

> **Status: Research prototype**
>
> This project is intended for strategy research, backtesting, and quantitative experimentation. It has not been validated as a profitable strategy and is not a production-ready trading bot. Historical and synthetic test results must not be interpreted as evidence of future performance.

---

## Table of Contents

- [Overview](#overview)
- [Objectives](#objectives)
- [Strategy](#strategy)
- [Features](#features)
- [Backtesting Methodology](#backtesting-methodology)
- [Performance and Risk Analytics](#performance-and-risk-analytics)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
- [Configuration](#configuration)
- [Running a Backtest](#running-a-backtest)
- [Validation and Testing](#validation-and-testing)
- [Known Limitations](#known-limitations)
- [Roadmap](#roadmap)
- [Risk Disclaimer](#risk-disclaimer)
- [Contributing](#contributing)
---

## Overview

This project explores the development and evaluation of a systematic RSI-based trading strategy for cryptocurrency markets, with Hyperliquid perpetual markets as the intended application.

The framework is designed to make strategy assumptions explicit and to provide a repeatable process for analysing historical performance. Rather than evaluating a strategy solely on headline returns, it incorporates transaction costs, execution assumptions, portfolio equity tracking, and risk-adjusted performance metrics.

The current implementation focuses on a long-only RSI threshold-crossing strategy. Its main purpose is to provide a baseline that can be tested, challenged, and extended before any consideration of live trading.

### Project goals

- Build a reproducible environment for quantitative strategy research.
- Reduce common backtesting errors, including lookahead bias and inconsistent trade accounting.
- Evaluate performance after configurable trading costs.
- Measure drawdown, volatility, trade outcomes, and cost sensitivity.
- Establish a foundation for future strategy development and exchange integration.

## Objectives

The project is intended to support three related workflows:

| Area | Objective |
|---|---|
| Quantitative research | Test strategy hypotheses and investigate whether historical performance is robust. |
| Quantitative development | Build reusable, testable components for data processing, signal generation, simulation, and analytics. |
| Trading-system development | Establish a foundation for future paper trading and exchange integration, subject to further validation. |

The framework is not designed to guarantee returns or to replace independent risk assessment.

## Strategy

The baseline strategy uses the Relative Strength Index (RSI), calculated using Wilder's smoothing method.

### Entry rule

Open a long position when RSI crosses upward through the configured threshold.

### Exit rule

Close the long position when RSI crosses downward through the configured threshold.

### Baseline parameters

The default configuration includes:

| Parameter | Default |
|---|---:|
| RSI period | 14 |
| RSI threshold | 70 |
| Initial capital | 1,000 quote-currency units |
| Capital allocation | 50% |
| Leverage | 1× |
| Trading timeframe | 1 day |
| Fee rate | 0.045% per side |
| Slippage assumption | 2 basis points |
| Stop loss | Disabled |
| Take profit | Disabled |

These values are configurable research defaults, not recommended live-trading settings. The fee rate, capital allocation, and execution assumptions must be checked against the intended instrument, account tier, and trading conditions.

The RSI rule is a baseline hypothesis. Its profitability and suitability have not been established.

## Features

### Market data

- OHLCV data validation.
- Timestamp and chronological-order checks.
- Duplicate and malformed data checks.
- Paginated historical-data retrieval support.
- Coverage checks to help identify incomplete history.

Data retrieval is deliberately not triggered automatically when the notebook is opened. This makes research runs more controlled and avoids unexpected network calls.

### Signal generation

- Wilder RSI calculation.
- Configurable RSI parameters.
- Explicit signal timing.
- Next-bar-open execution assumptions to reduce lookahead bias.

### Portfolio simulation

- Long-only position simulation.
- Configurable capital allocation and leverage.
- Entry and exit transaction fees.
- Adverse slippage assumptions.
- Optional funding-rate inputs.
- Optional stop-loss and take-profit rules.
- Approximate maintenance-margin liquidation handling.
- Equity-curve and drawdown tracking.
- Event logs and completed-trade records.

### Research and reporting

- Performance summary metrics.
- Buy-and-hold price benchmark.
- Cost sensitivity analysis.
- Calendar-year return analysis.
- Synthetic validation tests.

## Backtesting Methodology

The simulator attempts to make execution assumptions explicit rather than assuming that signals can always be filled at the prices used to generate them.

### Signal timing

Signals are derived from completed candles. The baseline execution model uses the following candle's opening price, adjusted for the configured slippage assumption.

This is more conservative than filling at the same candle close that generated the signal. It does not, however, reproduce actual order-book execution or guarantee that the assumed price was available.

### Transaction costs

The model includes configurable entry and exit fees and adverse slippage. Funding can be incorporated when a suitable funding-rate series is supplied.

Results are only as reliable as the cost inputs and execution model. Actual fills can differ because of spreads, liquidity, market impact, order type, latency, and exchange-specific rules.

### Stops and liquidation

Optional stop-loss and take-profit rules are supported. If both levels are touched within one candle and the price path is unknown, the simulator applies a conservative stop-first assumption.

Liquidation is approximate. It should not be treated as an exact reproduction of Hyperliquid's margin engine, liquidation process, or account-level risk controls.

### Performance measurement

Performance is evaluated using the simulated equity curve and completed-trade records. The report includes return, risk, trade, and cost metrics to provide a broader assessment than cumulative return alone.

## Performance and Risk Analytics

The framework reports metrics including:

- Total return and compound annual growth rate (CAGR).
- Annualised volatility.
- Sharpe ratio and Sortino ratio.
- Maximum drawdown.
- Number of completed trades and win rate.
- Average trade P&L and profit factor.
- Total transaction fees.
- Buy-and-hold gross price benchmark.
- Calendar-year returns.
- Sensitivity to alternative fee and slippage assumptions.

These metrics should be interpreted together. For example, a positive return can coexist with an unacceptable drawdown, and a high historical Sharpe ratio may be unreliable when the sample is short or returns are serially correlated.

A gross price benchmark is not equivalent to a fully cost-adjusted, risk-matched investment benchmark.

## Project Structure

The primary research artefact is a Jupyter notebook.

```text
Dimensionless_hyperliquid_trading-bot/
├── hyperliquid-trading-backtester.ipynb
├── README.md
├── requirements.txt
├── .gitignore
├── data/
│   └── .gitkeep
├── reports/
│   └── .gitkeep
└── tests/
    └── .gitkeep
```

The structure above is a recommended repository layout; directories and dependency files should be added as the project is modularised. The revised notebook currently contains the principal research and simulation logic.

## Getting Started

### Requirements

- Python 3.10 or a compatible version supported by the selected dependencies.
- Jupyter Notebook or JupyterLab.
- Python libraries used by the notebook, including NumPy, pandas, and the relevant data-access and plotting packages.

Confirm the actual Python version and dependency requirements in the project environment before running the notebook.

### Installation

Clone the repository:

```bash
git clone <repository-url>
cd hyperliquid-trading-bot
```

Create and activate a virtual environment:

```bash
python -m venv .venv
```

On macOS or Linux:

```bash
source .venv/bin/activate
```

On Windows PowerShell:

```powershell
.venv\Scripts\Activate.ps1
```

Install the project's pinned dependencies once a `requirements.txt` file has been defined:

```bash
pip install -r requirements.txt
```

Launch Jupyter:

```bash
jupyter lab
```

Open `hyperliquid-trading-bot-backtest-revised.ipynb` and execute the notebook cells in order.

> The dependency file should reflect the versions actually tested with the project. Do not assume an unverified package list is a tested environment.

## Configuration

The notebook uses a central `BacktestConfig` dataclass to hold the main strategy and simulation settings.

Typical configuration areas include:

- RSI period and threshold.
- Initial capital and capital allocation.
- Leverage.
- Fee rate and slippage.
- Market timeframe and historical data interval.
- Backtest evaluation interval.
- Optional stop-loss and take-profit parameters.

Before running an experiment, record the configuration alongside the data source, data interval, code version, and output report. This is essential for reproducibility and fair comparison between strategy variants.

## Running a Backtest

A recommended workflow is:

1. Verify the market-data source and instrument.
2. Check timestamp continuity and data quality.
3. Configure the strategy and execution assumptions.
4. Run the synthetic validation tests.
5. Execute the backtest on verified historical data.
6. Inspect the equity curve, trade log, and event log.
7. Review risk metrics and cost sensitivity.
8. Save the configuration and results.
9. Repeat using a separate validation period before drawing conclusions.

A backtest should not be considered validated simply because the notebook executes without errors.

## Validation and Testing

The revised notebook includes deterministic synthetic tests to check selected components under controlled conditions.

These tests are useful for checking software behaviour, but they do not establish that the strategy is profitable or that the simulator perfectly models the exchange.

Recommended further testing includes:

- Hand-calculated accounting scenarios.
- Tests for missing and duplicated candles.
- Signal-timing and lookahead-bias tests.
- Fee and slippage reconciliation.
- Stop-loss and take-profit gap scenarios.
- Insufficient-collateral and liquidation edge cases.
- Zero-trade and end-of-data behaviour.
- Historical data coverage verification.
- Out-of-sample and walk-forward performance evaluation.
- Comparison against independently calculated reference results.

## Known Limitations

The project is a research prototype and has important limitations.

1. **Strategy validation:** No verified historical or out-of-sample result currently establishes a persistent trading edge.
2. **Execution:** Slippage is modelled and fills are simplified. The simulator does not reproduce full order-book dynamics.
3. **Funding:** Funding is not included unless an appropriate rate series is supplied.
4. **Liquidation:** Maintenance margin and liquidation are approximations, not an exact exchange implementation.
5. **Market coverage:** Historical data must be checked for gaps, correct instrument identity, and sufficient coverage.
6. **Risk metrics:** Ratios and annualisation assumptions depend on data frequency and sample characteristics.
7. **Instrument scope:** The baseline implementation is long-only and does not represent a complete multi-asset or portfolio-margin system.
8. **Production readiness:** Live execution, order reconciliation, monitoring, fault recovery, and exchange-specific safeguards have not been established by this notebook.

These limitations should be resolved or explicitly accepted before relying on the model for capital-allocation decisions.

## Roadmap

### Phase 1 — Research reliability

- [ ] Verify historical OHLCV data and coverage.
- [ ] Reconcile portfolio accounting against hand-calculated cases.
- [ ] Expand synthetic and edge-case test coverage.
- [ ] Document dependencies and reproducible environments.
- [ ] Produce a baseline historical report.

### Phase 2 — Quantitative validation

- [ ] Add verified historical funding rates.
- [ ] Improve execution-cost and fill modelling.
- [ ] Compare against appropriate benchmarks.
- [ ] Add parameter stability and sensitivity studies.
- [ ] Implement chronological train, validation, and test periods.
- [ ] Conduct walk-forward and out-of-sample testing.

### Phase 3 — Research platform

- [ ] Extract core logic into reusable Python modules.
- [ ] Introduce automated tests and continuous integration.
- [ ] Add versioned experiment configurations and result storage.
- [ ] Support alternative strategy implementations.
- [ ] Improve performance attribution and risk reporting.

### Phase 4 — Paper trading

- [ ] Integrate live market data.
- [ ] Add a paper-trading execution adapter.
- [ ] Reconcile expected and simulated fills.
- [ ] Add monitoring, alerting, and fault handling.
- [ ] Document deployment and operational procedures.

### Phase 5 — Controlled live deployment

- [ ] Validate exchange-specific account and margin mechanics.
- [ ] Implement position limits and emergency shutdown controls.
- [ ] Verify order-state and balance reconciliation.
- [ ] Complete operational and security reviews.
- [ ] Consider limited deployment only after all earlier validation gates pass.

## Risk Disclaimer

This software is provided for research, educational, and experimental purposes only. It is not investment advice, and no representation is made that the strategy will be profitable or suitable for any particular user.

Cryptocurrency perpetual contracts involve substantial market, leverage, liquidity, funding, and liquidation risks. Backtests are sensitive to data quality, modelling assumptions, parameter selection, and market regime. Past simulated performance does not guarantee future results.

Do not deploy capital based solely on backtest outputs. Independently validate the data, strategy, execution model, and risk controls before considering live trading.

## Contributing

Contributions that improve reproducibility, test coverage, accounting correctness, data integrity, execution realism, or research transparency are particularly welcome.

For proposed strategy changes, document:

- The hypothesis being tested.
- The parameters and data used.
- The baseline comparison.
- The impact of fees and slippage.
- Out-of-sample results, where available.
- Known limitations and unresolved assumptions.

Keep strategy improvements separate from simulator changes wherever possible so that performance changes can be attributed correctly.


**Built with ❤️ for quantitative trading research**
# Dimensionless Hyperliquid Backtesting Bot

---


## Contact
**Made by Dimensionless Developments**
**Head to our website https://quant.dimensionlessdevelopments.com/**
  **↓   ↓   ↓   ↓   ↓   ↓   ↓   ↓   ↓   ↓**
**Email: contactus@dimensionlessdevelopments.com**
