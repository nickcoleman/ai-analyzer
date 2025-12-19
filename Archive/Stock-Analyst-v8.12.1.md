# Stock-Analyst.md v8.12.1
## Phase 1 Update: SLA Blocking Removed, Local Golden Cross, Auto-Rerun System

**Version:** v8.12.1 (Phase 1 Final)  
**Previous:** v8.12  
**Status:** Production Ready  
**Last Updated:** December 13, 2025, 12:20 PM MST  
**Framework Version:** v8.13  
**Changes:** Cache-first TURN 1, local Golden Cross, auto-rerun system, quality_metadata integration  

---

## PHASE 1 CHANGES SUMMARY

### Major Improvements
1. **TURN 1 Latency:** <5 seconds (removed MA/Alpha/EMA external blocking)
2. **Golden Cross:** Computed locally (no escalation to MA)
3. **Auto-Rerun:** Up to 3 attempts per check (80%+ error recovery)
4. **Quality Metadata:** Comprehensive audit trail in every SAOutput
5. **Graceful Degradation:** Analysis proceeds with penalties when inputs missing/stale

### Framework Invariants Maintained
- ✓ 6-TURN architecture (TURN 0-6 structure intact)
- ✓ 10-gate QA protocol (Gates 1-10 preserved, Gates 1-8 in Quality-Assurance-Engineer.md v8.0.7, Gates 9-10 in TURN 3 & 6)
- ✓ 3/21/200 EMA technical analysis (core technical framework)
- ✓ Conviction multiplier stack (EMA + Golden Cross)
- ✓ QUALITY-Guardrails A–D integration

---

## TURN 1: STARTUP CONTEXT (CACHE-FIRST, NO BLOCKING)

### New Architecture: Cache-First Startup

**Objective:** Load portfolio context in <5 seconds with zero external API blocking.

**TURN 1 Flow:**

```
TURN 1: Startup Context (Cache-First)

Step 1A: Load Local Portfolio
  - Action: Load PORTFOLIO.md from local storage
  - Duration: ~0.1 sec
  - Fallback: If file missing, escalate (unrecoverable)
  - Log: portfolio_loaded_timestamp, portfolio_age_hours

Step 1B: Load Cached Market-Analyst Output
  - Action: Check local MA cache (if exists)
  - Duration: ~0.2 sec
  - Age Check: If cache <4 hours old, use it
  - If >4 hours old: Use NEUTRAL regime (default)
  - If missing: Use NEUTRAL regime (no penalty at this stage)
  - Log: ma_cache_age_hours, ma_status (FRESH | NEUTRAL)

Step 1C: Load Cached Alpha Context
  - Action: Check local Alpha Picks cache (if exists)
  - Duration: ~0.2 sec
  - Age Check: If cache <24 hours old, use it
  - If >24 hours old or missing: Use NONE (no context)
  - Log: alpha_cache_available (true|false), alpha_age_hours

Step 1D: Load EMA Cache for Golden Cross
  - Action: Load cached 50/200 EMA values for past 20 trading days
  - Duration: ~0.5 sec
  - Requirement: Need 200-day history for Golden Cross detection
  - If <200 days available: Mark incomplete (will skip Golden Cross multiplier)
  - Log: ema_cache_completeness (%), days_available
  - Background Task: Refresh EMA cache (async, non-blocking)

Step 1E: Accept User Inputs
  - Action: Receive ticker, analysis_type (NEW_PICK | UPDATE | REBALANCE)
  - Duration: <0.1 sec
  - Validation: Stock ticker exists in PORTFOLIO or tradeable
  - Log: user_inputs_received_timestamp

Step 1F: Record Startup Metrics
  - Action: Measure total TURN 1 duration
  - Calculation: turn_1_latency_seconds = (current_time - turn_1_start_time)
  - Target: <5 seconds
  - Log: turn_1_latency_seconds, startup_context_complete

Result: All context loaded in <5 seconds, zero external blocking
```

### Startup Quality Metadata

After TURN 1, `quality_metadata` contains:

```json
{
  "turn_1_latency_seconds": 1.2,
  "data_freshness": {
    "portfolio_age_hours": 0.5,
    "portfolio_loaded_timestamp": "2025-12-13T09:30:00Z",
    "ma_context_age_hours": 2.0,
    "ma_status": "FRESH",
    "alpha_context_available": false,
    "alpha_context_age_hours": 36,
    "ema_cache_age_hours": 0,
    "ema_cache_completeness_percent": 100,
    "ema_cache_days_available": 200,
    "last_context_load": "2025-12-13T09:30:00Z"
  },
  "startup_status": "CONTEXT_READY",
  "async_background_tasks": [
    {
      "task": "REFRESH_MA_OUTPUT",
      "status": "QUEUED",
      "priority": "NORMAL",
      "max_duration_seconds": 30
    },
    {
      "task": "UPDATE_EMA_CACHE",
      "status": "QUEUED",
      "priority": "NORMAL",
      "max_duration_seconds": 20
    },
    {
      "task": "FETCH_ANALYST_REVISIONS",
      "status": "QUEUED",
      "priority": "LOW",
      "max_duration_seconds": 15
    }
  ]
}
```

### Background Async Tasks (Non-Blocking)

These run **after** TURN 1 completes, without blocking analysis:

```
ASYNC BACKGROUND TASKS (Started after TURN 1 completes):

1. REFRESH_MA_OUTPUT (Low Priority)
   - Fetch fresh Market-Analyst output
   - Update local MA cache
   - Max duration: 30 seconds
   - If timeout: Keep old cache, log warning

2. UPDATE_EMA_CACHE (Normal Priority)
   - Fetch latest 50/200 EMA values
   - Update local EMA cache
   - Max duration: 20 seconds
   - If timeout: Keep old cache, log warning

3. FETCH_ANALYST_REVISIONS (Low Priority)
   - Fetch latest analyst estimate revisions
   - Update sentiment context
   - Max duration: 15 seconds
   - If timeout: Skip revision check, proceed

All tasks run in parallel, non-blocking.
None of these delay analysis start.
```

---

## TURN 2: RESEARCH & FUNDAMENTAL ANALYSIS

*(No changes from v8.12 - preserved as-is)*

TURN 2 completes fundamental research, valuation analysis, and investment thesis development. Analyst researches:
- Company fundamentals (revenue, growth, profitability)
- Industry dynamics and competitive position
- Macro tailwinds/headwinds
- Management quality and strategy
- Valuation relative to peers

---

## TURN 3: TECHNICAL ANALYSIS & LOCAL GOLDEN CROSS COMPUTATION

### 3A: EMA Technical Analysis (3/21/200)

*(Preserved from v8.12)*

- Compute 3-day, 21-day, 200-day EMAs
- Assess alignment (STRONGUPTREND, BULLISHMOMENTUM, TRANSITIONZONE, etc.)
- Calculate confidence multiplier (1.15 to 0.50)

### NEW 3B: Local Golden Cross Computation (No Escalation)

**Objective:** Compute Golden Cross status locally; no escalation to MA; prepare multiplier for TURN 4.

**Golden Cross Logic:**

```
TURN 3.B: LOCAL GOLDEN CROSS DETECTION

Data Required: 50-day EMA, 200-day EMA (current + previous trading day)

IF ema_50_today > ema_200_today AND ema_50_yesterday ≤ ema_200_yesterday:
  Status: GOLDENCROSSTODAY
  Multiplier: 1.20x
  Severity: IMMEDIATE (most bullish signal)
  Rationale: Just crossed above today - institutional confirmation

ELSE IF ema_50_today > ema_200_today AND (today - crossover_date) ≤ 5 trading_days:
  Status: GOLDENCROSSRECENT_0_TO_5_DAYS
  Multiplier: 1.15x
  Severity: PRIORITY (fresh confirmation)
  Rationale: Recent cross (0-5 days) - institutions likely following

ELSE IF ema_50_today > ema_200_today AND (today - crossover_date) ≤ 20 trading_days:
  Status: GOLDENCROSSRECENT_5_TO_20_DAYS
  Multiplier: 1.08x
  Severity: INFORMATIONAL (established trend)
  Rationale: Established uptrend with institutional backing

ELSE IF ema_50_today > ema_200_today AND (today - crossover_date) > 20 trading_days:
  Status: GOLDENCROSS_AGED_HEALTHY_TREND
  Multiplier: 1.02x
  Severity: INFORMATIONAL (mature trend)
  Rationale: Old cross, trend healthy but no timing advantage

ELSE IF ema_50_today < ema_200_today AND ema_50_yesterday ≥ ema_200_yesterday:
  Status: GOLDENCROSS_WHIPSAW_DEATHCROSS_TODAY
  Multiplier: 0.70x
  Severity: RISK_ALERT (unstable trend)
  Rationale: Just crossed below - trend unstable, false signal risk

ELSE IF ema_50_today < ema_200_today AND (today - deathcross_date) ≤ 20 trading_days:
  Status: GOLDENCROSS_DEATHCROSS_RECENT_WHIPSAW
  Multiplier: 0.70x
  Severity: RISK_ALERT (conflicting signals)
  Rationale: Recent Death Cross after recent Golden Cross - high volatility

ELSE:
  Status: NO_GOLDENCROSS_SIGNAL
  Multiplier: 1.0x
  Severity: NONE (no technical signal)
  Rationale: Not positioned for Golden Cross setup

Calculate Gap Percentage:
  IF ema_50_today > 0:
    gap_pct = ((ema_50_today - ema_200_today) / ema_200_today) * 100
  Log: gap_pct, gap_trend (EXPANDING | NARROWING | STABLE)

NO ESCALATION - Multiplier applied locally in TURN 4
```

**Golden Cross Output Structure:**

```json
{
  "golden_cross_analysis": {
    "status": "GOLDENCROSSRECENT_0_TO_5_DAYS",
    "status_label": "Fresh Golden Cross (0-5 days old)",
    "multiplier": 1.15,
    "days_since_cross": 3,
    "cross_date": "2025-12-10",
    "ema_50_current": 145.67,
    "ema_200_current": 142.34,
    "ema_50_previous": 144.98,
    "ema_200_previous": 142.35,
    "gap_pct": 2.34,
    "gap_trend": "EXPANDING",
    "is_whipsaw": false,
    "is_imminent": false,
    "signal_quality": "FRESH_CONFIRMATION",
    "conviction_boost_tiers": 1,
    "recommendation_note": "Strong technical foundation - early mover advantage on institutional trends",
    "escalation_required": false,
    "escalation_type": "NONE",
    "alert_severity": "NONE"
  }
}
```

**No Escalation Routing:**

Unlike v8.12, Golden Cross signals **do not escalate** to Market-Analyst. The multiplier is computed locally and applied in TURN 4.

---

## TURN 4: CONVICTION SCORING & POSITION SIZING

*(Core conviction logic preserved, enhanced with local Golden Cross multiplier)*

### Conviction Multiplier Stack (with Golden Cross)

**Formula:**

```
Final Conviction = Base × EMA_Multiplier × GoldenCross_Multiplier

Where:
- Base = Fundamental conviction (60-80 typical range)
- EMA_Multiplier = 0.50 to 1.15 (from TURN 3A technical alignment)
- GoldenCross_Multiplier = 0.70 to 1.20 (from TURN 3B local computation)

Example 1: Strong case with recent Golden Cross
  Base: 75 (strong fundamental thesis)
  EMA: 1.05 (MEDIUM-HIGH technical confidence)
  GC: 1.15 (Recent 0-5 days)
  Final: 75 × 1.05 × 1.15 = 90.56 (VERY HIGH tier)

Example 2: Strong case with whipsaw
  Base: 75 (strong fundamental case)
  EMA: 1.05 (MEDIUM-HIGH technical)
  GC: 0.70 (Whipsaw detected)
  Final: 75 × 1.05 × 0.70 = 55.13 (below 55 threshold)
  Recommendation: HOLD (not BUY)
```

### Hard Stops Applied During Conviction Calculation

- **STRONGDOWNTREND:** Recommendation forced to HOLD (not BUY)
- **CONVICTION < 55:** Recommendation forced to HOLD (not BUY)
- **CONVICTION_EXCEEDS_TIER_CAP:** Escalate for Master-Architect approval

All conviction adjustments visible in `quality_metadata.conviction_calculation`:

```json
{
  "conviction_calculation": {
    "base_conviction": 75,
    "base_rationale": "Strong fundamental case with 15% expected growth",
    "ema_alignment_status": "MEDIUM_HIGH_CONFIDENCE",
    "ema_multiplier": 1.05,
    "ema_adjustment_detail": "Price above 21 EMA, EMA3 above EMA21, trend bullish",
    "golden_cross_status": "GOLDENCROSSRECENT_0_TO_5_DAYS",
    "golden_cross_multiplier": 1.15,
    "golden_cross_adjustment_detail": "Recent Golden Cross 3 days, gap expanding, institutions likely following",
    "penalties_applied": [
      {
        "penalty_type": "ASSUMPTION_DURABILITY",
        "assumption": "Earnings growth 15% YoY",
        "penalty_points": -3,
        "reason": "Based on guidance, not fully validated"
      }
    ],
    "total_penalties": -3,
    "final_conviction_before_range": 90.56,
    "conviction_range_min": 85,
    "conviction_range_max": 95,
    "final_conviction_tier": "VERY_HIGH",
    "recommendation": "BUY",
    "sizing_guidance": "3-5% of portfolio",
    "conviction_transparency": "75 × 1.05 (EMA) × 1.15 (GC) - 3 (penalties) = 90.56"
  }
}
```

---

## TURN 5: CONSTRAINT VALIDATION & POSITION STRUCTURE

*(Preserved from v8.12)*

Validates:
- Sector caps (35% hard cap per sector)
- Cash buffer (15% minimum)
- Position sizing (<6% per position)
- Portfolio concentration rules

---

## TURN 6: QA GATES & AUTO-RERUN SYSTEM

### QA Gates (8 gates in Quality-Assurance-Engineer.md v8.0.7 + 2 NEW gates in this TURN)

**Gates 1-8 (Quality-Assurance-Engineer.md v8.0.7):**
- Gate 1: Portfolio Freshness
- Gate 2: Ticker Validity
- Gate 3: Sector Constraints
- Gate 4: Conviction Sizing
- Gate 5: Cash Validation
- Gate 6: Thesis Schema
- Gate 7: Master-Architect Guidance (ALERT, non-blocking)
- Gate 8: Execution Ready

**Gate 9 (NEW - Phase 1, TURN 6):**
- Gate 9: EMA Technical Analysis (3/21/200 completion check)
  - Check: Was 3/21/200 EMA analysis completed in TURN 3A?
  - Validation: alignmentconfidence documented, conviction multiplier applied
  - If FAIL: Return to TURN 3A for EMA completion
  - Classification: **CHECK** (hard failure)

**Gate 10 (NEW - Phase 1, TURN 6):**
- Gate 10: Golden Cross Detection (local computation completion check)
  - Check: Was Golden Cross analysis completed in TURN 3B?
  - Validation: goldencrossstatus documented, Golden Cross multiplier applied, no escalations
  - If FAIL: Return to TURN 3B for Golden Cross completion
  - Classification: **CHECK** (hard failure)

### NEW: Auto-Rerun System (Up to 3 Attempts Per Check)

**Objective:** Recover from schema/calculation errors automatically; escalate only if unrecoverable.

**Auto-Rerun Logic:**

```
TURN 6: QA GATES WITH AUTO-RERUN SYSTEM

FOR each Check in [Check_1, Check_2, ..., Check_10]:
  attempt = 1
  max_attempts = 3
  
  WHILE attempt <= max_attempts:
    TRY:
      Run Check
      
      IF Check passes:
        Log: "Check {name} PASSED on attempt {attempt}"
        quality_metadata.checks_executed.passed += 1
        Move to next Check
        BREAK (success)
    
    CATCH error:
      IF isRecoverable(error):
        Log: "Check {name} FAILED on attempt {attempt}: {error_message}"
        quality_metadata.checks_executed.failed += 1
        
        attempt += 1
        
        IF attempt <= max_attempts:
          Log: "Retrying... (attempt {attempt}/{max_attempts})"
          
          # Rerun the specific TURN that produced the error
          IF error.origin == "TURN_4":
            Rerun TURN 4 (conviction calculation)
          ELSE IF error.origin == "TURN_3":
            Rerun TURN 3 (technical analysis)
          # etc.
          
          CONTINUE WHILE loop (retry same check)
        ELSE:
          # All 3 attempts exhausted
          Log: "Check {name} FAILED after 3 attempts, marking unrecoverable"
          quality_metadata.rerun_summary.failed_checks_unrecoverable.append({
            check_name: {name},
            attempts: 3,
            final_error: {error_message}
          })
          current_state = ERROR_UNRECOVERABLE
          ESCALATE to analyst
          BREAK (failure)
      
      ELSE IF isUnrecoverable(error):
        Log: "Check {name} FAILED (unrecoverable): {error_message}"
        quality_metadata.checks_executed.failed += 1
        quality_metadata.rerun_summary.failed_checks_unrecoverable.append({
          check_name: {name},
          attempts: 1,
          final_error: {error_message}
        })
        current_state = ERROR_UNRECOVERABLE
        ESCALATE to analyst
        BREAK (failure)

END FOR each Check

IF all Checks passed:
  current_state = READY_FOR_EXECUTION
  Log: "All 10 QA gates passed. SAOutput ready for delivery."
  RETURN SAOutput with quality_metadata
```

### Error Classification

**RECOVERABLE (Auto-Retry):**
- Schema validation error (missing field, type mismatch)
- Calculation incomplete (conviction math not finalized)
- Data freshness issue (stale assumption data)
- Formatting error (output structure malformed)
- Golden Cross calculation error (EMA data incomplete)

**UNRECOVERABLE (Escalate Immediately):**
- Market data unavailable for stock
- PORTFOLIO.md file missing or corrupted
- Unsupported stock ticker
- Conviction exceeds hard cap (requires Master-Architect approval)
- User input validation failed

### Rerun Tracking in Quality Metadata

```json
{
  "rerun_summary": {
    "total_reruns": 2,
    "max_attempts_per_check": 3,
    "failed_checks_recovered": [
      "CONVICTION_CALCULATION",
      "SECTOR_CONSTRAINT_VALIDATION"
    ],
    "failed_checks_unrecoverable": [],
    "recovery_details": [
      {
        "attempt_number": 1,
        "check_name": "CONVICTION_CALCULATION",
        "turn_rerun": "TURN_4",
        "error_reason": "conviction_math_incomplete_golden_cross_multiplier",
        "error_message": "Golden Cross multiplier not applied to final conviction",
        "result": "PASSED_on_retry",
        "timestamp": "2025-12-13T09:31:15Z",
        "duration_seconds": 2.3
      },
      {
        "attempt_number": 1,
        "check_name": "SECTOR_CONSTRAINT_VALIDATION",
        "turn_rerun": "TURN_5",
        "error_reason": "sector_cap_calculation_stale_portfolio",
        "error_message": "Portfolio allocation %s do not sum to 100%",
        "result": "PASSED_on_retry",
        "timestamp": "2025-12-13T09:31:18Z",
        "duration_seconds": 1.1
      }
    ],
    "recovery_rate_percent": 100
  }
}
```

---

## QUALITY METADATA STRUCTURE

Comprehensive `quality_metadata` object populated throughout TURN 1-6:

```json
{
  "quality_metadata": {
    "analysis_id": "SA_20251213_AAPL_001",
    "analysis_timestamp": "2025-12-13T09:30:00Z",
    "turn_1_latency_seconds": 1.2,
    
    "data_freshness": {
      "portfolio_age_hours": 0.5,
      "ma_context_age_hours": 2.0,
      "alpha_context_available": false,
      "ema_cache_age_hours": 0,
      "ema_cache_completeness_percent": 100,
      "last_updated": "2025-12-13T09:30:00Z"
    },
    
    "checks_executed": {
      "total": 10,
      "passed": 10,
      "failed": 0,
      "alerts": 2
    },
    
    "rerun_summary": {
      "total_reruns": 2,
      "failed_checks_recovered": ["CONVICTION_CALCULATION", "SECTOR_CONSTRAINT"],
      "failed_checks_unrecoverable": [],
      "recovery_rate_percent": 100
    },
    
    "alerts": [
      {
        "alert_id": "DATA_FRESHNESS_MA",
        "severity": "WARNING",
        "message": "Market-Analyst output 2 hours old (cache age)",
        "penalty_points": 0,
        "disclosure": "Analysis proceeded with cached MA output"
      },
      {
        "alert_id": "PORTFOLIO_SECTOR_CAP",
        "severity": "INFO",
        "message": "Materials sector at 44% of 35% cap (Portfolio shows overweight)",
        "penalty_points": 0,
        "disclosure": "Position sizing constrained by sector cap"
      }
    ],
    
    "conviction_calculation": {
      "base": 75,
      "penalties_applied": -3,
      "adjustments_applied": 8,
      "final": 90.56,
      "range": [85, 95],
      "tier": "VERY_HIGH"
    },
    
    "async_task_status": {
      "REFRESH_MA_OUTPUT": "COMPLETED",
      "UPDATE_EMA_CACHE": "COMPLETED",
      "FETCH_ANALYST_REVISIONS": "COMPLETED"
    }
  }
}
```

---

## DEGRADATION RULES (PHASE 1 ENHANCEMENT)

When inputs missing or stale, analysis proceeds with penalty and disclosure:

| Input | Missing/Stale | Action | Penalty | Disclosure |
|-------|----------------|--------|---------|--------------|
| MA Output | >4 hours old | Use NEUTRAL regime | -2 pts | Alert in quality_metadata |
| Alpha Context | >24 hours old | Use NONE | 0 pts (no boost) | Alert in quality_metadata |
| EMA Cache | <200 days data | Skip Golden Cross | -3 pts | Alert in quality_metadata |
| Portfolio Data | >1 day old | Proceed with constraints | -1 to -2 pts | Alert in quality_metadata |
| Analyst Revisions | Missing | Skip sentiment | -1 pt | Alert in quality_metadata |

All degradations visible in `quality_metadata.alerts` and `quality_metadata.conviction_calculation`.

See Quality-Assurance-Engineer.md v8.0.7 for detailed degradation guidance.

---

## DELIVERY & SIGN-OFF

### Final SAOutput Structure

Every analysis delivery includes:
1. **Recommendation** (BUY | HOLD | SELL)
2. **Conviction Score** (0-100, with range ±5-10 points)
3. **Technical Analysis** (EMA status + Golden Cross status + multipliers shown)
4. **Fundamental Case** (3 thesis drivers + catalysts + risks)
5. **Position Management** (entry, stop, targets, triggers)
6. **Quality Metadata** (complete audit trail + decision tracking)

### Version Certification Block

```
DELIVERY CERTIFICATION (Stock-Analyst v8.12.1)

Framework: Stock-Analyst.md v8.12.1
Methodology: Cache-first TURN 1, local Golden Cross, auto-rerun system
Analysis Date: [ISO 8601 timestamp]
Analyst: [Name]
Master-Architect Sign-Off: [Approval]

TURN 1: Latency <5 sec ✓
TURN 2: Fundamental research ✓
TURN 3: EMA + Golden Cross local ✓
TURN 4: Conviction + multipliers ✓
TURN 5: Constraints validated ✓
TURN 6: QA gates all PASSED ✓

Quality Metadata: COMPLETE
Auto-Rerun Status: Recovered [N] errors
Final Conviction: [Score] ([Tier])
Recommendation: [BUY|HOLD|SELL]

Ready for Execution: YES
```

---

## FRAMEWORK INVARIANTS MAINTAINED

✅ **6-TURN Architecture:** TURN 1 (startup) → TURN 6 (delivery)  
✅ **10-Gate QA Protocol:** All 10 gates preserved (Gates 1-8 in Quality-Assurance-Engineer.md v8.0.7, Gates 9-10 in this TURN)  
✅ **3/21/200 EMA Technical Gating:** Core technical framework intact  
✅ **Conviction Multiplier Stack:** EMA × Golden Cross × penalties = Final  
✅ **QUALITY-Guardrails A–D:** All guardrails maintained, integrated with quality_metadata  
✅ **Portfolio Constraints:** 35% sector cap, 6% position cap, 15% cash buffer  

---

## VERSION HISTORY

**v8.12.1 (December 13, 2025):**
- REMOVED synchronous MA/Alpha calls from TURN 1 (blocking eliminated)
- REMOVED MA escalation for Golden Cross (computed locally)
- ADDED cache-first TURN 1 startup (<5 sec latency)
- ADDED local Golden Cross computation (0.70x–1.20x multiplier)
- ADDED auto-rerun system (up to 3 attempts per check, 80%+ recovery)
- ADDED quality_metadata structure (comprehensive audit trail)
- ADDED async background task support (MA, EMA, analyst revision refresh)
- ADDED degradation rules (graceful degradation when inputs missing/stale)
- UPDATED Gate 9 to check local Golden Cross computation
- UPDATED all references from Quality-Feedback-Engine.md to Quality-Assurance-Engineer.md v8.0.7

**v8.12 (December 11, 2025):**
- Initial Golden Cross detection framework (with escalation to MA)
- 10-gate QA protocol
- Conviction multiplier system

---

## NEXT PHASE (Phase 2)

Phase 2 will implement:
- Evidence-based penalty formula (analyst confidence % → calculated penalty)
- Conviction ranges with sensitivity analysis
- Scenario-testing tiers (UNTESTED → STRESS_VALIDATED)

Phase 1 foundations (cache-first, local Golden Cross, auto-rerun) enable Phase 2's penalty sophistication.

---

**End of Stock-Analyst.md v8.12.1**
