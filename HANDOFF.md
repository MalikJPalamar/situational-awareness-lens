# 🧬 Situational Awareness Portfolio — Handoff Document

> **Created:** 2026-03-31
> **Author:** Claude (Opus 4.6) in claude.ai conversation with Malik Palamar
> **Purpose:** Full context transfer to Claude Dev or Claude Code for autonomous continuation
> **Status:** Active system — Daily Report Loop + Auto-Research Loop operational

---

## 1. WHAT THIS IS

An autonomous daily stock analysis system tracking 18 tickers mapped to Leopold Aschenbrenner's *Situational Awareness* thesis about the AI scaling race. The system uses a methodology adapted from [karpathy/autoresearch](https://github.com/karpathy/autoresearch) — a keep/discard experimentation loop that makes the analysis measurably better over time.

**Two loops run daily:**

```
┌─────────────────────────────────────────────────────┐
│  LOOP 1: Daily Report Loop (9pm CET, weekdays)      │
│                                                      │
│  Web search → 18 tickers × real data → Generate     │
│  report → Self-score → Push to Supabase              │
└──────────────────────┬──────────────────────────────┘
                       │ writes data
                       ▼
              ┌────────────────┐
              │   SUPABASE DB  │
              │  (source of    │
              │   truth)       │
              └────────┬───────┘
                       │ reads data
                       ▼
┌─────────────────────────────────────────────────────┐
│  LOOP 2: Auto-Research Loop (continuous)             │
│                                                      │
│  Read accumulated data → Score past signals →        │
│  Evaluate methodology → Propose experiment →         │
│  Keep/discard → Update methodology → Write back      │
└─────────────────────────────────────────────────────┘
```

**End goal:** Over weeks/months, accumulated data + evolving methodology → increasingly accurate predictions with quantified confidence levels.

---

## 2. THE PORTFOLIO (Fixed)

| Sector | Aschenbrenner Thesis | Tickers |
|--------|---------------------|---------|
| 🧠 AI Compute | Hyperscalers racing toward AGI | NVDA, AMD, GOOGL, MSFT, META |
| 💾 Semiconductors | Fab, lithography, equipment moats | TSM, ASML, AMAT, LRCX |
| ⚡ Energy for AI | Nuclear renaissance, grid-scale power | VST, CEG, OKLO, NRG, SMR |
| 🛡️ Defense AI | Intelligence platforms, autonomous systems | PLTR, BAH, LMT, RTX |

**18 tickers total.** Do not add/remove without Malik's explicit approval (this is a "framework change" per the methodology).

---

## 3. CURRENT STATE

### GitHub Repo
- **URL:** https://github.com/MalikJPalamar/situational-awareness-lens
- **Branch:** `main`
- **Structure:**
  ```
  program.md                        ← Governing methodology (human edits)
  auto-research/
    2026-03-28-report.md            ← First report (v1, baseline)
    2026-03-29-report.md            ← Second report (v2, first experiment)
    latest.md                       ← Copy of most recent report
    methodology.md                  ← Active methodology v1.1
    changelog.md                    ← All methodology changes + outcomes
    results.tsv                     ← Experiment log with Composite Scores
  daily-scans/                      ← Reserved (placeholder file)
  ```

### Methodology Version: v1.1
The report template currently includes:
- Macro overlay (VIX, DXY, 10Y yield, WTI, Brent, S&P 500, Nasdaq)
- Per-ticker: price, 52W range, RSI, MACD, SMA/EMA, support/resistance
- Per-ticker: directional bias (6-tier), timeframe, risk factor, catalyst
- Sector correlation table + rotation signal
- Aschenbrenner thesis alignment with sector priority ranking
- Alerts section

### Composite Score Tracking
| Date | Version | CS | AS | AC | CV | Experiment | Status |
|------|---------|----|----|----|----|-----------|--------|
| 2026-03-28 | v1 | 24.6 | 0.0 | 42.0 | 40.0 | baseline | keep |
| 2026-03-29 | v2 | 45.7 | 0.0 | 78.9 | 73.3 | format-001 | pending |

### Next Queued Experiment
- **format-002:** Add formal per-ticker news sentiment scoring (-2 to +2)
- **Type:** Auto-evolve (no approval needed)
- **Expected impact:** CV score → 80+ (adds 2 points from sentiment layer)

### Accuracy Scoring
- Starts populating around **April 4, 2026** (need 5 trading days of signal history)
- Requires: pulling prices for tickers that had directional signals 5 days prior, comparing actual move to predicted direction

---

## 4. SUPABASE ARCHITECTURE (NEW — TO BE BUILT)

### Decision: Move from GitHub to Supabase as Primary Data Store

**Why:** GitHub markdown files are good for human readability but bad for querying, accumulation, and machine learning. Supabase gives us a proper relational database where data compounds over time.

### Malik's Supabase Account

| Project | ID | Region | Status |
|---------|-----|--------|--------|
| sb1-dbzhwfdj | egvdaxscdxjfwazjadxb | us-west-1 | INACTIVE |
| cognitive-nexus | paeltbkdmjevvztcgbzs | eu-west-3 | INACTIVE |
| aob-executive-dashboard | cbguyrcrsuiqiummbdua | eu-west-3 | **ACTIVE** |

**Recommendation:** Either create a new Supabase project called `situational-awareness` or use the `cognitive-nexus` project (activate it — the name fits). Ask Malik which he prefers.

### Proposed Database Schema

```sql
-- Core ticker universe (static reference)
CREATE TABLE tickers (
  id SERIAL PRIMARY KEY,
  symbol TEXT UNIQUE NOT NULL,
  name TEXT NOT NULL,
  sector TEXT NOT NULL,        -- 'AI Compute', 'Semiconductors', 'Energy for AI', 'Defense AI'
  thesis TEXT,                 -- Aschenbrenner thesis for this sector
  active BOOLEAN DEFAULT true,
  created_at TIMESTAMPTZ DEFAULT now()
);

-- Daily price snapshots (Loop 1 writes this)
CREATE TABLE daily_prices (
  id SERIAL PRIMARY KEY,
  ticker_id INTEGER REFERENCES tickers(id),
  date DATE NOT NULL,
  price NUMERIC(12,2),
  daily_change_pct NUMERIC(6,2),
  high_52w NUMERIC(12,2),
  low_52w NUMERIC(12,2),
  volume BIGINT,
  market_cap NUMERIC(15,0),
  source TEXT,                 -- which website provided this data
  created_at TIMESTAMPTZ DEFAULT now(),
  UNIQUE(ticker_id, date)
);

-- Technical indicators per ticker per day (Loop 1 writes this)
CREATE TABLE technicals (
  id SERIAL PRIMARY KEY,
  ticker_id INTEGER REFERENCES tickers(id),
  date DATE NOT NULL,
  rsi_14 NUMERIC(6,2),
  macd_value NUMERIC(10,3),
  macd_signal NUMERIC(10,3),
  macd_histogram NUMERIC(10,3),
  macd_trend TEXT,             -- 'BULLISH', 'BEARISH', 'NEUTRAL'
  sma_20 NUMERIC(12,2),
  sma_50 NUMERIC(12,2),
  sma_200 NUMERIC(12,2),
  ema_9 NUMERIC(12,2),
  ema_20 NUMERIC(12,2),
  above_sma_20 BOOLEAN,
  above_sma_50 BOOLEAN,
  above_sma_200 BOOLEAN,
  support_level NUMERIC(12,2),
  resistance_level NUMERIC(12,2),
  created_at TIMESTAMPTZ DEFAULT now(),
  UNIQUE(ticker_id, date)
);

-- Signals generated per ticker per day (Loop 1 writes, Loop 2 scores)
CREATE TABLE signals (
  id SERIAL PRIMARY KEY,
  ticker_id INTEGER REFERENCES tickers(id),
  date DATE NOT NULL,
  directional_bias TEXT,       -- 'LONG', 'ACCUMULATE', 'CONTRARIAN', 'WATCH', 'SPECULATIVE', 'AVOID'
  timeframe TEXT,              -- 'swing_2_4wk', 'position_3_6mo', 'long_term_12mo+'
  risk_factor TEXT,
  catalyst TEXT,
  news_sentiment NUMERIC(3,1), -- -2.0 to +2.0 (added in future experiment)
  signal_reasoning TEXT,
  -- Accuracy tracking (filled by Loop 2 after 5 days)
  actual_move_5d_pct NUMERIC(6,2),
  signal_correct BOOLEAN,
  scored_at TIMESTAMPTZ,
  created_at TIMESTAMPTZ DEFAULT now(),
  UNIQUE(ticker_id, date)
);

-- Macro environment per day (Loop 1 writes this)
CREATE TABLE macro (
  id SERIAL PRIMARY KEY,
  date DATE UNIQUE NOT NULL,
  sp500 NUMERIC(10,2),
  sp500_change_pct NUMERIC(6,2),
  nasdaq NUMERIC(10,2),
  nasdaq_change_pct NUMERIC(6,2),
  vix NUMERIC(6,2),
  dxy NUMERIC(8,2),
  us_10y_yield NUMERIC(5,3),
  wti_crude NUMERIC(8,2),
  brent_crude NUMERIC(8,2),
  macro_regime TEXT,           -- 'risk_on', 'risk_off', 'neutral', 'war_premium'
  macro_summary TEXT,
  created_at TIMESTAMPTZ DEFAULT now()
);

-- Sector correlation snapshots (Loop 2 calculates from accumulated data)
CREATE TABLE sector_correlations (
  id SERIAL PRIMARY KEY,
  date DATE NOT NULL,
  sector_a TEXT NOT NULL,
  sector_b TEXT NOT NULL,
  correlation_30d NUMERIC(5,3),
  rotation_signal TEXT,        -- 'flowing_a_to_b', 'flowing_b_to_a', 'neutral'
  created_at TIMESTAMPTZ DEFAULT now(),
  UNIQUE(date, sector_a, sector_b)
);

-- Daily reports (the markdown output, stored as text)
CREATE TABLE reports (
  id SERIAL PRIMARY KEY,
  date DATE UNIQUE NOT NULL,
  report_version TEXT,
  methodology_version TEXT,
  markdown_content TEXT,
  composite_score NUMERIC(5,1),
  accuracy_score NUMERIC(5,1),
  actionability_score NUMERIC(5,1),
  coverage_score NUMERIC(5,1),
  created_at TIMESTAMPTZ DEFAULT now()
);

-- Methodology experiments (the autoresearch loop tracking)
CREATE TABLE experiments (
  id SERIAL PRIMARY KEY,
  experiment_id TEXT UNIQUE NOT NULL,  -- e.g. 'format-001', 'layer-001'
  experiment_type TEXT,                -- 'auto_evolve' or 'framework_change'
  hypothesis TEXT,
  changes_description TEXT,
  started_at DATE,
  status TEXT DEFAULT 'pending',       -- 'pending', 'keep', 'discard'
  cs_before NUMERIC(5,1),
  cs_after NUMERIC(5,1),
  cs_delta NUMERIC(5,1),
  resolved_at DATE,
  resolution_notes TEXT,
  created_at TIMESTAMPTZ DEFAULT now()
);

-- Prediction tracking (for future ML/confidence scoring)
CREATE TABLE predictions (
  id SERIAL PRIMARY KEY,
  ticker_id INTEGER REFERENCES tickers(id),
  prediction_date DATE NOT NULL,
  target_date DATE NOT NULL,           -- when we expect the move
  predicted_direction TEXT,            -- 'up', 'down', 'flat'
  predicted_magnitude_pct NUMERIC(6,2),
  confidence NUMERIC(4,2),             -- 0.00 to 1.00
  reasoning TEXT,
  -- Outcome (filled when target_date passes)
  actual_direction TEXT,
  actual_magnitude_pct NUMERIC(6,2),
  prediction_correct BOOLEAN,
  scored_at TIMESTAMPTZ,
  created_at TIMESTAMPTZ DEFAULT now()
);
```

### How the Two Loops Use Supabase

**Loop 1 — Daily Report (9pm CET, weekdays):**
1. Web search all 18 tickers → write to `daily_prices`, `technicals`
2. Web search macro → write to `macro`
3. Generate signals → write to `signals`
4. Generate markdown report → write to `reports`
5. Score any signals from 5 days ago → update `signals.actual_move_5d_pct`, `signals.signal_correct`

**Loop 2 — Auto-Research (runs after Loop 1, or on-demand):**
1. Read from `signals` table → calculate Accuracy Score over trailing window
2. Read from `experiments` table → check if current experiment has 3+ data points
3. Calculate Composite Score → write to `reports`
4. If experiment has 3 reports: compare CS before/after → mark keep/discard in `experiments`
5. Calculate sector correlations from `daily_prices` → write to `sector_correlations`
6. Propose next experiment → write to `experiments`
7. Over time: build `predictions` table with explicit forecasts + confidence → score them → refine

---

## 5. THE OPTIMIZATION METRIC

**Composite Score (CS) = 0.40 × AS + 0.30 × AC + 0.30 × CV**

### Accuracy Score (AS) — 0 to 100
Measured with 5-day lookback. For each ticker's directional bias:
- Signal matches actual move (≥1%) → +1
- Signal contradicts actual move → -1
- Neutral signal + small move (<1%) → +1
- Neutral signal + large move (>3%) → -1

**AS = (correct / total) × 100**

### Actionability Score (AC) — 0 to 100
Per ticker, 0-5 points:
1. Clear directional bias stated → +1
2. Entry/exit levels or zones → +1
3. Risk factor explicitly named → +1
4. Timeframe specified → +1
5. Catalyst identified → +1

**AC = (total points / max possible) × 100**

### Coverage Score (CV) — 0 to 100
Points per research layer present:

| Layer | Points |
|-------|--------|
| Price + daily change | 1 |
| Technical indicators (RSI, MACD, MAs) | 2 |
| Support/resistance levels | 1 |
| Sector correlation analysis | 2 |
| News sentiment scoring | 2 |
| Options flow / unusual activity | 2 |
| Macro overlays (DXY, oil, VIX, yields) | 2 |
| Earnings calendar + estimates | 1 |
| Aschenbrenner thesis alignment | 2 |

**CV = (points / 15) × 100**

---

## 6. THE AUTORESEARCH METHODOLOGY

Adapted from Karpathy's [program.md](https://github.com/MalikJPalamar/autoresearch/blob/master/program.md).

### Core Principles
1. **Single metric:** Composite Score (CS). Lower is better for val_bpb; higher is better for CS.
2. **One experiment at a time.** Change one thing, measure over 3 reports, keep or discard.
3. **Simplicity criterion:** If a change adds complexity but doesn't improve CS, discard.
4. **Never stop.** When told "Run SA scan," execute fully without further prompting.

### Keep/Discard Rules

**Auto-evolve (no approval needed):**
- Report formatting and layout
- Data source selection (which sites to scrape)
- Data presentation (tables vs prose)
- Coverage depth per ticker
- Source prioritization

**Requires Malik's approval (framework changes):**
- Adding/removing research layers (e.g., options flow)
- Changing CS scoring weights
- Adding/removing tickers
- Changing signal classification logic
- Modifying Aschenbrenner thesis mapping

### Experiment Lifecycle
```
1. Describe hypothesis in changelog.md (and experiments table)
2. Apply change to methodology.md
3. Run 3 reports with the change
4. Calculate CS delta:
   - CS improved → KEEP (update methodology, log "keep")
   - CS unchanged → KEEP only if simpler
   - CS decreased → DISCARD (revert methodology, log "discard")
```

### Research Layer Rollout Sequence

**Phase 1 — Foundation (DONE):**
- ✅ Price + daily change
- ✅ Technical indicators (RSI, MACD, SMA/EMA)
- ✅ Alerts (overbought/oversold, golden cross)
- ✅ Support/resistance levels
- ✅ Directional bias + timeframe + risk factor
- ✅ Macro overlay (VIX, DXY, yields, oil)
- ✅ Sector correlation table
- ✅ Aschenbrenner thesis alignment

**Phase 2 — Next experiments (auto-evolve):**
- ⬜ Per-ticker news sentiment scoring (-2 to +2) ← NEXT
- ⬜ Relative strength ranking within sectors

**Phase 3 — Requires approval:**
- ⬜ Options flow / unusual activity signals
- ⬜ Cross-sector rotation signals from accumulated data
- ⬜ Correlation breakdown alerts

**Phase 4 — Future (requires data accumulation):**
- ⬜ ML-based predictions with confidence scores
- ⬜ Backtesting signals against historical data
- ⬜ Catalyst calendar automation

---

## 7. DATA SOURCES (Priority Order)

For each daily scan, web-search these in order:

1. **Google Finance** — real-time quotes, cross-reference prices
2. **TipRanks** — technical indicators, analyst consensus, targets
3. **Investing.com** — RSI, MACD, MAs, macro data (VIX, yields, oil, DXY)
4. **TradingView** — chart signals, technical ratings, community analysis
5. **Yahoo Finance** — fundamentals, earnings, news
6. **Schwab Market Update** — macro context, market structure narrative
7. **CNBC** — macro, bonds, yields, breaking news
8. **StockAnalysis.com** — overview, news aggregation
9. **Robinhood** — intraday range, volume, quick snapshots
10. **Barchart** — technical analysis summaries

---

## 8. SIGNAL CLASSIFICATION

| Signal | Meaning | Criteria |
|--------|---------|----------|
| 🟢 LONG | Clear bullish | Technicals + fundamentals + catalyst aligned |
| 🟢 ACCUMULATE | Buy the dip | Long-term bullish, currently in pullback zone |
| 🟢 CONTRARIAN | Deep value | Oversold / compressed valuation, thesis intact |
| 🟡 WATCH | No edge | Conflicting signals, wait for confirmation |
| 🟡 SPECULATIVE | High risk/reward | Pre-revenue or binary outcome, size small |
| 🔴 AVOID | Bearish | Deteriorating fundamentals or poor risk/reward |

---

## 9. SCHEDULING

**Target:** 9pm CET (8pm UTC in winter, 7pm UTC in summer), weekdays only.

**Reality:** Claude cannot self-trigger. Malik must open a conversation and say "Run SA scan" (or equivalent). The agent then executes the full loop autonomously:

1. Pull latest methodology from Supabase (or repo)
2. Score 5-day-old signals
3. Web-search all 18 tickers + macro
4. Generate report
5. Self-score (AC + CV)
6. Write all data to Supabase
7. Evaluate current experiment (keep/discard if 3+ reports)
8. Propose next experiment
9. Optionally push markdown report to GitHub repo

**For true automation (no human trigger):** Consider a cron job or GitHub Action that calls the Anthropic API directly, or a Supabase Edge Function triggered by pg_cron. This is a Claude Code task.

---

## 10. GITHUB REPO CREDENTIALS

- **Repo:** `MalikJPalamar/situational-awareness-lens`
- **PAT:** The one provided in this session has been exposed in chat history. **Malik must rotate it.** The new PAT should be stored as an environment variable, never committed.
- **Git identity:** `malik@builderbee.io` / `SA-Portfolio-Bot`

---

## 11. MALIK'S CONTEXT

- **Role:** Head of IT & Applied Intelligence at Alchemy of Breath; Fractional CEO of BuilderBee and Centaurion.me
- **Location:** El Valle Golf Resort, Murcia, Spain (CET timezone)
- **Coding:** JavaScript/TypeScript, beginner level
- **Thinking style:** Visual-spatial, synoptic, first-principles
- **AI tools:** Claude for Teams, Claude Code, Hostinger VPS
- **Related projects:** The SA portfolio tracker feeds into his Centaurion.me framework (Fitness = Predictive Order / Thermodynamic Cost) and his 16-project scorecard for AI tool evaluation
- **Prior autoresearch work:** Forked karpathy/autoresearch, extended Bassim Eledath's 8 Levels of Agentic Engineering with Levels 9-11

---

## 12. IMMEDIATE NEXT STEPS FOR CLAUDE DEV / CLAUDE CODE

### Step 1: Supabase Setup
- Ask Malik: use existing project or create new one called `situational-awareness`?
- Run the SQL schema above to create all tables
- Seed `tickers` table with the 18 stocks
- Backfill `daily_prices`, `technicals`, `signals`, `macro`, `reports` from the two existing reports (2026-03-28, 2026-03-29)

### Step 2: Build the Daily Report Loop
- Create a script (Node.js or Edge Function) that:
  - Reads `tickers` table
  - Calls web search / Anthropic API for each ticker
  - Writes to `daily_prices`, `technicals`, `signals`, `macro`
  - Generates markdown report and writes to `reports`
  - Scores actionability + coverage

### Step 3: Build the Auto-Research Loop
- Create a script that:
  - Reads `signals` from 5 days ago + current prices → scores accuracy
  - Calculates Composite Score
  - Evaluates current experiment (3-report window)
  - Proposes next experiment
  - Writes to `experiments` table

### Step 4: Schedule with pg_cron or External Trigger
- Option A: Supabase pg_cron + Edge Function (self-contained)
- Option B: GitHub Action on cron → calls Anthropic API → writes to Supabase
- Option C: Malik's Hostinger VPS cron → Node.js script

### Step 5: Build Prediction Layer
- Once 20+ days of signal data accumulated:
  - Calculate trailing accuracy per ticker, per sector, per signal type
  - Build confidence scores based on historical hit rates
  - Start writing to `predictions` table with explicit forecasts
  - Score predictions and feed back into methodology evolution

---

## 13. FILES IN THIS CONVERSATION TO PRESERVE

The following files were created during this session and should be referenced:

| File | Location | Purpose |
|------|----------|---------|
| `program.md` | GitHub repo root | Governing methodology |
| `auto-research/methodology.md` | GitHub repo | Active template v1.1 |
| `auto-research/changelog.md` | GitHub repo | Methodology evolution log |
| `auto-research/results.tsv` | GitHub repo | Experiment scores |
| `auto-research/2026-03-28-report.md` | GitHub repo | Baseline report (v1) |
| `auto-research/2026-03-29-report.md` | GitHub repo | First experiment report (v2) |

---

*This handoff document is the single source of truth for continuing the SA Portfolio Tracker. Read this first, then read program.md in the repo, then proceed with Supabase setup.*
