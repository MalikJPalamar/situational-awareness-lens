# Methodology Changelog

All changes to the SA analysis methodology, with rationale and outcome.

---

## v1.1 — 2026-03-29 — Experiment: Directional Bias + Macro Overlay

**Type:** Auto-evolve (format/source change — no framework approval needed)
**Status:** pending (measuring over 3 reports)
**Experiment ID:** format-001

**Hypothesis:** Adding explicit directional bias, timeframe, risk factor, support/resistance, macro overlay, and sector correlation will significantly improve both Actionability (AC) and Coverage (CV) scores.

**Changes:**
- Added directional bias per ticker with 6-tier classification
- Added timeframe per ticker (swing / position / long-term)
- Added explicit risk factor per ticker
- Added support/resistance levels per ticker
- Added macro overlay section (VIX, DXY, yields, oil, indices)
- Added sector correlation table with rotation signal
- Enhanced Aschenbrenner thesis alignment with sector priority ranking
- Added Schwab Market Update and CNBC as data sources

**Self-Score:**
- Accuracy (AS): 0.0 (no history yet — starts populating ~April 4)
- Actionability (AC): 78.9 (↑ from 42.0 — directional bias + levels drove this)
- Coverage (CV): 73.3 (↑ from 40.0 — macro overlay + sector correlation added 6 points)
- **Composite (CS): 45.7** (↑ from 24.6 — +85.8% improvement)

**Assessment:** Massive CS improvement. AC nearly doubled. CV jumped 33 points. The report is now meaningfully more actionable — every ticker has a clear bias, most have entry zones and risk factors. Macro overlay adds crucial context (war regime, VIX >30, rising yields).

**Next experiment planned:** Add formal per-ticker news sentiment scoring (-2 to +2) to improve CV further. This is an auto-evolve change (adding a data layer, not changing the framework).

---

## v1.0 — 2026-03-28 — Baseline

**Type:** Initial setup
**Status:** keep
**Description:** First report generated. Covers all 18 tickers with price, technicals (RSI, MACD, MAs), alerts, and Aschenbrenner thesis alignment. No directional bias per ticker, no support/resistance, no macro overlay.

**Self-Score:**
- Accuracy (AS): 0.0 (no history yet)
- Actionability (AC): 42.0
- Coverage (CV): 40.0
- **Composite (CS): 24.6** (baseline)
