# LinkedIn Post — Phase 3 Multi-Market Screener

---

I just ran my Phase 3 quant screener across 119 stocks simultaneously — US, Australia, and Japan in a single automated run.

Here's what came out.

**The setup:**
5 strategies × full parameter grid search × 119 assets
Train: 2016–2020 | Test: 2021–present (out-of-sample only)
Each asset scored 0–100 on a composite of Sharpe ratio, total return, drawdown protection, and DD saved vs Buy & Hold
Ranked into tiers: S / A / B / Skip

**What I didn't expect:**

Japan dominated.

4 of the top 5 out-of-sample Sharpe results came from JPX financial stocks — specifically banking and insurance companies like MS&AD Insurance (8725.T) hitting **Sharpe 1.31** with RSI, and Mitsubishi Heavy Industries (7011.T) at **Sharpe 1.10** with MACD.

Japanese financials have a cleaner oscillation pattern than their US and Australian equivalents. The Bank of Japan's policy environment in 2021–2024 created well-defined momentum cycles that RSI and MACD captured consistently.

**Sony confirmed what I found in Phase 1:**

Bollinger Bands Sharpe held at **0.87 out-of-sample** — one of the most stable results in the entire project. Sony (6758.T) is structurally mean-reverting. Same strategy, same parameters, validated across the full test period.

**The strategy distribution across 119 assets:**

MA Crossover won 34 assets (29%) — mostly trending US and ASX stocks
MACD won 27 (23%) — growth and momentum names
RSI won 24 (20%) — banks and stable dividend payers
Bollinger Bands won 22 (18%) — range-bound and mean-reverting assets
Mean Reversion won 12 (10%) — sideways-market candidates

**One honest observation:**

Most strategies still don't beat Buy & Hold on raw return — and in a 2021–2026 bull market, that's the expected outcome, not a failure. The screener's real signal is in the drawdown column. Several S-tier assets show strategy max drawdowns of −12% to −18% while the same assets held passively dropped −35% to −70% over the same period. For risk-managed capital, that gap is the entire point.

The notebook, script, and one-click launcher are open source on GitHub — link in comments.

---

**What I'm building next — Phase 4:**

The gap between what I have and how professional quant funds actually operate isn't the indicator. It's everything around it.

Next phase: regime detection. Classify each market as trending, mean-reverting, or choppy — then only deploy strategies suited to that regime. The biggest flaw I found in this project is mean reversion strategies dying in bull markets. The fix isn't a better mean reversion strategy. It's knowing when not to use it.

After that: volatility-scaled position sizing with Kelly Criterion. Right now every trade is 100% in or out. That's not how real risk management works.

Still learning. Always building.

---

#QuantitativeFinance #AlgorithmicTrading #Python #JapaneseStocks #SystematicTrading #Backtesting #Finance #FinancialEngineering #UQ #Nikkei #ASX
