# AGENT-OUTPUT-SCHEMA.md v8.0.6
## Phase 3 Update: Input Sources Metadata

**Version:** v8.0.6  
**Previous:** v8.0.5 (Phase 2)  
**Status:** Production Ready  
**Date:** December 13, 2025, 7:34 PM MST  
**Framework:** Stock-Analyst v8.13  
**Phase:** Phase 3 (Async Input Model - Cache-First Context Loading)  
**Changes:** Added `input_sources` section to quality_metadata; documents origin, age, and penalty for each input

---

## PHASE 3 CHANGES SUMMARY

### New Additions
1. **input_sources Object:** Complete provenance tracking for every input
2. **Source Attribution:** Where each input came from (local, cached, async-fetched, default)
3. **Input Age Tracking:** Age in hours for cached inputs (determines penalty application)
4. **Penalty Transparency:** Which inputs triggered penalties and why
5. **Async Task Status:** Completion status of background refresh tasks

### Schema Invariants Maintained
- ✓ All Phase 2 fields preserved (conviction_calculation, assumption_evidence_transparency, etc.)
- ✓ Output structure remains SAOutput (no breaking changes)
- ✓ Quality metadata fully expanded (now includes input_sources)
- ✓ Backward compatible with Phase 1-2 consumers

---

## COMPLETE SAOutput JSON SCHEMA (v8.0.6)

```json
{
  "analysis_metadata": {
    "analysis_id": "SA_20251213_AAPL_001",
    "analysis_timestamp": "2025-12-13T09:30:00Z",
    "analyst": "Stock-Analyst (AI)",
    "framework_version": "Stock-Analyst v8.13",
    "framework_phase": "Phase 3 (Cache-First + Async Inputs)",
    "output_schema_version": "v8.0.6",
    "status": "READY_FOR_EXECUTION",
    "execution_ready": true
  },
  
  "recommendation": {
    "action": "BUY",
    "conviction_percent": 87.88,
    "conviction_range": {
      "min": 80,
      "max": 94
    },
    "tier": "HIGH",
    "confidence_qualifiers": [
      "Strong technical foundation (Golden Cross recent)",
      "Solid fundamental case with modest assumptions",
      "Well-managed risk via conviction-linked position sizing"
    ],
    "primary_driver": "iPhone 16 sales acceleration + Services growth",
    "primary_risk": "Macro recession or China market weakness",
    "catalysts_near_term": [
      "Q1 2026 earnings beat (iPhone unit growth)",
      "Services guidance raise (recurring revenue expansion)"
    ]
  },
  
  "thesis": {
    "investment_case": "Apple enters favorable cycle with iPhone 16 sales acceleration and Services recurring revenue expansion. Technical setup supports institutional buying (Golden Cross fresh). Valuation reasonable (18x forward P/E vs. 20-year median 22x). Primary risk is macro recession.",
    "bull_case": "iPhone 16 upgrade cycle strongest since 12/13; Services 18%+ growth sustainable through 2027; Margin expansion to 48%+ via mix shift. Consensus underestimating Services TAM. Conviction: 85%.",
    "bear_case": "China smartphone market saturated; iPhone 16 demand disappoints (10% vs. guided 12%). Margin pressure from OLED costs. Services growth decelerates. Risk scenario: iPhone misses, returns -8% vs. thesis +15%.",
    "valuation": {
      "current_price": 245.50,
      "fair_value_dcf": 275,
      "fair_value_multiples": 280,
      "upside_downside": "+12% to +14% to fair value",
      "p_e_current": 28.5,
      "p_e_target": 32,
      "peg_ratio": 1.1
    }
  },
  
  "technical_analysis": {
    "ema_status": {
      "price_current": 245.50,
      "ema_3_day": 245.23,
      "ema_21_day": 243.45,
      "ema_200_day": 238.90,
      "alignment": "BULLISHMOMENTUM",
      "alignment_score": 1.12,
      "interpretation": "Price > EMA3 > EMA21 > EMA200; all moving averages rising; strong uptrend"
    },
    "golden_cross_status": {
      "status": "GOLDENCROSSRECENT_0_TO_5_DAYS",
      "status_label": "Fresh Golden Cross (0–5 days old)",
      "multiplier": 1.15,
      "days_since_cross": 3,
      "cross_date": "2025-12-10",
      "ema_50_current": 244.12,
      "ema_200_current": 238.90,
      "gap_percent": 2.18,
      "gap_trend": "EXPANDING",
      "signal_quality": "FRESH_CONFIRMATION",
      "interpretation": "EMA50 just crossed above EMA200 three days ago; institutional trend confirmation likely to follow; timing advantage for new entry"
    },
    "technical_summary": "Strong technical setup: bullish EMA alignment (1.12x multiplier) + recent Golden Cross (1.15x multiplier) = 1.27x conviction boost. Technical supports fundamental thesis."
  },
  
  "fundamental_analysis": {
    "company": "Apple Inc.",
    "ticker": "AAPL",
    "sector": "Technology",
    "industry": "Consumer Electronics / Software & Services",
    "market_cap": "$2,850B",
    "key_metrics": {
      "revenue_ttm": "383B",
      "revenue_growth_yoy": "2.2%",
      "eps_ttm": 8.52,
      "eps_growth_yoy": 12.4,
      "roe": "86%",
      "gross_margin": "46.2%",
      "operating_margin": "29.8%"
    },
    "key_assumptions": [
      {
        "assumption": "iPhone 16 sales +12% YoY",
        "type": "Durability",
        "analyst_confidence_pct": 85,
        "validation_status": "SCENARIO-TESTED-MILD",
        "evidence": "Recent quarters show 11% unit growth; upgrade cycle strengthening",
        "calculated_penalty": -0.9,
        "scenario_if_fails": "Conviction to 82.5% (if sales miss 10%)"
      },
      {
        "assumption": "Services growth 18% through 2027",
        "type": "Execution",
        "analyst_confidence_pct": 80,
        "validation_status": "STRESS-VALIDATED",
        "evidence": "Historical 15%+ growth; TAM expanding (AppleCare, subscriptions, cloud)",
        "calculated_penalty": -0.53,
        "scenario_if_fails": "Conviction to 78.3% (if growth drops to 12%)"
      },
      {
        "assumption": "Margin expansion to 48%+ via mix shift",
        "type": "Durability",
        "analyst_confidence_pct": 75,
        "validation_status": "SCENARIO-TESTED-SEVERE",
        "evidence": "Services mix already 22% of revenue (highest margin); OLED cost declining",
        "calculated_penalty": -1.25,
        "scenario_if_fails": "Conviction to 70% (if margins compress to 46%)"
      }
    ]
  },
  
  "quality_metadata": {
    "analysis_id": "SA_20251213_AAPL_001",
    "analysis_timestamp": "2025-12-13T09:30:00Z",
    
    "turn_1_latency_seconds": 1.2,
    
    "input_sources": {
      "portfolio": {
        "source": "LOCAL_PORTFOLIO.md",
        "loaded_timestamp": "2025-12-13T09:30:00Z",
        "age_hours": 0.5,
        "status": "READY",
        "penalty_points": 0,
        "note": "Portfolio loaded from local file; <1 hour old (very fresh)"
      },
      "macro_regime": {
        "source": "CACHED_MA (2 hours old) | DEFAULT (NEUTRAL)",
        "loaded_timestamp": "2025-12-13T09:30:00Z",
        "age_hours": 2.0,
        "cache_status": "FRESH",
        "value": "NEUTRAL",
        "penalty_applied_in_turn4": 0,
        "note": "Market-Analyst output cached; <4 hour max age; used as-is (no penalty)",
        "async_refresh_status": "COMPLETED (BULLISH regime confirmed)"
      },
      "alpha_context": {
        "source": "DEFAULT_NONE",
        "loaded_timestamp": "2025-12-13T09:30:00Z",
        "age_hours": null,
        "available": false,
        "penalty_points": 0,
        "note": "Alpha-Picks context not available at TURN 1; no penalty (confirmatory only)",
        "async_refresh_status": "COMPLETED (no recent picks affecting AAPL)"
      },
      "ema_data": {
        "source": "LOCAL_CACHE (COMPLETE - 200+ days)",
        "loaded_timestamp": "2025-12-13T09:30:00Z",
        "age_hours": 0,
        "days_available": 200,
        "cache_completeness_percent": 100,
        "golden_cross_enabled": true,
        "penalty_points": 0,
        "note": "EMA cache complete with 200 trading days; Golden Cross computation enabled",
        "async_refresh_status": "COMPLETED (today's close loaded; cache current)"
      },
      "analyst_revisions": {
        "source": "CACHED (12 hours old)",
        "loaded_timestamp": "2025-12-13T09:30:00Z",
        "age_hours": 12,
        "available": true,
        "penalty_applied_in_turn4": 0,
        "note": "Analyst revisions cached; <1 day old; sentiment validation enabled",
        "async_refresh_status": "COMPLETED (no significant revisions since cache)"
      },
      "penalties_applied": {
        "ma_stale_or_missing": 0,
        "ema_incomplete": 0,
        "analyst_revisions_missing": 0,
        "total_input_penalty": 0,
        "transparency": "All inputs fresh; no input-source penalties applied. Final conviction = base + multipliers - evidence_penalties (no input penalties)."
      }
    },
    
    "async_task_status": {
      "REFRESH_MA_OUTPUT": {
        "status": "COMPLETED",
        "started_timestamp": "2025-12-13T09:30:01Z",
        "completed_timestamp": "2025-12-13T09:30:02.1Z",
        "duration_seconds": 1.1,
        "max_duration_seconds": 30,
        "result": "MA regime confirmed NEUTRAL (was cached); no change from startup default"
      },
      "UPDATE_EMA_CACHE": {
        "status": "COMPLETED",
        "started_timestamp": "2025-12-13T09:30:01Z",
        "completed_timestamp": "2025-12-13T09:30:02.5Z",
        "duration_seconds": 1.5,
        "max_duration_seconds": 20,
        "result": "EMA cache updated with today's close; 200+ days available; Golden Cross status confirmed"
      },
      "UPDATE_ALPHA_CONTEXT": {
        "status": "COMPLETED",
        "started_timestamp": "2025-12-13T09:30:01Z",
        "completed_timestamp": "2025-12-13T09:30:01.8Z",
        "duration_seconds": 0.8,
        "max_duration_seconds": 30,
        "result": "Alpha context fetched; no recent picks conflict with AAPL thesis"
      },
      "FETCH_ANALYST_REVISIONS": {
        "status": "COMPLETED",
        "started_timestamp": "2025-12-13T09:30:01Z",
        "completed_timestamp": "2025-12-13T09:30:01.6Z",
        "duration_seconds": 0.6,
        "max_duration_seconds": 15,
        "result": "Analyst revisions fetched; momentum slightly positive (3 upward revisions, 1 downward)"
      },
      "all_tasks_completed_or_timed_out": true,
      "tasks_completed_count": 4,
      "tasks_timed_out_count": 0,
      "total_async_duration_seconds": 1.5,
      "interpretation": "All async tasks completed within SLA; no timeouts; input freshness optimal"
    },
    
    "conviction_calculation": {
      "base": 75,
      "ema_multiplier": 1.12,
      "golden_cross_multiplier": 1.15,
      "multiplied_conviction": 97.41,
      "evidence_based_penalties_applied": -2.68,
      "input_source_penalties_applied": 0,
      "final_point_estimate": 94.73,
      "conviction_range": {
        "min": 82,
        "max": 101,
        "range_rationale": "Downside from macro recession (-12 pts) or Services growth decel (-8 pts); Upside from all assumptions validated (+6 pts)"
      },
      "tier": "VERY_HIGH",
      "transparency": "75 × 1.12 × 1.15 = 97.41; minus evidence penalties -2.68; minus input penalties -0; = 94.73 final. Range [82-101] from scenario sensitivity."
    },
    
    "assumption_evidence_transparency": {
      "total_assumptions": 3,
      "unvalidated_count": 0,
      "partially_validated_count": 3,
      "validated_count": 0,
      "average_analyst_confidence_pct": 80,
      "average_calculated_penalty": -0.89,
      "total_penalties": -2.68,
      "breakdown": [
        {
          "assumption": "iPhone 16 sales +12%",
          "type": "Durability",
          "analyst_confidence_pct": 85,
          "validation_status": "SCENARIO-TESTED-MILD",
          "base_penalty": -10,
          "adjusted_penalty": -6,
          "calculated_penalty": -0.9
        },
        {
          "assumption": "Services growth 18%",
          "type": "Execution",
          "analyst_confidence_pct": 80,
          "validation_status": "STRESS-VALIDATED",
          "base_penalty": -8,
          "adjusted_penalty": -2.64,
          "calculated_penalty": -0.53
        },
        {
          "assumption": "Margin to 48%+",
          "type": "Durability",
          "analyst_confidence_pct": 75,
          "validation_status": "SCENARIO-TESTED-SEVERE",
          "base_penalty": -10,
          "adjusted_penalty": -5,
          "calculated_penalty": -1.25
        }
      ],
      "note": "All assumptions partially validated; analyst confidence 75–85% well-supported by evidence"
    },
    
    "checks_executed": {
      "total": 10,
      "passed": 10,
      "failed": 0,
      "alerts": 0,
      "details": [
        {
          "check_name": "PORTFOLIO_FRESHNESS",
          "status": "PASSED",
          "note": "Portfolio 0.5 hours old; <1 day threshold"
        },
        {
          "check_name": "STOCK_EXISTENCE",
          "status": "PASSED",
          "note": "AAPL valid ticker; found in portfolio"
        },
        {
          "check_name": "SECTOR_CONSTRAINTS",
          "status": "PASSED",
          "note": "Technology 32% of 35% cap; 3% available for position"
        },
        {
          "check_name": "CONVICTION_SIZING",
          "status": "PASSED",
          "note": "Conviction 94.73% → HIGH tier → 3-5% position; positioned at 4%"
        },
        {
          "check_name": "CASH_VALIDATION",
          "status": "PASSED",
          "note": "Cash 35% of portfolio; trade uses $10K; cash remaining 34.5%"
        },
        {
          "check_name": "THESIS_QUALITY",
          "status": "PASSED",
          "note": "Thesis clear; assumptions documented; risks identified"
        },
        {
          "check_name": "MA_GUIDANCE_COMPLIANCE",
          "status": "PASSED",
          "note": "NEUTRAL regime allows BUY; no MA conflicts"
        },
        {
          "check_name": "EXECUTION_READY",
          "status": "PASSED",
          "note": "All constraints met; ready for execution"
        },
        {
          "check_name": "EMA_TECHNICAL_ANALYSIS",
          "status": "PASSED",
          "note": "3/21/200 EMA computed; BULLISHMOMENTUM status; multiplier 1.12x applied"
        },
        {
          "check_name": "GOLDEN_CROSS_DETECTION",
          "status": "PASSED",
          "note": "Golden Cross fresh (3 days); EMA50 > EMA200 confirmed; multiplier 1.15x applied"
        }
      ]
    },
    
    "rerun_summary": {
      "total_reruns": 1,
      "max_attempts_per_check": 3,
      "failed_checks_recovered": [
        "CONVICTION_RANGE_CALCULATION"
      ],
      "failed_checks_unrecoverable": [],
      "recovery_details": [
        {
          "attempt_number": 1,
          "check_name": "CONVICTION_RANGE_CALCULATION",
          "turn_rerun": "TURN_4",
          "error_reason": "conviction_range_boundaries_incomplete",
          "error_message": "Downside/upside scenarios not calculated for all assumptions",
          "result": "PASSED_on_retry",
          "timestamp": "2025-12-13T09:31:15Z",
          "duration_seconds": 1.8
        }
      ],
      "recovery_rate_percent": 100
    },
    
    "alerts": [
      {
        "alert_id": "INFO_ASYNC_MA_COMPLETE",
        "severity": "INFO",
        "message": "Async MA refresh completed; regime confirmed NEUTRAL",
        "penalty_points": 0,
        "disclosure": "Background refresh confirmed cached regime; no conviction adjustment needed"
      }
    ]
  },
  
  "position_management": {
    "entry_trigger": "Technical Golden Cross fresh; fundamental catalyst Q1 2026 earnings",
    "entry_price": 245.50,
    "entry_size_percent": 4.0,
    "entry_size_dollars": 29_000,
    "entry_conviction": 94.73,
    "entry_thesis_score": "STRONG (3/3 assumptions supported)",
    
    "profit_target_1": {
      "target_price": 260,
      "target_conviction_trigger": 95,
      "rationale": "5% near-term target; take partial profits (30%)"
    },
    "profit_target_2": {
      "target_price": 280,
      "target_conviction_trigger": 95,
      "rationale": "14% target based on fair value DCF; exit 50%"
    },
    "profit_target_3": {
      "target_price": 300,
      "target_conviction_trigger": 95,
      "rationale": "22% bull case target; exit final 20%"
    },
    
    "stop_loss_hard": {
      "stop_price": 225,
      "stop_conviction_trigger": 40,
      "rationale": "Technical breakdown (EMA21 < EMA200); fundamental deterioration",
      "loss_percent": -8.3,
      "stop_type": "TECHNICAL_OR_FUNDAMENTAL"
    },
    "stop_loss_soft": {
      "stop_price": 235,
      "stop_conviction_trigger": 60,
      "rationale": "Conviction drops below MEDIUM tier; forces exit if thesis breaks",
      "loss_percent": -4.3
    },
    
    "rebalance_triggers": [
      {
        "trigger": "Conviction drops below 70",
        "action": "REDUCE to 2.5%",
        "rationale": "Thesis weakening; reduce exposure"
      },
      {
        "trigger": "Conviction rises above 95",
        "action": "MAINTAIN 4% (at cap)",
        "rationale": "Stay at conviction-appropriate sizing; no upsize beyond tier"
      },
      {
        "trigger": "Tech sector reaches 35% (cap)",
        "action": "TRIM to 3%",
        "rationale": "Sector cap constraint; maintain positions in order (highest conviction first)"
      }
    ],
    
    "conviction_linked_sizing": {
      "conviction_tier": "HIGH",
      "sizing_range": "3-5%",
      "assigned_size": "4%",
      "rationale": "High conviction (94.73%) positioned at midpoint of tier range"
    }
  },
  
  "risk_assessment": {
    "conviction_range_width": 19,
    "conviction_range_width_interpretation": "MODERATE (±9 pts range); downside risk from macro recession, upside from assumptions validation",
    
    "primary_risks": [
      {
        "risk": "Macro recession reduces smartphone demand",
        "probability_percent": 15,
        "impact": "-8 pts conviction",
        "mitigation": "Services growth more resilient; recurring revenue acts as cash flow cushion"
      },
      {
        "risk": "iPhone 16 sales disappoint (miss 10%)",
        "probability_percent": 20,
        "impact": "-5 pts conviction",
        "mitigation": "Recent quarters tracking above guidance; upgrade cycle intact"
      },
      {
        "risk": "China market weakness (geopolitical)",
        "probability_percent": 25,
        "impact": "-3 pts conviction (20% of revenue exposed)",
        "mitigation": "India market growing +50% YoY; offsets China weakness"
      }
    ],
    
    "conviction_sensitivity": {
      "base_case": 94.73,
      "if_iphone_beats_15pct": 99.0,
      "if_iphone_misses_10pct": 82.5,
      "if_services_accelerates_to_20pct": 96.2,
      "if_macro_recession": 72.0,
      "if_all_assumptions_validated": 101.0,
      "if_thesis_breaker_china_collapse": 55.0
    }
  }
}
```

---

## Section: input_sources Object Details

### Structure

```json
{
  "input_sources": {
    "portfolio": {
      "source": "LOCAL_PORTFOLIO.md | CACHED | DEFAULT",
      "loaded_timestamp": "ISO 8601",
      "age_hours": number,
      "status": "READY | STALE | UNAVAILABLE",
      "penalty_points": 0,
      "note": "Human-readable explanation"
    },
    "macro_regime": {
      "source": "CACHED_MA (X hours old) | DEFAULT (NEUTRAL)",
      "loaded_timestamp": "ISO 8601",
      "age_hours": number,
      "cache_status": "FRESH | STALE | DEFAULT",
      "value": "BULLISH | NEUTRAL | BEARISH",
      "penalty_applied_in_turn4": -2 or 0,
      "note": "Explanation",
      "async_refresh_status": "QUEUED | COMPLETED | TIMEOUT"
    },
    "alpha_context": {
      "source": "CACHED | USER_PROVIDED | NONE",
      "loaded_timestamp": "ISO 8601",
      "age_hours": number or null,
      "available": true | false,
      "penalty_points": 0,
      "note": "Explanation",
      "async_refresh_status": "QUEUED | COMPLETED | TIMEOUT"
    },
    "ema_data": {
      "source": "LOCAL_CACHE (COMPLETE | INCOMPLETE)",
      "loaded_timestamp": "ISO 8601",
      "age_hours": number,
      "days_available": number,
      "cache_completeness_percent": number,
      "golden_cross_enabled": true | false,
      "penalty_points": -3 or 0,
      "note": "Explanation",
      "async_refresh_status": "QUEUED | COMPLETED | TIMEOUT"
    },
    "analyst_revisions": {
      "source": "CACHED | SKIPPED",
      "loaded_timestamp": "ISO 8601",
      "age_hours": number or null,
      "available": true | false,
      "penalty_applied_in_turn4": -1 or 0,
      "note": "Explanation",
      "async_refresh_status": "QUEUED | COMPLETED | TIMEOUT"
    },
    "penalties_applied": {
      "ma_stale_or_missing": -2 or 0,
      "ema_incomplete": -3 or 0,
      "analyst_revisions_missing": -1 or 0,
      "total_input_penalty": number,
      "transparency": "Explanation of what penalties were applied and why"
    }
  }
}
```

---

## Section: Async Task Status Details

### Structure

```json
{
  "async_task_status": {
    "REFRESH_MA_OUTPUT": {
      "status": "QUEUED | COMPLETED | TIMEOUT",
      "started_timestamp": "ISO 8601",
      "completed_timestamp": "ISO 8601 or null",
      "duration_seconds": number or null,
      "max_duration_seconds": 30,
      "result": "Outcome description"
    },
    "UPDATE_EMA_CACHE": {
      "status": "QUEUED | COMPLETED | TIMEOUT",
      "started_timestamp": "ISO 8601",
      "completed_timestamp": "ISO 8601 or null",
      "duration_seconds": number or null,
      "max_duration_seconds": 20,
      "result": "Outcome description"
    },
    "UPDATE_ALPHA_CONTEXT": {
      "status": "QUEUED | COMPLETED | TIMEOUT",
      "started_timestamp": "ISO 8601",
      "completed_timestamp": "ISO 8601 or null",
      "duration_seconds": number or null,
      "max_duration_seconds": 30,
      "result": "Outcome description"
    },
    "FETCH_ANALYST_REVISIONS": {
      "status": "QUEUED | COMPLETED | TIMEOUT",
      "started_timestamp": "ISO 8601",
      "completed_timestamp": "ISO 8601 or null",
      "duration_seconds": number or null,
      "max_duration_seconds": 15,
      "result": "Outcome description"
    },
    "all_tasks_completed_or_timed_out": true | false,
    "tasks_completed_count": number,
    "tasks_timed_out_count": number,
    "total_async_duration_seconds": number,
    "interpretation": "Summary of async results"
  }
}
```

---

## Backward Compatibility

All Phase 1-2 fields preserved. Phase 3 additions:
- `input_sources` (new section in quality_metadata)
- `async_task_status` (new section in quality_metadata)

Existing consumers of SAOutput continue to work. New consumers can access input_sources for full provenance tracking.

---

## Compliance Checklist

**Before schema delivery, verify:**

- ☐ input_sources section complete (all inputs documented)
- ☐ Source attribution clear (local, cached, default, user-provided)
- ☐ Input ages logged (hours) for cached inputs
- ☐ Penalties transparent (what penalty + why)
- ☐ Async task status logged (COMPLETED or TIMEOUT)
- ☐ All Phase 1-2 fields preserved (backward compatible)
- ☐ JSON structure valid and parseable
- ☐ Examples realistic and complete

---

## VERSION HISTORY

| Version | Date | Summary | Status |
|---------|------|---------|--------|
| v8.0.4 | Dec 12, 2025 | Phase 1: Basic output structure | Superseded |
| v8.0.5 | Dec 13, 2025 | Phase 2: Evidence-based penalties, conviction ranges | Superseded |
| **v8.0.6** | **Dec 13, 2025** | **Phase 3: Input sources metadata, async task status** | **Active** |

---

**END OF AGENT-OUTPUT-SCHEMA.md v8.0.6**

**Status:** Production Ready - Phase 3 Deliverable  
**Last Updated:** December 13, 2025, 7:34 PM MST  
**Phase Authority:** Master-Architect (Phase 3 Execution)  
**Approval:** Phase 3 DIRECTIVE AUTHORIZED  
**Next Phase:** Phase 4 Ready