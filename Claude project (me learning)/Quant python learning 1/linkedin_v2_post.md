# LinkedIn Post — Screener v2

---

Most people pick a strategy and apply it everywhere.

That's the wrong approach.

After backtesting 5 quantitative strategies individually — Moving Average, RSI, Bollinger Bands, MACD, and Mean Reversion — I built an automated screener that asks a different question:

**For each asset, which strategy actually fits?**

Here's how it works:

→ Downloads live price data via Yahoo Finance
→ Trains on 2016–2020 data only (parameters locked after this step)
→ Applies the best strategy to 2021–present data the model never saw
→ Compares against Buy & Hold on Sharpe ratio, total return, and max drawdown
→ Exports a colour-coded Excel report automatically

The out-of-sample results confirmed what I suspected:

The same Bollinger Bands strategy that produced a **Sharpe of 0.87** on Sony (6758.T) produced **−0.12 on NVDA**. Inverse. The same asset class, completely different character.

NVDA needed MACD. Sony needed Bollinger Bands. CBA.AX worked best with RSI. BHP.AX failed everything — commodity stocks driven by macro cycles don't fit rule-based strategies cleanly.

**The lesson isn't which strategy is best. It's that strategy-asset fit matters more than the strategy itself.**

One other finding worth sharing: most strategies don't beat Buy & Hold on raw return in a bull market — and that's expected, not a failure. 2021–2026 was a strong uptrend. What the strategies did deliver was drawdown protection. The difference between a −12% strategy drawdown and a −58% Buy & Hold drawdown on the same asset is the real value for risk-conscious investors.

Code is open source on GitHub — link in comments.

Currently building Phase 3: expanding the screener to screen US, Australian, and Japanese markets simultaneously with a composite scoring and tier ranking system.

---

#QuantitativeFinance #AlgorithmicTrading #Python #Backtesting #SystematicTrading #Finance #UQ #FinancialEngineering
