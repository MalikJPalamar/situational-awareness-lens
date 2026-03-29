# Situational Awareness Portfolio — Autoresearch Program

> Adapted from [karpathy/autoresearch](https://github.com/karpathy/autoresearch) methodology.
> The core loop: **Hypothesis → Experiment → Measure → Keep or Discard → Repeat.**

## Purpose

This is the governing methodology for daily autonomous stock analysis of the Situational Awareness portfolio — 18 tickers mapped to Leopold Aschenbrenner's AI scaling thesis across four sectors. The analyst (Claude) runs this program daily, evolving the analysis methodology over time to produce increasingly accurate, actionable, and comprehensive reports.

---

## The Portfolio (Fixed — Do Not Modify)

| Sector | Thesis | Tickers |
|--------|--------|---------|
| 🧠 AI Compute | Hyperscalers racing toward AGI | NVDA, AMD, GOOGL, MSFT, META |
| 💾 Semiconductors | Fab, lithography, equipment moats | TSM, ASML, AMAT, LRCX |
| ⚡ Energy for AI | Nuclear renaissance, grid-scale power | VST, CEG, OKLO, NRG, SMR |
| 🛡️ Defense AI | Intelligence platforms, autonomous systems | PLTR, BAH, LMT, RTX |

---

## File Structure

```
program.md              ← This file. The "research org code." Human edits this.
auto-research/
  YYYY-MM-DD-report.md  ← Daily report (analyst generates this)
  latest.md             ← Copy of most recent report
  results.tsv           ← Experiment log (keep/discard history)
  methodology.md        ← Current active methodology version (analyst evolves this)
  changelog.md          ← Log of all methodology changes with rationale
daily-scans/            ← Reserved for future use
```

---

## The Optimization Metric: Composite Score (CS)

Our equivalent of autoresearch's `val_bpb`. Every daily report is scored on three dimensions, each 0–100:

### 1. Accuracy Score (AS) — "Were the signals right?"

Measured with a **5-day lookback**. For each ticker's signal from 5 trading days ago:

| Signal Given | Price moved in signal direction within 5 days? | Points |
|---|---|---|
| 🟢 Bullish | Price rose ≥1% | +1 |
| 🟢 Bullish | Price fell ≥1% | -1 |
| 🔴 Bearish | Price fell ≥1% | +1 |
| 🔴 Bearish | Price rose ≥1% | -1 |
| 🟡 Neutral | Price moved <1% either way | +1 |
| 🟡 Neutral | Price moved >3% either way | -1 |

**AS = (correct signals / total signals) × 100**

### 2. Actionability Score (AC) — "Could you trade on this?"

Self-assessed per report. Each ticker earns 0–5 points based on:

- Clear directional bias stated (not just data) → +1
- Specific entry/exit levels or zones identified → +1
- Risk factor explicitly named → +1
- Timeframe specified (swing, position, watch) → +1
- Catalyst or trigger identified → +1

**AC = (total points earned / max possible points) × 100**

### 3. Coverage Score (CV) — "How deep is the research?"

Points per research layer present in the report:

| Layer | Points | Description |
|---|---|---|
| Price + daily change | 1 | Basic quote data |
| Technical indicators (RSI, MACD, MAs) | 2 | Standard TA |
| Support/resistance levels | 1 | Key price zones |
| Sector correlation analysis | 2 | Cross-sector dynamics |
| News sentiment scoring | 2 | Per-ticker news impact |
| Options flow / unusual activity | 2 | Smart money signals |
| Macro overlays (DXY, oil, VIX, yields) | 2 | Macro context |
| Earnings calendar + estimates | 1 | Forward catalysts |
| Aschenbrenner thesis alignment | 2 | SA framework connection |

**CV = (points earned / 15) × 100**

### Composite Score

**CS = (0.40 × AS) + (0.30 × AC) + (0.30 × CV)**

Target: Start at whatever baseline scores, improve monotonically over time.

---

## The Experiment Loop

### Daily Run (The "Training Loop")

Each day (weekdays, after US market close):

1. **Read current state**: Check `methodology.md` for active methodology version. Check `results.tsv` for recent scores and trends.

2. **Score yesterday's signals** (5-day lookback): Pull prices for tickers that had signals 5 trading days ago. Calculate Accuracy Score. Log to `results.tsv`.

3. **Research**: For all 18 tickers, gather:
   - Real-time prices and daily changes (web search)
   - Technical indicators from multiple sources (TipRanks, Investing.com, TradingView, Barchart)
   - Recent news and sentiment (web search)
   - Any active research layers from current methodology version

4. **Generate report**: Write `YYYY-MM-DD-report.md` following the active methodology template.

5. **Self-score**: Calculate AC and CV for today's report. Log to `results.tsv`.

6. **Propose methodology experiments**: Based on score trends, propose ONE change to test in the next report. This follows the keep/discard loop below.

7. **Push to repo**: Commit report + updated results.tsv + any methodology changes to `auto-research/` in the GitHub repo.

### The Keep/Discard Loop (Methodology Evolution)

This is the autoresearch core — how the analysis gets better over time.

**What the analyst auto-evolves (no approval needed):**
- Report formatting and layout
- Which data sources to query (adding/removing search sources)
- How data is presented (tables vs prose vs charts)
- Depth of coverage per ticker (more/less detail based on what moves)
- Source prioritization (which sites give best data)

**What requires human approval (framework changes):**
- Adding or removing a research layer (e.g., adding options flow analysis)
- Changing the scoring weights in the Composite Score
- Adding or removing tickers from the portfolio
- Changing signal classification logic
- Modifying the Aschenbrenner thesis mapping

**The loop:**

```
FOR EACH methodology experiment:
  1. Describe the change in changelog.md (hypothesis + expected impact)
  2. If auto-evolve category → apply immediately, measure CS delta over 3 reports
  3. If framework change → propose to human, wait for approval
  4. After 3 reports with the change:
     - IF CS improved → KEEP (update methodology.md, log "keep" in results.tsv)
     - IF CS unchanged → KEEP only if simpler (simplicity criterion)
     - IF CS decreased → DISCARD (revert methodology.md, log "discard" in results.tsv)
```

**Simplicity criterion** (from Karpathy): All else being equal, simpler is better. A marginal improvement that adds ugly complexity to the report — not worth it. A simplification that maintains the same score — definitely keep.

---

## Results Tracking: results.tsv

Tab-separated. Updated every report.

```
date	report_version	cs_score	as_score	ac_score	cv_score	methodology_version	experiment	status	description
```

Example:
```
date	report_version	cs_score	as_score	ac_score	cv_score	methodology_version	experiment	status	description
2026-03-28	v1	0.0	0.0	42.0	40.0	v1.0	baseline	keep	Initial report - price + technicals + alerts
2026-03-31	v2	0.0	0.0	48.0	46.7	v1.1	format-001	keep	Added explicit directional bias per ticker
2026-04-01	v3	0.0	0.0	52.0	53.3	v1.1	format-002	keep	Added support/resistance zones
```

Note: AS (Accuracy Score) starts at 0.0 because we need 5 trading days of history before we can score signal accuracy. It will populate starting ~day 6.

---

## Research Layers — Rollout Sequence

These are added incrementally, one at a time, following the keep/discard discipline.

### Phase 1 — Foundation (Current)
- [x] Price + daily change
- [x] Technical indicators (RSI, MACD, SMA/EMA)
- [x] Alerts (overbought/oversold, golden cross)
- [x] Earnings calendar
- [x] Aschenbrenner thesis alignment

### Phase 2 — Depth (Next experiments)
- [ ] Support/resistance levels per ticker
- [ ] Explicit directional bias + timeframe per ticker
- [ ] Risk factor per ticker
- [ ] Entry/exit zone suggestions

### Phase 3 — New Layers (Requires approval)
- [ ] **Sector correlation analysis**: How do the four SA sectors move relative to each other? Are there divergences?
- [ ] **News sentiment scoring**: Per-ticker sentiment from recent headlines (-2 to +2 scale)
- [ ] **Macro overlays**: DXY, 10Y yield, WTI crude, VIX — and how they map to SA sectors
- [ ] **Options flow**: Unusual volume, put/call ratios, large block trades

### Phase 4 — Advanced (Future)
- [ ] Cross-sector rotation signals (capital flowing from one SA sector to another)
- [ ] Relative strength ranking within each sector
- [ ] Correlation breakdown alerts (when normally correlated assets diverge)
- [ ] Catalyst calendar (regulatory decisions, product launches, earnings, geopolitical events)

---

## Rules

1. **Never fabricate data.** Every price, indicator, and metric must come from a verifiable web source fetched during the analysis run.
2. **Never stop improving.** If scores plateau, try harder — read more sources, try different analytical angles, combine near-miss experiments.
3. **One experiment at a time.** Don't change three things and measure. Change one thing, measure, keep or discard.
4. **Log everything.** Every methodology change goes in `changelog.md`. Every report score goes in `results.tsv`.
5. **The human sleeps.** When told "run SA scan," execute the full loop without asking permission. Push results. The human reviews when they wake up.
6. **Rotate the PAT.** Never store or log the GitHub PAT in any committed file.

---

## How to Trigger a Run

The human says any of:
- "Run SA scan"
- "Daily SA report"
- "Run the situational awareness analysis"
- "Push today's report"

The analyst then executes the full daily loop above and pushes to `MalikJPalamar/situational-awareness-lens`.

---

*This program.md is the "research org code." The human iterates on this document. The analyst iterates on the methodology within the boundaries this document defines. Together, the analysis gets better every day.*
