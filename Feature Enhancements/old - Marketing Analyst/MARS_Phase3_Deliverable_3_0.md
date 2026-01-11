# MARS PHASE 3 COMPLETE DIRECTIVE PACKAGE
## Report Generation & Automation Architecture

**Date:** December 29, 2025, 1:00 AM MST  
**Authority:** Design Authority (MARS-Phase3-Directive.md)  
**Status:** ✅ READY FOR PHASE 2 COMPLETION → IMMEDIATE PHASE 3 EXECUTION

---

# TABLE OF CONTENTS

1. [Executive Summary](#executive-summary)
2. [Phase 3 Overview](#phase-3-overview)
3. [Phase 3 Deliverables (5 Total)](#phase-3-deliverables)
4. [Deliverable 3.1: Daily Brief Generator](#deliverable-31-daily-market-brief-generator)
5. [Deliverable 3.2: Weekly Assessment Generator](#deliverable-32-weekly-market-assessment-generator)
6. [Deliverable 3.3: Analyst Context Generators](#deliverable-33-analyst-context-generators)
7. [Deliverable 3.4: Report Formatting & Distribution](#deliverable-34-report-formatting--distribution)
8. [Deliverable 3.5: Scheduling & Automation](#deliverable-35-scheduling--automation)
9. [Integration Points & Critical Rules](#integration-points--critical-rules)
10. [Success Criteria & Timeline](#success-criteria--timeline)

---

# EXECUTIVE SUMMARY

Phase 3 transforms Phase 2 market intelligence outputs (macro regime scores + sector attractiveness rankings) into **automated daily and weekly intelligence reports** delivered to analysts, portfolio managers, and trading systems.

**Key Objective:** Consolidate 3 existing manual weekly reporting tasks into 2 fully automated report templates:
- **Daily Market Brief** (2-3 pages, 7:30 AM ET weekdays)
- **Weekly Market Assessment** (8-10 pages, Friday 4 PM ET)

**Deliverables:** 5 implementation components  
**Token Budget:** 15,000 tokens (fixed allocation)  
**Dependencies:** Phase 2 completion + quality gate (95%+ accuracy)

---

# PHASE 3 OVERVIEW

## What Phase 3 Does

1. **Consumes Phase 2 Outputs**
   - Reads macro regime scores (BULLISH/NEUTRAL/BEARISH/CRISIS)
   - Reads sector attractiveness rankings (1-11 per GICS sector)
   - Reads dimension scores (7 macro + 4 per sector)

2. **Adds Real-Time Context**
   - Overnight futures (S&P 500, Nasdaq, Russell)
   - Global market moves (Nikkei, DAX, Shanghai)
   - Treasury yields, spreads, currency moves
   - Today's economic calendar and data releases
   - Latest market news headlines

3. **Generates Reports**
   - Daily brief (before market open)
   - Weekly assessment (Friday PM)

4. **Distributes to Stakeholders**
   - Email: PM, senior analysts, risk committee
   - Slack: #market-brief, #market-assessment channels
   - PDF: Board presentations (if needed)
   - Dashboard: Embedded in Perplexity space
   - Archive: Historical records for regression testing

5. **Feeds Downstream Systems**
   - Stock-Analyst: Macro context for conviction adjustment
   - Alpha-Picks: Sector ranking + rotation signals
   - Portfolio-Orchestrator: Rebalancing recommendations

## What Phase 3 Does NOT Do

- ❌ Recalculate regime scores (Phase 2 owns that)
- ❌ Make portfolio decisions (Portfolio-Orchestrator owns that)
- ❌ Validate stock predictions (Alpha-Picks owns that)
- ❌ Enforce constraints (Quality-Assurance owns that)

---

# PHASE 3 DELIVERABLES

## Deliverable 3.1: Daily Market Brief Generator (2,500 tokens)

### Objective
Generate 2-3 page market snapshot before market open (7:30 AM ET Monday-Friday)

### Input Data Sources

**From Phase 2 Cache (Yesterday 5 PM ET):**
- Macro regime classification + score + confidence
- All 7 macro dimension scores
- Sector attractiveness ranking (all 11 sectors)
- Dimension scores per sector (4 per sector)

**Live Overnight Feeds (<2 hours old):**
- S&P 500, Nasdaq 100, Russell 2000 futures (via Finnhub)
- Global market closes (Nikkei, DAX, Shanghai)
- Treasury yields (10Y, 2Y, 2-10 spread)
- Dollar Index, Oil, Bitcoin prices
- Economic calendar (today's scheduled releases)
- Market headlines (top 3-5 overnight news)

### Report Structure

```
# DAILY MARKET BRIEF
[Date & Time]

## 1. OVERNIGHT ACTION (Last 16 Hours)
- Equity futures: [±X.XX%] Assessment: [CONSTRUCTIVE/NEUTRAL/DETERIORATING]
- Global markets: [Nikkei, DAX, Shanghai moves + spillover effects]
- Fixed income: [Treasury yields, spreads, trend]
- Currencies & commodities: [Dollar, Oil, Bitcoin]

## 2. MACRO REGIME STATUS (From Phase 2)
- Classification: [BULLISH/NEUTRAL/BEARISH/CRISIS]
- Score: [X.X/100], Confidence: [X%]
- Change from yesterday: [SAME/IMPROVED/DETERIORATED]
- Key drivers: [Top 2-3 factors]
- Risks today: [Monitor for specific signals]

## 3. SECTOR WATCH (From Phase 2)
- Top 3 most attractive: [Table with overnight signals]
- Bottom 3 least attractive: [Table with overnight signals]
- Rotation alert: [If sector ranks changed overnight]

## 4. DATA CALENDAR (Today's Events)
- [Time] [HIGH/MEDIUM impact] Release | Forecast vs Prior

## 5. QUICK POSITIONING SUMMARY
- Equity exposure: [INCREASE/MAINTAIN/REDUCE] risk
- Sector positioning: [Overweight/Underweight specific sectors]
- Key levels: [Support/resistance, VIX target]

## 6. ANALYST CONTEXT FEEDS (JSON for Downstream Systems)
- Stock-Analyst: [macro_regime, multiplier, conviction_adjustment]
- Alpha-Picks: [sector_ranking, rotation_signals, concentration_alert]
- Portfolio-Orchestrator: [rebalancing_trigger, hedge_positioning, cash_signal]

## 7. OVERNIGHT HEADLINE SUMMARY
- [Top 3-5 news items affecting markets]
```

### Implementation

```python
def generate_daily_brief():
    # Load Phase 2 outputs from cache (yesterday 5 PM, max 14 hours old)
    current_regime = load_macro_regime_cache()
    previous_regime = load_macro_regime_cache(days_back=1)
    current_sectors = load_sector_ranking_cache()
    previous_sectors = load_sector_ranking_cache(days_back=1)
    
    # Fetch overnight market data (live)
    overnight_data = {
        "sp500_futures": fetch_sp500_futures(),  # Finnhub
        "nasdaq_futures": fetch_nasdaq_futures(),
        "global_markets": fetch_global_markets(),  # search_web
        "treasuries": fetch_treasury_yields(),  # FRED
        "commodities": fetch_commodities(),
        "calendar": fetch_economic_calendar_today()  # search_web
        "headlines": search_web("market news overnight 2025")
    }
    
    # Render template
    report = render_daily_brief_template(
        overnight_data, current_regime, previous_regime,
        current_sectors, previous_sectors
    )
    
    # Generate context feeds
    context_feeds = {
        "stock_analyst": generate_stock_analyst_context(current_regime),
        "alpha_picks": generate_alpha_picks_context(current_sectors),
        "portfolio_orchestrator": generate_portfolio_context(current_regime)
    }
    
    return report, context_feeds
```

---

## Deliverable 3.2: Weekly Market Assessment Generator (5,000 tokens)

### Objective
Consolidate 3 existing manual weekly tasks into single 8-10 page unified report

### Current Tasks Being Replaced
1. **WEEKLY-MARKET-STRUCTURE-TASK.md** → Part A (Market Structure)
2. **Weekly-Risk-Regime.md** → Part B (Risk & Regime)
3. **WEEKLY-AI-and-MAG7-INTELLIGENCE-TASK.md** → Part C (AI & Mag7)
4. **NEW** → Part D (Unified Positioning)
5. **NEW** → Part E (Critical Catalysts)

### Report Structure: 5 Parts (8-10 pages)

**PART A: MARKET STRUCTURE ANALYSIS (2 pages)**
- Four Pillars Assessment:
  1. Manufacturing & Production (ISM PMI)
  2. Labor Market (unemployment, jobless claims, wage growth, Sahm Rule)
  3. Credit Conditions (HY/IG spreads, credit trends)
  4. Market Breadth (advance-decline ratio, % stocks >50-day MA)

- Sector Rotation Status: [Which sectors gaining/losing leadership]
- Overall diagnosis: Market health (healthy / diverging / deteriorating)

**PART B: RISK & REGIME ASSESSMENT (2 pages)**
- Risk asset performance (Bitcoin, Gold, Silver trends)
- Volatility & sentiment (VIX range, Fear & Greed, Put/Call)
- Treasury landscape (yield curve, rate expectations, recession signals)
- Market stress: Carry trade health, liquidity indicators
- Risk hierarchy: Tier 1 (highest risk) → Tier 3 (lowest)

**PART C: AI & MAG7 INTELLIGENCE (1.5 pages)**
- Magnificent 7 weekly performance table
- Core AI (NVDA, MSFT, AMZN, GOOG) vs Speculative AI (TSLA, META, AAPL)
- AI narrative scenarios (3 narratives with probabilities)
- Concentration risk: [If >35%, elevated; if >40%, critical]

**PART D: UNIFIED POSITIONING RECOMMENDATIONS (1.5 pages)**
- Equity allocation: [INCREASE/MAINTAIN/REDUCE] risk
- Sector rebalancing targets: [Overweight/Neutral/Underweight per sector]
- Hedge positioning: [VIX calls, put spreads, Treasury, gold]
- Cash buffer: [Maintain / Deploy / Build]

**PART E: CRITICAL DRIVERS & CATALYSTS (0.5 pages)**
- Next week's key events: [HIGH/MEDIUM impact schedule]
- IF-THEN decision tree: [If [condition] → [regime shift/action]]

### Implementation

```python
def generate_weekly_assessment():
    # Load Phase 2 outputs from cache (Friday 5 PM, current + 1 week ago)
    current_regime = load_macro_regime_cache(timestamp="Fri 17:00")
    previous_regime = load_macro_regime_cache(timestamp="Fri 17:00, 1 week back")
    
    current_sectors = load_sector_ranking_cache(timestamp="Fri 17:00")
    previous_sectors = load_sector_ranking_cache(timestamp="Fri 17:00, 1 week back")
    
    # Fetch weekly market data
    weekly_data = {
        "sp500_wk_return": fetch_sp500_week_return(),
        "vix_4week_range": fetch_vix_range(days=28),
        "mag7_prices": fetch_mag7_prices(),
        "sector_returns": fetch_sector_returns(days=5),
        "treasuries": fetch_treasury_data(),
        "credit_spreads": fetch_credit_spreads(),
        "breadth": fetch_market_breadth(),
        "earnings": fetch_earnings_trends()
    }
    
    # Detect rotation signals
    rotation = detect_sector_rotation(current_sectors, previous_sectors)
    
    # Render all 5 parts
    report = render_weekly_template(
        current_regime, previous_regime, current_sectors,
        previous_sectors, weekly_data, rotation
    )
    
    # Generate context feeds
    context_feeds = generate_all_context_feeds(current_regime, current_sectors)
    
    return report, context_feeds
```

---

## Deliverable 3.3: Analyst Context Generators (4,000 tokens)

### Stock-Analyst Context (1,000 tokens)

**Output:**
```json
{
  "macro_regime": "BULLISH|NEUTRAL|BEARISH|CRISIS",
  "regime_multiplier": 0.5-1.25,
  "regime_confidence": "X%",
  "key_macro_drivers": ["Driver 1", "Driver 2"],
  "conviction_adjustment": "RAISE|MAINTAIN|LOWER conviction by X%",
  "conviction_basis": "narrative",
  "tail_risk": "primary risk"
}
```

**Logic:**
- BULLISH → "RAISE conviction 10%, widen fair value targets"
- NEUTRAL → "MAINTAIN conviction, monitor for regime shift"
- BEARISH → "LOWER conviction 15%, narrow fair value targets"
- CRISIS → "LOWER conviction 30%, reduce position sizing"

### Alpha-Picks Context (1,500 tokens)

**Output:**
```json
{
  "sector_ranking": [
    {"rank": 1-11, "sector": "name", "score": 0-100, 
     "attractiveness": "ATTRACTIVE|NEUTRAL|UNATTRACTIVE",
     "rotation_signal": "GAINING|NEUTRAL|LOSING"}
  ],
  "momentum_tailwind": "Rank 1 sector",
  "rotation_catalyst": "description or null",
  "concentration_alert": true|false,
  "high_conviction_sectors": ["Sector 1", "Sector 2", "Sector 3"]
}
```

### Portfolio-Orchestrator Context (1,500 tokens)

**Output:**
```json
{
  "regime_based_allocation": {
    "bullish": {"equities": 0.90, "cash": 0.10, "hedge": 0.05},
    "neutral": {"equities": 0.70, "cash": 0.20, "hedge": 0.10},
    "bearish": {"equities": 0.50, "cash": 0.35, "hedge": 0.15},
    "crisis": {"equities": 0.30, "cash": 0.50, "hedge": 0.20}
  },
  "sector_rebalancing_trigger": true|false,
  "hedge_positioning": "INCREASE|MAINTAIN|REDUCE",
  "cash_deployment_signal": "DEPLOY|BUILD|MAINTAIN",
  "risk_adjustment_multiplier": 0.5-1.25
}
```

---

## Deliverable 3.4: Report Formatting & Distribution (2,000 tokens)

### Markdown Rendering
- Template population with live data
- Production-quality formatting
- Proper sections, tables, emphasis

### PDF Export
- Convert markdown → HTML → PDF
- Add branding header + footer
- Support for printing, board presentations

### Email Distribution
- Recipients: PM, analysts, risk committee
- Subject: Clear date/report type
- Body: Summary + link + PDF attachment

### Slack Integration
- Channel: #market-brief (daily), #market-assessment (weekly)
- Format: Summary with link to full report
- Truncate for Slack (max 2000 chars)

### Dashboard Embedding
- Render markdown as HTML
- Embed in Perplexity space
- Auto-refresh (hourly cadence)

### Archiving
- Store with timestamp + metadata
- Directory structure: `/archive/daily/[YYYY]/[MM]/` or `/archive/weekly/[YYYY]/w[WW]/`
- Immutable (no editing after generation)
- Support historical regression testing

---

## Deliverable 3.5: Scheduling & Automation (1,500 tokens)

### Daily Brief Scheduler (7:30 AM ET)

```python
@scheduler.scheduled_job('cron', day_of_week='mon-fri', 
                         hour=7, minute=30, timezone='US/Eastern')
def daily_job():
    try:
        report, context_feeds = generate_daily_brief()
        distribute_via_email(report, ["pm@example.com", "analysts@example.com"], "daily")
        post_to_slack(report, "daily", "#market-brief")
        archive_report(report, "daily", datetime.now())
        push_context_feeds(context_feeds)
    except Exception as e:
        escalate_to_master_architect(error=e, report_type="daily")
```

### Weekly Assessment Scheduler (Friday 4 PM ET)

```python
@scheduler.scheduled_job('cron', day_of_week='fri', 
                         hour=16, minute=0, timezone='US/Eastern')
def weekly_job():
    try:
        report, context_feeds = generate_weekly_assessment()
        distribute_via_email(report, [...], "weekly")
        post_to_slack(report, "weekly", "#market-assessment")
        export_to_pdf(report, f"weekly-{datetime.now().strftime('%Y-w%W')}")
        archive_report(report, "weekly", datetime.now())
        push_context_feeds(context_feeds)
    except Exception as e:
        escalate_to_master_architect(error=e, report_type="weekly")
```

### Context Feed Push

Push JSON context feeds to downstream systems via API:
- Stock-Analyst: `/api/stock-analyst/macro-context` (POST)
- Alpha-Picks: `/api/alpha-picks/sector-context` (POST)
- Portfolio-Orchestrator: `/api/portfolio-orchestrator/rebalancing-signal` (POST)

### Error Handling & Escalation

If report generation fails:
1. Log error with full stack trace
2. Email alert to Master-Architect
3. Post to #mars-alerts Slack channel
4. Flag as unrecoverable escalation
5. Manual report generation fallback required

---

# INTEGRATION POINTS & CRITICAL RULES

## Integration with Phase 2

| Phase 2 Output | Phase 3 Usage | Freshness Requirement |
|---|---|---|
| Macro regime score | Daily brief section 2 | ≤14 hours old ✓ |
| Regime classification | Daily brief header | ≤14 hours old ✓ |
| All 7 macro dimensions | Weekly Part B | ≤164 hours old (Mon report) |
| Sector attractiveness (11) | Daily brief section 3 | ≤14 hours old ✓ |
| Sector dimension scores (4×11) | Weekly Part A | ≤164 hours old |

## Integration with Downstream Systems

| Downstream | Input | Purpose | Frequency |
|---|---|---|---|
| Stock-Analyst | Macro context JSON | Conviction adjustment | Daily + Weekly |
| Alpha-Picks | Sector context JSON | Tier 1 factor | Daily + Weekly |
| Portfolio-Orchestrator | Portfolio context JSON | Rebalancing signal | Weekly |

## Critical Rules

### Rule 1: Data Freshness Compliance
- Daily Brief regime: From 5 PM ET previous day (max 14 hours old at 7:30 AM)
- All overnight data: <2 hours old
- Fallback: If Phase 2 data >24 hours old, flag with data quality warning

### Rule 2: No Recalculation
- Phase 3 reads Phase 2 outputs AS-IS (no regime recalculation)
- Phase 3 only formats, distributes, generates context feeds

### Rule 3: Context Feed Format
- All outputs are JSON dictionaries (for API consumption)
- No hallucinated analyst commentary
- Only data from Phase 2 + overnight market moves

### Rule 4: Fallback Procedures
- If Phase 2 data unavailable: Generate report with caveats (do NOT delay)
- If overnight data unavailable: Use previous day + flag
- All fallback reports clearly marked with data quality warnings

### Rule 5: Archive Immutability
- All reports archived with generation timestamp + metadata
- No editing after publication
- Enables historical comparison for regression testing

---

# SUCCESS CRITERIA & TIMELINE

## Phase 3 Success Criteria

- ✅ Daily Brief Generator: <5 min generation, 7:30 AM ET weekdays
- ✅ Weekly Assessment: <10 min generation, Friday 4 PM ET
- ✅ All 3 existing tasks consolidated (no duplication)
- ✅ Analyst contexts generated for all 3 downstream systems
- ✅ All formatting (markdown, PDF, email, Slack, archive) working
- ✅ Scheduling automated (zero manual intervention)
- ✅ Error handling + escalation tested
- ✅ 1 week real data validation (analyst consensus match)
- ✅ Zero hallucinations in all reports
- ✅ All 5 deliverables complete + tested

## Phase 3 Dependencies

**Phase 2 Must Complete First:**
- All 6 Phase 2 deliverables (2.1-2.6)
- Phase 2 quality gates: 95%+ accuracy
- Phase 2 caching: Macro regime + sectors cached at 5 PM ET daily
- Phase 2 freshness: Data <1 day old

## Phase 3 Timeline (if Phase 2 Complete)

| Week | Milestone | Deliverables |
|------|-----------|---|
| Week 1 | Design + Prototype | 3.1, 3.2 templates |
| Week 2 | Implementation | 3.1, 3.2, 3.3 code |
| Week 3 | Integration + Automation | 3.4, 3.5 scheduling |
| Week 4 | Real Data Validation | All 5 tested + signed off |

---

# RISKS & MITIGATIONS

## Critical Dependency Risks

**Risk:** Phase 2 accuracy <90%
- **Impact:** Phase 3 reports will be unreliable
- **Mitigation:** Phase 2 must pass 95%+ accuracy gate before Phase 3 start

**Risk:** Phase 2 data freshness >14 hours
- **Impact:** Daily Brief shows stale regime classification
- **Mitigation:** Phase 2 caching must be automated (5 PM ET daily)

**Risk:** Phase 2 system failure / crash
- **Impact:** Phase 3 reports fail (no data)
- **Mitigation:** Escalate to Master-Architect, manual fallback required

## Data Source Risks

**Risk:** Overnight futures unavailable (Finnhub API down)
- **Impact:** Daily Brief missing key overnight action
- **Mitigation:** Fallback to previous day's data + flag with caveat

**Risk:** Search_web times out or returns no results
- **Impact:** Missing headlines or calendar events
- **Mitigation:** Retry once, then proceed without that section + flag

**Risk:** FRED API down (Treasury yields unavailable)
- **Impact:** Missing fixed income context
- **Mitigation:** Fallback to cached yields from previous day

## Scheduling Risks

**Risk:** Timezone error (EDT vs EST transition)
- **Impact:** Daily Brief runs at wrong time
- **Mitigation:** APScheduler handles DST automatically, use explicit timezone

**Risk:** Holiday market closures (Thanksgiving, Christmas, etc.)
- **Impact:** Daily Brief/Weekly Assessment run on closed market
- **Mitigation:** Manual override calendar, skip on market closures

---

# EXECUTIVE SUMMARY FOR DESIGN AUTHORITY

**Phase 3 Ready:** All 5 deliverables designed and architected

**Token Budget:** 15,000 tokens (fixed allocation)

**Prerequisites:** Phase 2 completion + quality gate (95%+ accuracy)

**Go-Live:** Immediately upon Phase 2 sign-off

**Impact:** Consolidates 3 manual weekly tasks into 2 fully automated daily/weekly reports

**Risk Level:** LOW (Phase 3 is pure formatting/distribution, depends entirely on Phase 2 quality)

---

**MARS Phase 3 Complete Directive Package: READY FOR DESIGN AUTHORITY REVIEW**

Implementation may begin Phase 3 immediately following Phase 2 sign-off and quality gate approval.

---

**Status:** ✅ Phase 3 Designed & Ready  
**Authority:** Design Authority (MARS-Phase3-Directive.md)  
**Date:** December 29, 2025, 1:00 AM MST
