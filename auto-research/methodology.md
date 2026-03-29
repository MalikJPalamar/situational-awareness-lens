# Methodology v1.1 — First Experiment

## Changes from v1.0
- **Added:** Directional bias per ticker (🟢 LONG / 🟡 WATCH / 🔴 AVOID / 🟢 ACCUMULATE / 🟢 CONTRARIAN / 🟡 SPECULATIVE)
- **Added:** Timeframe per ticker (Swing 2-4wk / Position 3-6mo / Long-term 12mo+)
- **Added:** Explicit risk factor per ticker
- **Added:** Support/resistance levels per ticker
- **Added:** Macro overlay section (VIX, DXY, 10Y yield, WTI, Brent, S&P 500, Nasdaq)
- **Added:** Sector correlation table with rotation signal
- **Added:** Enhanced Aschenbrenner thesis alignment with priority ranking

## Report Structure

1. **Macro Overlay** — VIX, DXY, 10Y yield, WTI crude, Brent, S&P 500, Nasdaq + SA impact
2. **Momentum Leaderboard** — All 18 tickers ranked with bias, timeframe, RSI, signal
3. **Sector Breakdown** — Each of the 4 SA sectors with per-ticker analysis
4. **Per-Ticker Analysis** includes:
   - Price, 52W range, RSI, MACD, SMA 20/50/200, EMA
   - Support/resistance levels
   - **Directional bias** with reasoning
   - **Timeframe** (swing / position / long-term)
   - **Risk factor** (specific, named)
   - Catalyst or trigger
5. **Alerts Section** — Overbought, oversold, golden cross, pullbacks, VIX, macro
6. **Sector Correlation Table** — Cross-sector dynamics + rotation signal
7. **Aschenbrenner Thesis Alignment** — Priority ranking of sectors
8. **Earnings Calendar** — embedded in per-ticker sections

## Signal Classification (updated)

- **🟢 LONG**: Clear bullish setup — technicals, fundamentals, and catalyst aligned
- **🟢 ACCUMULATE**: Longer-term bullish, currently in a dip/pullback buy zone
- **🟢 CONTRARIAN**: Deep value / oversold with thesis intact, higher risk
- **🟡 WATCH**: No clear edge — conflicting signals or waiting for confirmation
- **🟡 SPECULATIVE**: High-conviction thesis but pre-revenue or binary outcome
- **🔴 AVOID**: Bearish technicals, deteriorating fundamentals, or risk/reward skewed negative

## Data Sources (Priority Order)

1. Google Finance (real-time quotes)
2. TipRanks (technical indicators, analyst consensus)
3. Investing.com (RSI, MACD, moving averages, macro data)
4. TradingView (chart signals, technical ratings)
5. Yahoo Finance (fundamentals, earnings, news)
6. Schwab Market Update (macro context, market structure)
7. CNBC (macro, bonds, yields)
8. StockAnalysis.com (overview, news)
9. Robinhood (intraday range, volume)

## Limitations (v1.1)

- No formal news sentiment scoring (-2 to +2) yet
- No options flow / unusual activity analysis
- Accuracy Score still at 0.0 (needs 5 trading days of history)
- Macro overlay is present but not yet scored per-ticker impact
