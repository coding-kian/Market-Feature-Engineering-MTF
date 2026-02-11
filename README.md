*Published 09/02/2026*
# Probabilistic Market Scoring System (2021)
> *A partially redacted historic snapshot*

> This project is for representation purposes only, it is not a trading product. The public version shows the data architecture, and thought process.  
> Multi-Timeframe Signals (Pine Script v4). MTV (MACD Trending Volume), MTF (Multiple Time Frames)

Originally from 2021, where I aimed to build a continuous market tool, using multiple time frames and weighted signals to understand behaviour. It was created solely through independent research. 
As a **partially redacted historic snapshot** of a project, it looks at **how** you can model the market in a probabilistic manner using volume and momentum across timeframes. Showing feature engineering and signal aggregation through a heuristic approach with weight decaying over time.

See video `https` for full product.

---

## Introduction
Popular indicators use discrete isolated single events such as crossovers or thresholds, this project looks at a combined continuous probabilistic value, allowing for more accurate interpretation without memory or context of the market. It is an early attempt at a continuous heuristic decision system.
The goal was to design a project is to convert raw temporal data into stable, reusable and explainable signals instead of noisy one off triggers. 

- compute a set of related signals (momentum, volume trend, candle shape)
- track how recently they occured for weighted decay
- aggregate and assign interpretable weight (probability), to create **enter size suggestion** and **TP size suggestions**

---

## Objective, Advantages & Limitations
### Objective
Build a multi timeframe feature, ranking framework that can turn market volume behavior into normalized entry and TP suggestions. 
Show state, weight and multiple time frame aggregation of patterns to signal into a backend python system using webhooks. (Removed from project)
Shows aggregation of signals over temporal dimensions, feature engineering under constraints and decaying scoring system.

### Advantages
- More structure feature pipelines,
- Temporal weighting through decay
- continuous explainable outputs. 
- Feature engineering under constraints
- Structured interpretable outputs for justifiable actions

### Limitations
- PineScript v4 limits readability and modularity.
- Heuristic thresholds are manually tuned
- No FORMAL backtesting or optimisation frameworks used
- Designed for high liquidity markets

---

## Data, Method & Outputs.
### Data used
This uses **standard TradingView chart series** only:
- `open`, `high`, `low`, `close`
- `volume`
- `tr` (true range)


### Method overview
The script builds several layers:

1. **Base features**
   - EMA/SMA of price and volume (e.g., 8/13/20)
   - volatility via `stdev(close, 20)`
   - candle body (`open-close`) and wick/shape heuristics
   - “above/below” 20 EMA regimes and streak counters

2. **Momentum + Volume Trend logic**
   - MACD signals (8 EMA & 13 EMA) with short signal smoothing
   - volume & price changes classification into bullish/bearish “trend”, for a combined bull/bear condition

3. **Signal detection**
   - “dots” and signal types are detected using rolling counts
   - Weights are created which **decay over time** using `barssince()` patterns and manual weighting logic

4. **Take-profit model (TP)**
   Multiple TP heuristics are aggregated, including:
   - adjacent candle patterns (hammer/star-like/engulfing )
   - Bollinger band behavior (touch/overextension patterns)
   - wick/engulfing/volume “gulfing” style signal

5. **Aggregation → Position sizing**
   The system converts:
   - MTVS strength
   - MTVD strength
   - Run-up/exhaustion adjustments
   - TP signal  
   into **Entry suggestions** (e.g. 5%-40% style).

6. **Multi-timeframe output**
   Designed for 1 minute timeframe, and executed on higher timeframes.:
   - `security(..., "5", mtv_values())`
   - `security(..., "15", mtv_values())`


### Computed Output
- **MTVS** - MACD Trending Volume signal (primary state)
- **MTVD** - Volatility and candle size indication dot signal
- **Large E** - high volume engulfing signals.
- **Run Up** - continuation or exhaustive pattern over the rolling windows
- **TP (Take Profit)** - Aggregates all signals into a continuous probabilistic signal.
- **Entry labels**: Calculated from above weights  entry/exit size and TP-linked entry info
- Two supported MTF outputs: **5 & 15 minutes**

