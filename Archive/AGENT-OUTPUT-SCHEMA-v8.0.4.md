# AGENT-OUTPUT-SCHEMA.md v8.0.4
## Phase 1 Enhancement: Quality Metadata Integration

**Version:** v8.0.4 (Phase 1)  
**Previous:** v8.0.3  
**Status:** Production Ready  
**Date:** December 13, 2025, 1:05 PM MST  
**Framework:** Stock-Analyst v8.13, Phase 1

---

## PHASE 1 CHANGES (v8.0.3 → v8.0.4)

**Added:** `quality_metadata` comprehensive object to SAOutput
- Turn 1 latency tracking
- Data freshness audit (portfolio, MA, Alpha, EMA, analyst revisions)
- Checks executed (passed, failed, alerts)
- Auto-rerun summary (recovery tracking, unrecoverable errors)
- Alert structure (severity levels, penalties, disclosures)
- Conviction calculation transparency (base, penalties, multipliers, final range)
- Async background task status

**Preserved:** All existing SAOutput, MAOutput, AlphaPicksOutput structures

---

## SAOutput JSON SCHEMA v8.0.4

```json
{
  "saoutput": {
    "analysis_id": "SA_20251213_AAPL_001",
    "analysis_timestamp": "2025-12-13T09:30:00Z",
    "ticker": "AAPL",
    "recommendation": "BUY",
    
    "conviction": {
      "base_conviction": 75,
      "base_rationale": "Strong fundamental case with 15% expected growth",
      "ema_alignment_status": "MEDIUM_HIGH_CONFIDENCE",
      "ema_multiplier": 1.05,
      "golden_cross_status": "GOLDENCROSSRECENT_0_TO_5_DAYS",
      "golden_cross_multiplier": 1.15,
      "penalties_applied": [
        {
          "penalty_type": "ASSUMPTION_DURABILITY",
          "assumption": "Earnings growth 15% YoY",
          "penalty_points": -3,
          "reason": "Based on guidance, not fully validated"
        }
      ],
      "total_penalties": -3,
      "final_conviction": 90.56,
      "conviction_range_min": 85,
      "conviction_range_max": 95,
      "conviction_tier": "VERY_HIGH"
    },

    "technical_findings": {
      "ema_3_day": 145.67,
      "ema_21_day": 143.45,
      "ema_200_day": 142.34,
      "golden_cross_analysis": {
        "status": "GOLDENCROSSRECENT_0_TO_5_DAYS",
        "multiplier": 1.15,
        "days_since_cross": 3,
        "gap_pct": 2.34,
        "escalation_required": false
      }
    },

    "fundamental_case": {
      "bull_thesis": "Strong product demand, pricing power, margin expansion",
      "key_drivers": [
        { "driver": "iPhone 16 sales acceleration", "metric": "+12% YoY", "confidence": 0.85 },
        { "driver": "Services revenue growth", "metric": "18% YoY", "confidence": 0.80 },
        { "driver": "Gross margin expansion", "metric": "48.5% vs 47.8% prior", "confidence": 0.75 }
      ],
      "key_risks": [
        { "risk": "China market slowdown", "severity": "MEDIUM", "confidence": 0.60 },
        { "risk": "Regulatory antitrust action", "severity": "MEDIUM", "confidence": 0.50 },
        { "risk": "Macro recession risk", "severity": "HIGH", "confidence": 0.65 }
      ]
    },

    "position_management": {
      "entry_price": 235.50,
      "entry_strategy": "Scale in 50% at market, 25% each at support",
      "position_size_percent": 4.5,
      "target_price": 280,
      "stop_loss": 210,
      "time_horizon_months": 12,
      "triggers": {
        "add_triggers": [
          "If conviction strengthens to 95+, add to 5%",
          "If support breaks below 220, reduce to 2%"
        ],
        "exit_triggers": [
          "Conviction drops below 65",
          "Earnings miss guidance by 10%+",
          "Macro recession confirmed"
        ]
      }
    },

    "quality_metadata": {
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

      "checks_executed": {
        "total": 10,
        "passed": 10,
        "failed": 0,
        "alerts": 2,
        "details": [
          { "check_name": "PORTFOLIO_FRESHNESS", "status": "PASSED" },
          { "check_name": "STOCK_EXISTENCE", "status": "PASSED" },
          { "check_name": "SECTOR_CONSTRAINTS", "status": "PASSED" },
          { "check_name": "CONVICTION_SIZING", "status": "PASSED" },
          { "check_name": "CASH_VALIDATION", "status": "PASSED" },
          { "check_name": "THESIS_QUALITY", "status": "PASSED" },
          { "check_name": "MA_GUIDANCE_COMPLIANCE", "status": "PASSED" },
          { "check_name": "EXECUTION_READY", "status": "PASSED" },
          { "check_name": "EMA_TECHNICAL_ANALYSIS", "status": "PASSED" },
          { "check_name": "GOLDEN_CROSS_DETECTION", "status": "PASSED" }
        ]
      },

      "rerun_summary": {
        "total_reruns": 1,
        "max_attempts_per_check": 3,
        "failed_checks_recovered": [
          "CONVICTION_CALCULATION"
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
          }
        ]
      },

      "alerts": [
        {
          "alert_id": "DATA_FRESHNESS_MA",
          "severity": "WARNING",
          "message": "Market-Analyst output 2 hours old (cache age)",
          "penalty_points": 0,
          "disclosure": "Analysis proceeded with cached MA output; fresh context being loaded asynchronously"
        },
        {
          "alert_id": "PORTFOLIO_SECTOR_CAP",
          "severity": "INFO",
          "message": "Materials sector at 44% of 35% cap (Portfolio shows overweight)",
          "penalty_points": 0,
          "disclosure": "Position sizing constrained by sector cap; recommend diversification"
        }
      ],

      "conviction_calculation": {
        "base": 75,
        "penalties_applied": -3,
        "adjustments_applied": 8,
        "final": 90.56,
        "range": [85, 95],
        "tier": "VERY_HIGH",
        "transparency": "75 × 1.05 (EMA) × 1.15 (GC) - 3 (durability penalty) = 90.56"
      },

      "async_task_status": {
        "REFRESH_MA_OUTPUT": "COMPLETED",
        "UPDATE_EMA_CACHE": "COMPLETED",
        "FETCH_ANALYST_REVISIONS": "COMPLETED"
      }
    }
  }
}
```

---

## DEGRADATION RULES & CHECK CLASSIFICATIONS (Phase 1)

### Check vs. Alert Classification (10 QA Gates)

| Gate # | Gate Name | Classification | Behavior on Failure |
|--------|-----------|---|---|
| 1 | Portfolio Freshness | **CHECK** | Hard fail, block analysis |
| 2 | Stock Existence & Uniqueness | **CHECK** | Hard fail, block analysis |
| 3 | Sector Portfolio Constraints | **CHECK** | Hard fail, block analysis |
| 4 | Conviction Position Sizing | **CHECK** | Hard fail, block analysis |
| 5 | Account Portfolio Cash Validation | **CHECK** | Hard fail, block analysis |
| 6 | Thesis Quality & Schema | **CHECK** | Hard fail, block analysis |
| 7 | Master-Architect Guidance | **ALERT** | Non-blocking, proceeds with note |
| 8 | Execution Ready | **CHECK** | Hard fail, block analysis |
| 9 | EMA Technical Analysis | **CHECK** | Hard fail, return to TURN 3 |
| 10 | Golden Cross Detection | **CHECK** | Hard fail, return to TURN 3B |

### Degradation Rules (5 Paths)

**When inputs missing/stale, analysis proceeds with penalty:**

| Input | Stale Threshold | Missing | Proceed? | Penalty | Alert Severity |
|-------|---|---|---|---|---|
| Portfolio Data | >1 day | N/A | YES | -1 to -2 pts | WARNING |
| MA Output | >4 hours | Any | YES | -2 pts | WARNING |
| Alpha Context | >24 hours | Any | YES | 0 pts | INFO |
| EMA Cache | <200 days | Any | YES | -3 pts | WARNING |
| Analyst Revisions | >5 days | Any | YES | -1 pt | INFO |

**Detailed degradation logic:**

1. **Portfolio Data Stale (>1 day):**
   - Action: Proceed with analysis
   - Penalty: -1 point (slightly stale 1-2 days) or -2 points (significantly stale >2 days)
   - Alert: `PORTFOLIODATASTALE`, WARNING severity
   - Disclosure: "Sector caps based on stale allocation; recommend refresh at market open"

2. **MA Output Stale/Missing (>4 hours or unavailable):**
   - Action: Use NEUTRAL regime (default safe position)
   - Penalty: -2 conviction points
   - Alert: `DATAFRESHNESSMA`, WARNING severity
   - Disclosure: "Analysis used cached/default NEUTRAL regime; fresh output loading asynchronously"

3. **Alpha Context Missing (>24 hours or unavailable):**
   - Action: Use NONE (no context)
   - Penalty: 0 points (no boost, not a penalty)
   - Alert: `ALPHACONTEXTMISSING`, INFO severity
   - Disclosure: "No Alpha Picks conviction boost applied; conviction from fundamental + technical"

4. **EMA Cache Incomplete (<200 days available):**
   - Action: Skip Golden Cross analysis; use 3/21/200 EMA only
   - Penalty: -3 conviction points
   - Alert: `EMADATAINCOMPLETE`, WARNING severity
   - Disclosure: "Golden Cross analysis skipped; 200-day history requirement not met"

5. **Analyst Revisions Unavailable (>5 days or missing):**
   - Action: Skip sentiment validation
   - Penalty: -1 conviction point
   - Alert: `ANALYSTREVISIONSUNAVAILABLE`, INFO severity
   - Disclosure: "Sentiment validation skipped; conviction from fundamental + technical"

---

## AUTO-RERUN SYSTEM (Phase 1)

**Recoverable errors (auto-retry up to 3 attempts):**
- Schema validation error (missing field, type mismatch)
- Calculation incomplete (conviction math not finalized)
- Data freshness issue (stale assumption data)
- Formatting error (output structure malformed)
- Golden Cross calculation error (EMA data incomplete)

**Unrecoverable errors (escalate immediately):**
- Market data unavailable for stock
- PORTFOLIO.md missing or corrupted
- Unsupported stock ticker
- Conviction exceeds hard cap
- User input validation failed

**Recovery tracked in `quality_metadata.rerun_summary`:**
- `total_reruns`: Number of rerun attempts
- `failed_checks_recovered`: Checks that passed on retry
- `failed_checks_unrecoverable`: Checks failed all 3 attempts
- `recovery_details`: Array of attempt details (turn rerun, error, result, duration)

---

## VERSION HISTORY

**v8.0.4 (December 13, 2025) – Phase 1 Enhancement**
- Added `quality_metadata` comprehensive object (40+ fields)
- Added data freshness tracking (portfolio, MA, Alpha, EMA, analyst revisions)
- Added checks execution summary (passed, failed, alerts)
- Added auto-rerun tracking (recovery details, unrecoverable errors)
- Added alert structure (severity, penalties, disclosures)
- Added conviction calculation transparency
- Added async background task status
- Integrated Check vs. Alert classification
- Integrated 5 degradation rules with penalties

**v8.0.3 (December 10, 2025)**
- Original schema baseline

---

**End of AGENT-OUTPUT-SCHEMA.md v8.0.4**
