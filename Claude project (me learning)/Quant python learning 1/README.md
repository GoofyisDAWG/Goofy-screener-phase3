# Goofy Screener — Multi-Market Quant Strategy System

A quantitative trading research project built from scratch in Python.  
Five strategies individually backtested, then combined into an automated screener that runs across **117 assets across US, ASX, and JPX markets** — picking the best strategy per asset and ranking by risk-adjusted performance.

> **Core finding:** Strategy-asset fit matters more than the strategy itself. The same strategy that produces a Sharpe of 0.87 on one stock produces -0.21 on another. The screener exists to find which combination actually works — and proves it on data the model never saw.

---

## Project Structure

```
├── Goofy MA for 6 assets.ipynb             — Strategy 1: MA Crossover
├── Goofy8 RSI for 6 assets.ipynb           — Strategy 2: RSI
├── Goofy BB for 6 assets.ipynb             — Strategy 3: Bollinger Bands
├── Goofy MACD for 6 assets.ipynb           — Strategy 4: MACD
├── Goofy Mean Reversion for 6 assets.ipynb — Strategy 5: Mean Reversion
├── goofy_screener_daily.py                 — Automated daily screener (Phase 3)
├── screener_output/                        — Excel reports (auto-generated)
└── README.md
```

---

## Methodology

All strategies share the same validation framework:

| | Detail |
|---|---|
| **Train period** | Jan 2016 → Dec 2020 (in-sample) |
| **Test period** | Jan 2021 → present (out-of-sample) |
| **Parameter selection** | Grid search on training data only — locked before testing |
| **Signal execution** | `.shift(1)` on all signals — zero lookahead bias |
| **Sharpe ratio** | Annualised return ÷ annualised volatility |
| **Max drawdown** | Peak-to-trough on cumulative equity curve |
| **Benchmark** | Buy & Hold (passive holding of the same asset) |

Parameters are chosen on historical data only, then frozen and applied to future data the model never saw. This is the core of the methodology — what separates genuine signal from overfitting.

---

## The 5 Strategies

### 1 — Moving Average Crossover
Goes long when the fast MA crosses above the slow MA; exits on the reverse. Pure trend-following.  
**Best fit:** SPY, broad indices, macro ETFs  
**Worst fit:** Mean-reverting stocks (generates whipsaws)

### 2 — RSI (Relative Strength Index)
Buys on oversold dips, sells on overbought spikes. Captures momentum extremes.  
**Best fit:** CBA.AX, NAB.AX, stable dividend stocks  
**Worst fit:** NVDA-style trending assets (kept triggering premature sells during the AI bull run)

### 3 — Bollinger Bands
Buys when price breaks below the lower band (2σ); sells at the upper band. Volatility-adjusted mean reversion.  
**Best fit:** Sony (6758.T) — the only strategy-asset pair to beat Buy & Hold out-of-sample in the entire project  
**Worst fit:** NVDA (negative Sharpe OOS — parabolic trends never mean-revert to a 20-day average)

### 4 — MACD
Goes long when MACD crosses above its signal line. Hybrid momentum + trend.  
**Best fit:** NVDA, trending US growth stocks, Japanese financials  
**Standout:** NVDA is the only asset where OOS Sharpe *exceeded* in-sample (0.94 → 1.03)

### 5 — Mean Reversion (Z-Score)
Goes long when price drops N standard deviations below its rolling mean; exits at mean.  
**Best fit:** Sideways / range-bound markets  
**Key limitation:** Structurally underperforms in bull markets — 2021–2026 was the wrong regime for this strategy

---

## Phase 3 Screener — 117 Assets, 3 Markets

**File:** `goofy_screener_daily.py`

The screener automates all 5 strategies into a single pipeline across three global markets:

1. Downloads live price data via Yahoo Finance
2. Splits into train (2016–2020) and test (2021–present)
3. Grid searches all strategies and parameters on training data only
4. Selects the best strategy per asset by in-sample Sharpe
5. Applies that strategy to out-of-sample data — data the model never touched
6. Scores each asset by a weighted formula: Sharpe, return, drawdown protection
7. Assigns tiers and exports a colour-coded Excel report

### Asset Universe

| Market | Count | Examples |
|--------|-------|---------|
| 🇺🇸 US | ~40 | NVDA, TSLA, AAPL, JPM, XOM, GLD, SPY |
| 🇦🇺 ASX | ~37 | CBA.AX, BHP.AX, CSL.AX, WTC.AX, STW.AX |
| 🇯🇵 JPX | ~40 | 7203.T, 6758.T, 8306.T, 9984.T, 8725.T |

### Tier System

| Tier | Criteria |
|------|---------|
| ⭐ S — Excellent | Sharpe ≥ 0.8, Return ≥ 30%, Max DD ≥ -20% |
| ✅ A — Good | Sharpe ≥ 0.4, Return ≥ 10%, Max DD ≥ -35% |
| 🔵 B — Decent | Sharpe ≥ 0.1, Max DD ≥ -50% |
| ⬜ Skip | Below all thresholds |

### Latest Results (Run: 2026-04-12)

**117 assets screened | 12 S-tier | 30 A-tier | 22 assets beat Buy & Hold**

**Top 5 by Out-of-Sample Sharpe:**

| Rank | Market | Asset | Strategy | OUT Sharpe | Tier |
|------|--------|-------|---------|-----------|------|
| 1 | JPX | 8725.T | RSI | 1.306 | ⭐ S |
| 2 | JPX | 8002.T | MA Crossover | 1.276 | 🔵 B |
| 3 | JPX | 9434.T | Bollinger Bands | 1.151 | ✅ A |
| 4 | US | COP | RSI | 1.121 | ✅ A |
| 5 | JPX | 7011.T | MACD | 1.104 | ✅ A |

**Strategy distribution across 117 assets:**

| Strategy | Count |
|----------|-------|
| MA Crossover | 34 |
| MACD | 27 |
| RSI | 24 |
| Bollinger Bands | 22 |
| Mean Reversion | 10 |

### Why only 22/117 beat Buy & Hold — and why that's honest

2021–2026 was a strong bull market. In sustained uptrends, passive holding wins on raw return because rule-based strategies move to cash during corrections and miss part of the recovery. This is not a failure — it is an accurate reflection of what quant strategies do in trending markets.

The real value is **drawdown protection**. Top assets show strategy drawdowns of -10% to -25% while the same assets held passively experienced -30% to -75% drops over the same period. For risk-conscious investors, a strategy that earns less but caps the worst drop at -15% is genuinely useful.

---

## Key Findings

**1. Strategy-asset fit is everything.**  
Sony beat Buy & Hold with Bollinger Bands (Sharpe 0.87) but failed with MACD (Sharpe 0.31). Same asset, wrong strategy.

**2. Out-of-sample validation is non-negotiable.**  
Sony's Mean Reversion in-sample Sharpe was 1.34 — the highest single result in the whole project. Out-of-sample it fell to 0.41. Without the train/test split that looks like the best strategy in the study. It isn't.

**3. Regime determines performance.**  
Mean Reversion underperformed across the board because 2021–2026 was trending. The same strategies in a sideways market would tell a completely different story.

**4. Japanese markets showed the strongest signals.**  
JPX financials (8725.T, 8411.T, 8750.T, 8306.T) dominated the top Sharpe rankings. Japanese bank stocks have cleaner oscillation patterns that suit RSI and MACD well.

**5. Sharpe matters more than raw return.**  
CBA's RSI strategy returned +52.7% with Sharpe 0.99 and max drawdown -11%. Buy & Hold returned +167% but with -35% drawdown. Which is better depends entirely on risk tolerance.

---

## How to Run

### Requirements
```bash
pip install yfinance pandas numpy openpyxl
```

### Run the screener
```bash
python goofy_screener_daily.py
```

Output saved to `screener_output/Goofy_Phase3_YYYY-MM-DD.xlsx`

### Individual strategy notebooks
Open any `.ipynb` file in Jupyter. Each notebook is self-contained with its own data download, backtest, and charts.

---

## Honest Limitations

- **No transaction costs.** Brokerage and slippage would reduce returns, especially for high-frequency strategies.
- **Sharpe without risk-free rate.** Subtracting cash rates (~4–5%) would reduce headline Sharpe by roughly 0.3–0.5.
- **Multiple comparisons.** Selecting the best of 5 strategies introduces selection bias even with a proper train/test split.
- **Long-only.** All strategies are long or flat. No short selling.
- **No portfolio construction.** No correlation analysis or position sizing across assets.

---

## Disclaimer

This project is for educational and research purposes only. All backtested results are historical simulations and do not guarantee future performance. Nothing in this repository constitutes financial advice.

---

*Built by Hiroki Kunu — International Finance, University of Queensland*  
*GitHub: [GoofyisDAWG](https://github.com/GoofyisDAWG) | LinkedIn: [Hiroki Kunu](https://www.linkedin.com/in/hiroki-kunu-ba4218401)*
