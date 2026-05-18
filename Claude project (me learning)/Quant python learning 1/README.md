# Goofy Quant Screener

A 9-phase systematic equity screener covering ~120 stocks across US, ASX (Australia), and JPX (Japan) markets.

Built from scratch as a learning project to understand quantitative finance end-to-end — from raw price data to live paper trading with ML signal validation.

---

## What It Does

Each week the screener downloads live price data, runs every asset through 9 layers of analysis, and produces a colour-coded Excel report with trade signals, position sizes, and portfolio risk metrics.

**Signal chain:**
```
Price data → Strategy backtest → Regime filter → Kelly sizing → Correlation adjustment → ML gate → Paper trade
```

An asset only reaches TRADE status if it passes every layer.

---

## The 9 Phases

| Phase | What it builds | Key output |
|-------|---------------|------------|
| 1–2 | Individual strategy backtests (MA, RSI, BB, MACD, Mean Reversion) | Equity curves, Sharpe ratios |
| 3 | Multi-market screener across US/ASX/JPX | Score 0–100, Tier S/A/B/Skip |
| 4 | Regime detection — only trade in favourable conditions | TRADE / STAND DOWN verdict |
| 5 | Position sizing — Kelly Criterion + volatility scaling | Kelly %, Vol Scalar, Recommended Size % |
| 6 | Portfolio construction — correlation clustering | Cluster, Corr Risk, Adjusted Size % |
| 7 | XGBoost ML signal layer — filters low-confidence signals | ML Score (0–100), Val AUC, ML Gate |
| 8 | Paper trading framework — live P&L tracking | Open/closed trades, win rate, Sharpe |
| 9 | Walk-forward validation — proves the ML edge is real | AUC across time windows, verdict |

---

## The 5 Strategies

Each asset is tested on all 5 strategies. The best out-of-sample performer wins.

- **MA Crossover** — 50-day vs 200-day moving average. Trend-following.
- **RSI** — 14-day Relative Strength Index. Mean reversion on overbought/oversold conditions.
- **Bollinger Bands** — Price position within 2-sigma bands. Mean reversion.
- **MACD** — Momentum acceleration via EMA crossover. Trend-following.
- **Mean Reversion** — Z-score based. Statistical reversion to rolling mean.

Training: 2016–2021 | Test (out-of-sample): 2021–present

---

## ML Features (Phase 7)

11 technical features fed into XGBoost — all use only past data (no lookahead):

| Feature | What it captures |
|---------|-----------------|
| `ret_1m`, `ret_3m`, `ret_6m` | Short, medium, long momentum |
| `rsi_14` | Overbought / oversold level |
| `macd_hist` | Momentum acceleration (normalised by price) |
| `bb_pos` | Position within Bollinger Band (0=lower, 1=upper) |
| `bb_width` | Band width relative to price (volatility regime) |
| `ma200_slope` | Direction of long-term trend |
| `price_vs_ma200` | Distance above/below 200-day MA |
| `vol_21` | 21-day annualised volatility |
| `vol_ratio` | Short vs long volatility ratio |
| `drawdown` | Distance from 252-day high |
| `atr_pct` | ATR percentile — intraday range vs history |

Model: XGBoost with conservative hyperparameters (max_depth=3, min_child_weight=20, L1+L2 regularisation) to resist overfitting on noisy financial time series.

Pass threshold: ML probability ≥ 0.55

---

## Paper Trading Results (Phase 8)

Live paper trades opened 12 May 2026 across 23 positions (US / ASX / JPX).

- Hold period: 20 trading days
- Stop-loss: −5%
- Exit logged with `exit_reason`: `HOLD_COMPLETE` or `STOP_LOSS`
- Trades log persisted at `paper_trades/trades_log.json`

Results close ~10 June 2026.

Historical simulation (2021–present): **56.8% win rate | +1.45% avg 20-day return**

---

## Position Sizing (Phase 5)

```
Kelly % = Win Rate − (Loss Rate / Win/Loss Ratio)
Half-Kelly applied: Final Kelly = Kelly × 0.5

Vol Scalar = Target Vol (15%) / Current Vol (21-day annualised)
Recommended Size = Half-Kelly × Vol Scalar
```

---

## Correlation Adjustment (Phase 6)

Assets with correlation ρ ≥ 0.65 are grouped into clusters.

```
Adjusted Size = Phase 5 Size × (1 / √cluster_size)
```

Prevents hidden concentration when multiple correlated assets all show TRADE signals simultaneously.

---

## How To Run

**Full Phase 8 screener (signals + paper trading update):**
```bash
pip install yfinance openpyxl pandas numpy xgboost scikit-learn
python3 goofy_screener_phase8.py --market ALL
```

**Single market:**
```bash
python3 goofy_screener_phase8.py --market US
python3 goofy_screener_phase8.py --market ASX
python3 goofy_screener_phase8.py --market JPX
```

**Walk-forward validation (Phase 9):**
Run the Jupyter notebook: `Goofy Phase 9 — Walk-Forward Validation.ipynb`

**Output:** Colour-coded Excel report saved to `screener_output/`

---

## File Structure

```
goofy_screener_phase8.py   — Main weekly screener (all phases combined)
ml_signal.py               — Phase 7: XGBoost feature engineering + model
paper_trader.py            — Phase 8: paper trade logging and P&L tracking
portfolio_builder.py       — Phase 6: correlation matrix + cluster sizing
position_sizer.py          — Phase 5: Kelly Criterion + vol scaling
regime_detector.py         — Phase 4: market regime detection

paper_trades/
  trades_log.json          — Persistent paper trade log (open + closed)

screener_output/
  Goofy_Phase8_YYYY-MM-DD.xlsx   — Weekly Excel report
  phase9_walkforward.png         — Walk-forward validation chart

Goofy Phase 9 — Walk-Forward Validation.ipynb
Goofy Phase 8 — Paper Trading.ipynb
Goofy Phase 7 — ML Signal.ipynb
Goofy Phase 6 — Portfolio Construction.ipynb
Goofy Phase 5 — Position Sizing.ipynb
```

---

## Automated Weekly Run

The screener runs every Sunday 9am Brisbane (AEST) and Monday–Friday 7am Brisbane via scheduled remote agents on Anthropic cloud infrastructure. No local machine required.

---

## Background

Built while studying Finance at the University of Queensland. No prior programming experience at the start of this project. Each phase added one layer of institutional-grade methodology on top of the last.

The goal was to understand *why* each component exists — not just to implement it.

---

*GoofyisDAWG — Brisbane, Australia*
