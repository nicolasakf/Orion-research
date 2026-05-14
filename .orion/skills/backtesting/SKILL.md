---
name: backtesting
description: Builds and reviews reproducible strategy backtests in notebooks. Use when creating a new backtest, adapting an existing strategy analysis, validating portfolio exposures, benchmarking returns, or summarizing performance and drawdown results.
---

# Backtesting

Use this skill to build a clean, repeatable backtest notebook and review it in a consistent order.

Reference style: based on a research notebook that instantiates a strategy object, wraps it in a `Backtest`, assigns benchmark data, then reviews universe, exposure, cumulative return, drawdown, and period performance.

## Default workflow

1. **Initialize the notebook cleanly**
   - Import core libraries first.
   - Keep setup, strategy construction, backtest wiring, diagnostics, and reporting in separate cells.
   - Parameterize strategy options instead of scattering hardcoded values across cells.

2. **Instantiate the strategy**
   - Create the strategy with an explicit parameter dictionary.
   - Display or inspect the resulting strategy object immediately to confirm configuration.
   - If the project has side-effect methods such as uploads or report publishing, gate them behind a debug or production flag.

3. **Create the backtest object**
   - Wrap the strategy in a dedicated backtest object.
   - If benchmark market data is available, assign it explicitly before plotting performance.
   - Confirm the backtest object is initialized and has the expected date range, portfolio data, and benchmark series.

4. **Review portfolio construction before performance**
   - Inspect the investable universe and filter pass counts.
   - Plot or tabulate gross and net exposure.
   - Check hedge exposure if the strategy is market-neutral or benchmark-aware.
   - Review number of positions, long/short balance, and turnover or names traded by period.
   - Surface any strategy-specific diagnostics that explain position sizing or event selection.

5. **Evaluate performance in layers**
   - Start with cumulative return.
   - Compare against benchmark where relevant.
   - Show long and short legs separately for long/short strategies.
   - Include transaction cost impact when the framework exposes it.
   - Review drawdown level and worst drawdown by period.
   - Break out return by annual, quarterly, and monthly periods when enough history exists.

6. **Attribute and contextualize results**
   - Add contribution analysis by sector, factor, sleeve, or another meaningful grouping if available.
   - Use treemaps or grouped bar charts only when they add interpretability.
   - Prefer simple tables over flashy plots when comparing many periods.

7. **Summarize findings explicitly**
   - State whether performance is driven by a few periods, one side of the book, or a broad edge.
   - Note exposure stability, concentration, benchmark dependence, and cost sensitivity.
   - Call out fragile assumptions, missing data risks, and implementation caveats.

## Notebook structure template

Use sections in this order:

1. `# Strategy name`
2. `## Init`
3. `## Backtest setup`
4. `## Portfolio`
   - `### Universe`
   - `### Exposure`
   - `### Strategy-specific diagnostics`
5. `## Performance Plots`
   - `### Cumulative Return`
   - `### Drawdown`
   - `### Performance by Period`
6. `## Key Takeaways`

## Coding rules

- Separate setup from analysis.
- Use descriptive names like `strategy`, `backtest`, `benchmark_prices`, `exposure_summary`.
- Avoid hidden side effects in analysis cells.
- Put uploads, exports, or publishing steps in guarded cells.
- Keep plotting calls deterministic and rerunnable.
- When a helper object exposes `plot` methods, call them in small focused cells rather than building one oversized dashboard cell.

## Validation checklist

Before concluding a backtest, verify:

- The strategy parameters are visible in the notebook.
- The benchmark series matches the intended market and date range.
- Gross and net exposures are plausible.
- Position counts and trade counts are realistic for the strategy style.
- Performance is reviewed both before and after costs if possible.
- Drawdowns are examined, not just returns.
- Results are broken down by time period, not only full-sample totals.
- Any unusual spikes are investigated with portfolio or event-level diagnostics.

## Reporting defaults

When summarizing a backtest, include:

- Strategy objective
- Test period
- Benchmark used
- Net and gross exposure behavior
- Cumulative return and drawdown overview
- Best and worst periods
- Cost impact
- Main drivers of performance
- Main limitations and next checks

## Minimal pseudocode pattern

```python
# Init
strategy = StrategyClass(params={
    "param_a": value_a,
    "param_b": value_b,
})

# Backtest setup
backtest = Backtest(strategy)
backtest.benchmarks = benchmark_prices.to_frame("benchmark")

# Portfolio diagnostics
strategy.filter_count.plot(title="Names passing each filter")
backtest.plot.net_gross_exposure(same_ax=False)
backtest.plot.n_positions_ls(mirror=True)

# Performance
backtest.plot.cumulative_return(plot_bench=True, adj_bench_vol=True)
backtest.plot.drawdown()
backtest.plot.period_return('Y')
```

## Adaptation guidance

Adapt this skill to the project’s framework:

- Replace `StrategyClass` and `Backtest` with local abstractions.
- If the project stores benchmark data on the strategy, wire it into the backtest explicitly.
- If there are custom diagnostics such as event counts, signal strength, or volatility regime splits, place them under portfolio diagnostics before headline return charts.

## Anti-patterns

Avoid:

- Jumping straight to cumulative return without checking exposures or position counts.
- Mixing data preparation, order upload, and analysis in one cell.
- Using only full-period Sharpe or total return to judge a strategy.
- Omitting drawdown and cost review.
- Hiding benchmark choice or parameter settings.
