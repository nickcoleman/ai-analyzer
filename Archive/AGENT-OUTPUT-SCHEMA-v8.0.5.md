# AGENT-OUTPUT-SCHEMA.md v8.0.5
## Phase 2 Enhancement: Conviction Ranges & Evidence-Based Penalties

**Version:** v8.0.5 (Phase 2)  
**Previous:** v8.0.4  
**Status:** Production Ready  
**Date:** December 13, 2025, 5:53 PM MST  
**Framework:** Stock-Analyst v8.13, Phase 2

---

## PHASE 2 CHANGES (v8.0.4 → v8.0.5)

**Added:** Conviction ranges and evidence-based penalty fields to SAOutput
- Conviction range (min/max) with scenario sensitivity
- Evidence-based penalty calculation transparency
- Assumption confidence % and justification fields
- Assumption validation status (UNTESTED / SCENARIO-TESTED / VALIDATED)
- Conviction sensitivity scenarios (if-assumption-validated, if-assumption-fails)

**Preserved:** All existing Phase 1 quality_metadata structure

---

## UPDATED: SAOutput JSON SCHEMA v8.0.5

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
      
      "evidence_based_penalties": [
        {
          "assumption_id": "ASSUMPTION_001",
          "assumption": "iPhone 16 sales +12% YoY",
          "assumption_type": "DURABILITY",
          "analyst_confidence_pct": 85,
          "confidence_justification": "Recent quarters show acceleration; peer data confirms strength. Reduced from 95% due to China slowdown risk.",
          "validation_status": "SCENARIO_TESTED_MILD",
          "base_penalty": -10,
          "adjusted_base_penalty": -6,
          "calculated_penalty": -0.9,
          "penalty_calculation": "(1 - 0.85) × 6 = -0.9",
          "reason": "Validated through peer comparison and recent quarterly trends"
        },
        {
          "assumption_id": "ASSUMPTION_002",
          "assumption": "Services revenue growth 18% YoY",
          "assumption_type": "EXECUTION",
          "analyst_confidence_pct": 80,
          "confidence_justification": "Management track record strong; guidance confirmed Q3 2025. Confidence at 80% due to one large customer concentration (12% of revenue).",
          "validation_status": "STRESS_VALIDATED",
          "base_penalty": -8,
          "adjusted_base_penalty": -2.4,
          "calculated_penalty": -0.48,
          "penalty_calculation": "(1 - 0.80) × 2.4 = -0.48",
          "reason": "Management 95%+ historical hit rate on guidance"
        },
        {
          "assumption_id": "ASSUMPTION_003",
          "assumption": "Gross margin expands to 48.5% (vs 47.8%)",
          "assumption_type": "DURABILITY",
          "analyst_confidence_pct": 75,
          "confidence_justification": "Cost structure improvements evident in Q3; mix shift toward services supporting expansion. Confidence at 75% due to execution risk on new product line transition.",
          "validation_status": "SCENARIO_TESTED_SEVERE",
          "base_penalty": -10,
          "adjusted_base_penalty": -5,
          "calculated_penalty": -1.25,
          "penalty_calculation": "(1 - 0.75) × 5 = -1.25",
          "reason": "Tested with 100 bps margin compression; thesis still positive"
        }
      ],
      
      "total_evidence_based_penalties": -2.63,
      
      "final_conviction_before_range": 90.56,
      "conviction_range": {
        "min": 82,
        "max": 95,
        "range_rationale": "82 pts assumes iPhone sales miss + macro slowdown; 95 pts assumes all assumptions validated + upside scenario; current point estimate 90.56 assumes base case"
      },
      "conviction_sensitivity_scenarios": {
        "base_case": 90.56,
        "if_iphone_sales_beat_15pct": 93.2,
        "if_iphone_sales_miss_10pct": 85.1,
        "if_services_accelerate_to_20pct": 92.8,
        "if_macro_recession_confirmed": 78.3,
        "if_china_market_improves": 94.1,
        "if_all_assumptions_validated": 95.0,
        "if_thesis_breaker_occurs": 65.0
      },
      
      "conviction_tier": "VERY_HIGH",
      "position_size_recommendation": "4-5% of portfolio",
      "tier_enforcement": {
        "unvalidated_assumptions_count": 0,
        "tier_guidance_range": "70-75",
        "tier_hard_cap": "65-80",
        "conviction_vs_guidance": "WITHIN_GUIDANCE",
        "conviction_vs_hard_cap": "WITHIN_HARD_CAP",
        "override_required": false
      }
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
      "bull_thesis": "Strong product demand, pricing power, margin expansion, services acceleration",
      "key_drivers": [
        {
          "driver": "iPhone 16 sales acceleration",
          "metric": "+12% YoY",
          "confidence": 0.85,
          "evidence": "Recent quarters trending at +13%; peer data shows +11% category average",
          "validation_status": "SCENARIO_TESTED_MILD"
        },
        {
          "driver": "Services revenue growth",
          "metric": "18% YoY",
          "confidence": 0.80,
          "evidence": "Management guidance issued Q3 2025; track record 95% hit rate",
          "validation_status": "STRESS_VALIDATED"
        },
        {
          "driver": "Gross margin expansion",
          "metric": "48.5% vs 47.8% prior",
          "confidence": 0.75,
          "evidence": "Cost improvements evident in Q3; services mix shift positive",
          "validation_status": "SCENARIO_TESTED_SEVERE"
        }
      ],
      "key_risks": [
        {
          "risk": "China market slowdown",
          "severity": "MEDIUM",
          "confidence": 0.60,
          "upside_if_validated": "Additional +3% growth upside",
          "downside_if_materializes": "-8% conviction impact"
        },
        {
          "risk": "Regulatory antitrust action",
          "severity": "MEDIUM",
          "confidence": 0.50,
          "upside_if_validated": "Minimal impact if swift resolution",
          "downside_if_materializes": "-10% conviction impact"
        },
        {
          "risk": "Macro recession risk",
          "severity": "HIGH",
          "confidence": 0.65,
          "upside_if_validated": "Market resilience improves +5% conviction",
          "downside_if_materializes": "-12% conviction impact (services compression, capex delay)"
        }
      ]
    },

    "position_management": {
      "entry_price": 235.50,
      "entry_strategy": "Scale in 50% at market, 25% each at support levels (228, 220)",
      "position_size_percent": 4.5,
      "position_size_conviction_alignment": "HIGH tier (70-75 guidance, conviction 90.56 point estimate) → 4-5% portfolio allocation",
      "target_price": 280,
      "price_target_rationale": "DCF valuation $280-290 range; 12-month horizon; 19% upside from entry",
      "stop_loss": 210,
      "stop_loss_trigger": "Technical breakdown + conviction drops below 70 (iPhone miss confirmation)",
      "time_horizon_months": 12,
      "conviction_triggers": {
        "add_triggers": [
          "If China market improves (conviction +4 pts) → expand to 5%",
          "If all assumptions validated quarter-over-quarter → expand to 5%"
        ],
        "exit_triggers": [
          "Conviction drops below 70 (mid-tier boundary)",
          "Earnings miss guidance by 10%+",
          "Macro recession confirmed with earnings cuts",
          "iPhone 16 ramp fails to materialize"
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
            "error_reason": "conviction_range_calculation_incomplete",
            "error_message": "Assumption evidence-based penalties not calculated; conviction range missing",
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
          "message": "Technology sector at 32% of 35% cap (Portfolio shows near-cap allocation)",
          "penalty_points": 0,
          "disclosure": "Position sizing constrained by sector cap; consider diversification"
        }
      ],

      "conviction_calculation": {
        "base": 75,
        "evidence_based_penalties_applied": -2.63,
        "ema_adjustment": 1.05,
        "golden_cross_adjustment": 1.15,
        "final_point_estimate": 90.56,
        "conviction_range": [82, 95],
        "range_rationale": "Low case: iPhone miss + macro recession = 82; high case: assumptions validated + upside = 95; base case (point estimate) = 90.56",
        "tier": "VERY_HIGH",
        "transparency": "75 × 1.05 (EMA) × 1.15 (GC) - 2.63 (evidence-based penalties) = 90.56 → range [82-95]"
      },

      "assumption_evidence_transparency": {
        "total_assumptions": 3,
        "unvalidated_count": 0,
        "partially_validated_count": 3,
        "validated_count": 0,
        "average_analyst_confidence_pct": 80,
        "average_calculated_penalty": -0.88,
        "total_penalties": -2.63,
        "note": "All three assumptions partially validated with scenario testing; confidence levels justified with specific evidence"
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

## CONVICTION RANGE METHODOLOGY (NEW - Phase 2)

### Overview

Conviction ranges replace point estimates to communicate uncertainty:

```
Instead of:  conviction = 90.56 (false precision)

Now output:  conviction_range = [82, 95]
             point_estimate = 90.56
             range_rationale = "[explanation]"
```

### Range Calculation Formula

```
range_min = point_estimate - max_downside_from_assumptions
range_max = point_estimate + upside_boost_if_validated

Where:
- max_downside = maximum conviction loss if any single assumption fails
- upside_boost = additional conviction if all assumptions validated + positive surprises
```

### Concrete Range Example

```
Analysis: Intel (INTC)

Base conviction: 68%
Assumptions:
1. Fab ramp executes on schedule (Execution, 70% confidence)
   - Calculated penalty: -2.4 pts
   - If fails: -8 additional pts (execution derailed)
   
2. Process tech advances proceed (Durability, 65% confidence)
   - Calculated penalty: -3.5 pts
   - If fails: -6 additional pts (tech delayed)
   
3. Competitive pressure from Taiwan remains manageable (Macro, 60% confidence)
   - Calculated penalty: -4.8 pts
   - If fails: -7 additional pts (market share loss)

Point Estimate: 68 - 2.4 - 3.5 - 4.8 = 57.3%

Downside Scenario (all fail): 57.3 - max(8, 6, 7) = 49.3%
Upside Scenario (all validate + beats): 57.3 + 6 (upside) = 63.3%

Conviction Range: [49–63] with point estimate 57%
```

---

## CONVICTION TIER ENFORCEMENT (UPDATED - Phase 2)

### Tier Validation Against Hard Cap

**New field in conviction tier enforcement:**

```json
{
  "tier_enforcement": {
    "unvalidated_assumptions_count": 0,
    "tier_guidance_range": "70-75",
    "tier_hard_cap": "65-80",
    "conviction_vs_guidance": "WITHIN_GUIDANCE | OUTSIDE_GUIDANCE",
    "conviction_vs_hard_cap": "WITHIN_HARD_CAP | OUTSIDE_HARD_CAP",
    "override_required": false,
    "override_evidence": "[If applicable]"
  }
}
```

**Logic:**

```
IF conviction < hard_cap_min OR conviction > hard_cap_max:
  → ESCALATE to Master-Architect (blocking)
  
ELSE IF conviction < guidance_min OR conviction > guidance_max:
  → FLAG in quality_metadata (non-blocking, evidence required)
  
ELSE:
  → PROCEED (no flag)
```

---

## ASSUMPTION EVIDENCE TRANSPARENCY SECTION (NEW - Phase 2)

**Added to quality_metadata:**

```json
{
  "assumption_evidence_transparency": {
    "total_assumptions": 3,
    "unvalidated_count": 0,
    "partially_validated_count": 3,
    "validated_count": 0,
    "average_analyst_confidence_pct": 80,
    "average_calculated_penalty": -0.88,
    "total_penalties": -2.63,
    "summary_table": [
      {
        "assumption": "iPhone sales +12%",
        "type": "DURABILITY",
        "confidence": 85,
        "validation_status": "SCENARIO-TESTED-MILD",
        "penalty": -0.9
      },
      {
        "assumption": "Services growth 18%",
        "type": "EXECUTION",
        "confidence": 80,
        "validation_status": "STRESS-VALIDATED",
        "penalty": -0.48
      },
      {
        "assumption": "Margin to 48.5%",
        "type": "DURABILITY",
        "confidence": 75,
        "validation_status": "SCENARIO-TESTED-SEVERE",
        "penalty": -1.25
      }
    ],
    "note": "All assumptions partially validated with scenario testing; analyst confidence justified"
  }
}
```

---

## SENSITIVITY SCENARIOS (NEW - Phase 2)

**conviction_sensitivity_scenarios object in conviction section:**

Shows conviction impact for various scenarios:

```json
{
  "conviction_sensitivity_scenarios": {
    "base_case": 90.56,
    "if_iphone_sales_beat_15pct": 93.2,
    "if_iphone_sales_miss_10pct": 85.1,
    "if_services_accelerate_to_20pct": 92.8,
    "if_macro_recession_confirmed": 78.3,
    "if_china_market_improves": 94.1,
    "if_all_assumptions_validated": 95.0,
    "if_thesis_breaker_occurs": 65.0
  }
}
```

**Usage:** Analysts and decision-makers can see conviction impact for various outcomes without running separate scenarios.

---

## DEGRADATION RULES (Phase 1 - MAINTAINED)

All Phase 1 degradation rules maintained in v8.0.5:

| Input | Missing/Stale | Action | Penalty | Disclosure |
|-------|---|---|---|---|
| MA Output | >4 hours old | Use NEUTRAL regime | -2 pts | Alert in quality_metadata |
| Alpha Context | >24 hours old | Use NONE | 0 pts (no boost) | Alert in quality_metadata |
| EMA Cache | <200 days data | Skip Golden Cross | -3 pts | Alert in quality_metadata |
| Portfolio Data | >1 day old | Proceed with constraints | -1 to -2 pts | Alert in quality_metadata |

---

## VERSION HISTORY

| Version | Date | Summary | Status |
|---------|------|---------|--------|
| v8.0.4 | Dec 13, 2025 | Phase 1: Quality metadata integration | Superseded |
| **v8.0.5** | **Dec 13, 2025** | **Phase 2: Conviction ranges, evidence-based penalties, assumption transparency** | **Active** |

---

**END OF AGENT-OUTPUT-SCHEMA.md v8.0.5**

**Status:** Production Ready - Phase 2 Enhancements Complete  
**Last Updated:** December 13, 2025, 5:53 PM MST  
**Phase Authority:** Master-Architect (Phase 2 Execution)  
**Approval:** Phase 2 DIRECTIVE AUTHORIZED  
**Next Update:** Phase 3 (Stock-Analyst v8.13 TURN 4 modifications)