# Methodology v1.0 — Baseline

## Report Structure

1. **Momentum Leaderboard** — All 18 tickers ranked by composite signal strength
2. **Sector Breakdown** — Each of the 4 SA sectors with per-ticker analysis
3. **Per-Ticker Analysis** includes:
   - Price and daily change
   - 52-week range
   - RSI (14), MACD (12,26), SMA 20/50/200, EMA 9/20
   - Directional signal: 🟢 Bullish / 🟡 Neutral / 🔴 Bearish
   - Key signal summary (text)
4. **Alerts Section** — Overbought, oversold, golden cross, death cross, deep pullbacks
5. **Macro Context** — Aschenbrenner thesis alignment paragraph
6. **Earnings Calendar** — Upcoming earnings dates for all tickers

## Data Sources (Priority Order)

1. Google Finance (real-time quotes, cross-referenced prices)
2. TipRanks (technical indicators, analyst targets)
3. Investing.com (RSI, MACD, moving averages, daily signals)
4. TradingView (chart signals, community analysis)
5. Yahoo Finance (fundamentals, earnings, news)
6. StockAnalysis.com (overview, news aggregation)
7. Robinhood (intraday range, volume)

## Signal Classification

- **🟢 Bullish**: RSI < 40 with MACD buy signal, OR price above SMA50 + MACD bullish, OR strong catalyst
- **🔴 Bearish**: RSI > 70 with MACD sell signal, OR price below SMA50 + SMA200, OR negative catalyst
- **🟡 Neutral**: Everything else, or conflicting signals

## Limitations (v1.0)

- No support/resistance levels yet
- No explicit entry/exit zones
- No sector correlation analysis
- No news sentiment scoring
- No options flow
- No macro overlays (DXY, VIX, yields, oil)
- Accuracy scoring starts after 5 trading days of history
