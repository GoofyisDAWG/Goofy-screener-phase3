# LinkedIn Post — Phase 4: Regime Detection + Empirical Gate Search

---

I built a theory. Then I let the data destroy it.

Phase 4 of my quant screener project was supposed to be simple: detect market regimes (Bull / Sideways / Bear) and gate strategies to only run in their "preferred" regime.

My theory going in: MA Crossover should only trade in Bull markets. RSI and Bollinger Bands should trade in Sideways markets. Logical, right?

I was wrong.

---

**What I built in Phase 4a:**

A rule-based regime detector using 200-day MA slope for trend and rolling ATR percentile for volatility. Labels every trading day as Bull, Sideways, or Bear for any asset.

First tested on NVDA, CBA.AX (Commonwealth Bank), and Toyota (7203.T).

NVDA looked like my theory was right — Bull gate improved MA Crossover Sharpe from 0.94 → 1.02 and cut maximum drawdown from −63% to −45%.

Then I tested CBA.AX.

Bull gate: +9% → **−26%**. Catastrophic.
Sideways gate: +9% → +10.7%, max drawdown cut from −34% to −12%. Every metric improved.

Same strategy. Same gate type. Opposite direction.

---

**Why it happens (my hypothesis):**

CBA.AX and 7203.T both spend ~49% of time in Bull, ~41% Sideways — nearly identical regime distributions. Yet they respond to the same gate in opposite directions.

CBA.AX is an Australian dividend bank. Its "Bull" periods are slow upward grinds full of whipsaws — exactly where MA Crossover gets destroyed. Its "Sideways" periods contain the sharper, short-duration moves the strategy actually catches.

**You cannot predict optimal gate from regime distribution alone. You have to measure it.**

---

**Phase 4b — I ran the full experiment:**

6 assets × 5 strategies × 4 gate options = **120 backtests**

Winner rule: highest Sharpe. Three safeguards against overfitting:
- Must beat ungated by >0.05 Sharpe (otherwise complexity isn't worth it)
- Must deliver positive Sharpe (gating can't rescue a broken strategy)
- Regime must occur on ≥15% of trading days (rare regimes = overfit Sharpe)

That third guard caught 20+ false positives. CBA.AX MA Crossover in Bear markets showed Sharpe 0.59 — impressive. But Bear only happens 2.6% of the time on CBA.AX. That Sharpe isn't real. It's noise from 6 trades in 10 years.

**Final empirical result:**

Only one gate type won anywhere: **Sideways**, and only for trend-following strategies (MA, MACD) on range-bound Asian and Australian stocks.

US assets — NVDA, SPY — ungated wins every time. Strong trending assets don't need help.

My original "MA → Bull" theory? Confirmed for zero of six assets in the final empirical test.

---

**What this taught me:**

The gap between theory and evidence in quant research is where most mistakes live. I spent time building a regime detector based on intuition about how markets "should" work, and the data told me I was wrong in a systematic way.

The right workflow isn't: form theory → implement → deploy.
It's: form theory → implement → test empirically → let the data override the theory → deploy with the right safeguards against overfitting.

Three files, two notebooks, one JSON of empirical results — and a much better understanding of why professional quant funds separate alpha research from execution logic.

---

**Next: Phase 5 — position sizing.**

Right now every trade is 100% in or out. Kelly Criterion and volatility scaling are the next layer. Equal position sizing is leaving risk-adjusted return on the table.

Still learning. Always building.

Open source on GitHub — link in comments.

---

#QuantitativeFinance #AlgorithmicTrading #Python #SystematicTrading #Backtesting #MachineLearning #Finance #UQ #Brisbane #Internship #QuantResearch #RegimeDetection
