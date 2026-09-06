# Mean-Reversion Statistical Arbitrage: A Cointegration-Based Research Project

## Context

This project started as a passion project to explore financial markets and
quantitative finance - I built it independently, outside of coursework, to
get hands-on with real statistical research methodology. 

## Research Question

> Can a cointegration-based mean-reversion strategy generate competitive
> risk-adjusted returns out-of-sample after accounting for multiple testing
> and transaction costs?

## Motivation

Two stocks can move together day-to-day without ever converging back to a
stable relationship - correlation alone doesn't guarantee that. This project
investigates whether a small set of economically-linked stocks actually
share a statistically defensible long-run equilibrium (cointegration), and,
if so, whether that relationship remains tradeable when evaluated strictly
out-of-sample rather than in-sample.

## Methodology

1. **Data.** Daily prices, sector-diversified basket, via yfinance. Train
   (2010–2022) and test (2023–2025) are strictly separated.
2. **Candidate pair selection.** 8 pairs pre-selected using real economic
   relationships (direct competitors / close peers), not blind combinatorial
   testing - this keeps the multiple-testing burden low and avoids data
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
   independently - never used for any model or parameter decision.

## Results

Of 8 candidate pairs, **1 pair (KO-PEP)** survived FDR-corrected
cointegration testing (corrected p = 0.036).

| Metric | Mean-Reversion Strategy | SPY Benchmark |
|---|---:|---:|
| Total Return | -0.82% | 58.24% |
| Annualized Return (CAGR) | -0.41% | 25.93% |
| Win Rate | 21.12% | 57.49% |
| Annualized Volatility | 7.25% | 12.82% |
| Sharpe Ratio | -0.02 | 1.87 |
| Max Drawdown | -14.52% | -9.97% |


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
  (KO-PEP) survived correction - an n=1 result that doesn't generalize to
  claims about pairs trading as a strategy family.
- **Untuned entry/exit thresholds.** Z-score levels (2.0 / 0.5) are
  reasonable defaults, not calibrated on a held-out validation set - tuning
  them against the test data would reintroduce look-ahead bias.
- **Simplified transaction-cost assumptions.** Flat per-trade cost, not a
  more realistic slippage/liquidity model.
- **No walk-forward validation.** A single split means these results are one
  data point, not a robust estimate of expected performance.

## v1 Status: Frozen - Completed Negative Result

This experiment is closed, not paused. Diagnostic checks were run before
considering any model complexity:

- **Transaction-cost sensitivity:** Sharpe is only 0.09 even at zero cost,
  and turns negative between 0 and 10 bps - there was minimal edge to
  protect from costs in the first place.
- **Statistical power / FDR fragility:** KO-PEP's cointegration clears the
  FDR threshold at 8 simultaneous tests (~0.00625) but fails at 9
  (~0.00556) - one additional candidate pair erases the "significant"
  result. This is a fragile finding, not a robust one.
- **Walk-forward instability:** Yearly Sharpe (2019–2024) alternates sign
  almost every year (-0.41, 0.90, 0.70, -0.81, 0.84, -0.84) with no
  persistent direction - consistent with noise, not a stable edge.

**Decision:** this version will not be reopened by expanding the candidate
universe within this experiment. Any continuation is a separate,
pre-registered v2 experiment (protocol defined before outcomes are
observed) - see [v2 protocol, once written].

## Future Work

A v2 experiment, if pursued, will be pre-registered: universe, candidate-pair
rule, hypothesis family size, split methodology, transaction-cost
assumptions, and an explicit pass/fail criterion all defined before any
outcome is observed. It will only proceed to model complexity (an ML
signal filter) if v2 itself produces a stable out-of-sample signal worth
filtering.