# Mean-Reversion Statistical Arbitrage: A Cointegration-Based Research Project

## Context

This project started as a passion project to explore financial markets and
quantitative finance — I built it independently, outside of coursework, to
get hands-on with real statistical research methodology. It's now being
adapted as an initial contribution to the BU1LD Financial Machine Learning &
Quantitative Finance research team.

## Research Question

> Can a cointegration-based mean-reversion strategy generate competitive
> risk-adjusted returns out-of-sample after accounting for multiple testing
> and transaction costs?

## Motivation

Two stocks can move together day-to-day without ever converging back to a
stable relationship — correlation alone doesn't guarantee that. This project
investigates whether a small set of economically-linked stocks actually
share a statistically defensible long-run equilibrium (cointegration), and,
if so, whether that relationship remains tradeable when evaluated strictly
out-of-sample rather than in-sample.

## Methodology

1. **Data.** Daily prices, sector-diversified basket, via yfinance. Train
   (2010–2022) and test (2023–2025) are strictly separated.
2. **Candidate pair selection.** 8 pairs pre-selected using real economic
   relationships (direct competitors / close peers), not blind combinatorial
   testing — this keeps the multiple-testing burden low and avoids data
   dredging.
3. **Correlation pre-filter.** Candidates must clear a minimum return
   correlation before formal testing.
4. **Cointegration testing.** Engle-Granger test on log prices (matching the
   scale the spread and hedge ratio are computed on).
5. **Multiple-testing correction.** Benjamini-Hochberg FDR correction
   applied across all simultaneous tests.
6. **Hedge ratio & spread.** OLS on train-only log prices; spread and
   rolling z-score computed from that.
7. **Signal & backtest.** Fixed z-score entry/exit thresholds, vectorized
   backtest with transaction costs.
8. **Out-of-sample evaluation.** All metrics reported below come only from
   the 2023–2025 test window, which no selection or fitting step ever saw.
9. **Benchmark.** SPY buy-and-hold over the identical test window, evaluated
   independently — never used for any model or parameter decision.

## Results

Of 8 candidate pairs, **1 pair (KO-PEP)** survived FDR-corrected
cointegration testing (corrected p = 0.036).

| Metric | Mean-Reversion Strategy | SPY Benchmark |
|---|---:|---:|
| Total Return | -0.82% | *(run notebook to fill in)* |
| Annualized Return (CAGR) | *(run notebook to fill in)* | *(run notebook to fill in)* |
| Win Rate | 21.12% | — |
| Annualized Volatility | 7.25% | *(run notebook to fill in)* |
| Sharpe Ratio | -0.02 | *(run notebook to fill in)* |
| Max Drawdown | -14.52% | *(run notebook to fill in)* |

**This table is intentionally left partly incomplete.** The CAGR and SPY
benchmark code is implemented in the notebook, but has not been executed
against live data yet — see the note at the end of this README.

The strategy underperformed out-of-sample on a raw-return basis. This is
reported as-is: the project is testing a hypothesis, not assuming it, and a
negative or unremarkable result here is a legitimate research finding, not
a failure to hide.

## Limitations

- **Static hedge ratio.** Estimated once on training data, not re-estimated
  over time (no walk-forward re-fitting).
- **Single train/test split.** One fixed window, not walk-forward validated
  across multiple periods.
- **Small number of surviving pairs.** Only 8 candidates tested; only 1
  (KO-PEP) survived correction — an n=1 result that doesn't generalize to
  claims about pairs trading as a strategy family.
- **Untuned entry/exit thresholds.** Z-score levels (2.0 / 0.5) are
  reasonable defaults, not calibrated on a held-out validation set — tuning
  them against the test data would reintroduce look-ahead bias.
- **Simplified transaction-cost assumptions.** Flat per-trade cost, not a
  more realistic slippage/liquidity model.
- **No walk-forward validation.** A single split means these results are one
  data point, not a robust estimate of expected performance.

## Future Work

**Research question:** Can an ML classifier predict whether a z-score
deviation will actually mean-revert, and can it filter out low-quality
trading signals?

Candidate features for that phase: current z-score, spread momentum/rate of
change, rolling volatility, recent returns, historical spread behavior, and
other relevant market features. The goal would be testing whether a learned
filter improves signal quality — not simply optimizing the backtest.

## Note on completeness

The CAGR and SPY-benchmark code (Sections in the notebook: `compute_metrics`
CAGR addition, SPY benchmark cell, comparison plot) is written and ready to
run, but I could not execute it here — no network access to market data in
this environment. Run the notebook end-to-end, then update the results table
above with the actual numbers it prints. Do not fill in numbers here that
the notebook hasn't actually produced.
