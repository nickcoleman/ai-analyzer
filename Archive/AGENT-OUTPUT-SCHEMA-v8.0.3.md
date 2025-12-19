# AGENT-OUTPUT-SCHEMA.md v8.0.3

**Version:** v8.0.3 - With QA Enforcement & Alpha Coordination  
**Framework:** v8 - Tested & Compliant  
**Status:** Ready for upload  
**Last Updated:** December 10, 2025, 8:29 AM MST

---

## OVERVIEW

AGENT-OUTPUT-SCHEMA defines JSON structure for SAOutput (Stock-Analyst output) and MAOutput (Market-Analyst output). This update integrates the **v8.1 Enforcement Schema** (formerly standalone) directly into the main output definition, alongside existing Alpha Coordination logic.

---

## SAOutput Schema – QA ENFORCEMENT UPDATE (v8.1)

### Structure: SAOutput (Standardized)

**Purpose:** Defines the mandatory structure for Stock-Analyst output. The `v8.1` update adds strict schema requirements for QA Gates, Citations, and Conviction Logic to prevent procedural bypass.

### SAOutput Object

```json
{
  "sa_output": {
    "meta": {
      "timestamp": "ISO_8601",
      "analyst_version": "v8.0.8",
      "schema_version": "v8.1"
    },
    
    "stock": "TICKER_SYMBOL",
    "recommendation": "enum [BUY | HOLD | SELL]",
    
    "qa_gate_results": {
      "gate_1_freshness": "enum [PASS | FAIL | ESCALATE]",
      "gate_2_ticker": "enum [PASS | FAIL]",
      "gate_3_sector": "enum [PASS | FAIL | ESCALATE]",
      "gate_4_conviction": "enum [PASS | FAIL]",
      "gate_5_cash": "enum [PASS | FAIL | ESCALATE]",
      "gate_6_thesis": "enum [PASS | FAIL]",
      "gate_7_ma_guidance": "enum [PASS | FAIL]",
      "gate_8_execution": "enum [PASS | FAIL]",
      "final_approval": "enum [APPROVED_FOR_EXECUTION | REJECTED | ESCALATED]"
    },

    "thesis_drivers": [
      {
        "name": "Driver Name",
        "narrative": "Detailed explanation...",
        "assumption_type": "enum [BINARY | DURABILITY | MACRO | EXECUTION]", 
        "validation_status": "enum [VALIDATED | UNVALIDATED]",
        "conviction_penalty_points": "integer (e.g. 10, 15)"
      }
    ],

    "conviction_calculation": {
      "starting_score": 100,
      "penalties_applied": [
        {"reason": "Binary Assumption", "points": 15},
        {"reason": "Execution Risk", "points": 10}
      ],
      "final_score": "integer",
      "conviction_tier": "enum [HIGH | MEDIUM | LOW]"
    },

    "technical_analysis": {
      "backtest_winner": {
        "strategy": "Name of best strategy",
        "performance": "vs Buy & Hold",
        "sharpe_ratio": "number",
        "key_insight": "string"
      }
    },

    "citations_check": {
      "status": "boolean (true = all cited)",
      "total_citations": "integer",
      "missing_citations": "[array of strings (field names)]"
    },
    
    "alpha_context": {
      "source": "Alpha-Picks-Analyst",
      "rank": "integer",
      "probability": "number"
    }
  }
}
```

### Field Definitions (v8.1 Updates)

**qa_gate_results:**
- MUST include status for all 8 gates.
- `final_approval` must derive logically from individual gates (e.g., if any FAIL, final is REJECTED).

**thesis_drivers.assumption_type:**
- **BINARY:** Outcome depends on a specific event (e.g., FDA approval). Penalty: 15 pts.
- **DURABILITY:** Outcome depends on trend continuing (e.g., margins hold). Penalty: 10 pts.
- **MACRO:** Outcome depends on external economy (e.g., rate cuts). Penalty: 20 pts.
- **EXECUTION:** Outcome depends on management action (e.g., merger integration). Penalty: 10 pts.

**citations_check:**
- `status`: True only if all financial claims in `thesis_drivers` and `technical_analysis` have inline citations (e.g., `[web:1]`).
- `missing_citations`: Debug list if status is False.

---

## MAOutput Schema – ALPHA COORDINATION ADDITION (v8.0.5)

### Structure: MAOutput.alpha_coordination (NEW)

**Purpose:** Capture Market-Analyst's validation of Alpha-Picks-Analyst candidate predictions against sector hard cap constraints. Ensures Stock-Analyst receives coordinated, non-contradictory guidance.

### Alpha Coordination Object

```json
{
  "alpha_coordination": {
    "alpha_picks_status": "enum [AVAILABLE | UNAVAILABLE | STALE | MISMATCHED]",
    
    "alpha_source": {
      "timestamp": "ISO_8601 (from AlphaPicksOutput)",
      "portfolio_reference": "ISO_8601 (portfolio state when Alpha ran)",
      "freshness_check": "boolean (is AlphaPicksOutput ≤4 hours old?)",
      "portfolio_match": "boolean (does AlphaPicksOutput portfolio match current PORTFOLIO.md?)"
    },
    
    "validation_results": {
      "total_candidates_checked": "integer",
      "candidates_allowed": "integer",
      "candidates_violating_cap": "integer",
      "sectors_with_violations": "[array of sector names]",
      "highest_ranked_violation": {
        "alpha_rank": "integer (e.g., 1)",
        "ticker": "string",
        "sector": "string",
        "issue": "string (reason for violation)"
      }
    },
    
    "validation_details": [
      {
        "alpha_rank": "integer (from AlphaPicksOutput)",
        "ticker": "string",
        "sector": "string",
        "alpha_probability": "number (0.0-1.0)",
        "validation_status": "enum [ALLOWED | CONSTRAINED | VIOLATES_CAP]",
        "sector_headroom": "number (0.0-1.0)",
        "sector_hard_cap": 0.35,
        "sector_current": "number (current allocation)",
        "note": "string (explanation)"
      }
    ],
    
    "coordinated_recommendations": [
      {
        "alpha_rank": "integer",
        "alpha_ticker": "string",
        "alpha_probability": "number",
        "alpha_sector": "string",
        "ma_status": "enum [ALLOWED | CONSTRAINED | VIOLATES_CAP]",
        "recommendation": "string (what to do)",
        "alternative_sector": "string (if constrained)",
        "alternative_alpha_rank": "integer (Alpha's rank for alternative sector)"
      }
    ],
    
    "escalations": [
      {
        "severity": "enum [INFO | WARNING | HIGH]",
        "trigger": "string",
        "message": "string",
        "affected_candidates": "[array of tickers]",
        "action_required": "boolean"
      }
    ],
    
    "integration_notes": "string (human-readable summary of coordination)",
    "ma_coordination_timestamp": "ISO_8601"
  }
}
```

### Field Definitions

**alpha_picks_status:**

| Status | Meaning | Action |
|--------|---------|--------|
| AVAILABLE | AlphaPicksOutput present, fresh, portfolio matches | Use coordinated recommendations |
| UNAVAILABLE | No AlphaPicksOutput provided | Skip alpha_coordination section, proceed with MA only |
| STALE | AlphaPicksOutput >4 hours old | Warn SA, use fundamental analysis primary |
| MISMATCHED | Portfolio state different at Alpha run time | Flag for review, alternative candidates may be invalid |

**validation_status (per candidate):**

| Status | Meaning | SA Action |
|--------|---------|-----------|
| ALLOWED | Candidate fits within sector hard cap | Use as-is from Alpha |
| CONSTRAINED | Candidate violates cap, but alternative available | Use alternative sector instead |
| VIOLATES_CAP | Candidate violates cap, no alternatives | Escalate, don't use this candidate |

**escalations.severity:**

| Severity | Escalates To | Action |
|----------|--------------|--------|
| INFO | None (informational only) | Log and continue |
| WARNING | Stock-Analyst (as context) | Use primary analysis, don't rely on Alpha |
| HIGH | Master-Architect | Hold, review constraint assumptions |

---

## AlphaPicksOutput Schema (Reference)

**Note:** AlphaPicksOutput is produced by Alpha-Picks-Analyst.md v8.0.0 (separate persona, not Market-Analyst).

**Structure provided here for schema alignment with v8 framework.**

### AlphaPicksOutput Object

```json
{
  "alpha_picks_output": {
    "timestamp": "ISO_8601",
    
    "data_sources": {
      "quant_screener_date": "YYYY-MM-DD",
      "portfolio_holdings_date": "YYYY-MM-DD",
      "analyst_revision_period": "enum [30d | 60d | 90d]"
    },
    
    "tier_0_screening": {
      "total_candidates": "integer (e.g., 20 from Quant Top 20)",
      "passed_tier_0": "integer (e.g., 9 viable after constraints)",
      "eliminated_count": "integer",
      "eliminated_reasons": {
        "already_in_portfolio": "[array of tickers]",
        "sector_saturated": "[array of tickers]",
        "price_below_minimum": "[array of tickers]",
        "market_cap_too_low": "[array of tickers]",
        "macro_break": "[array of tickers]",
        "analyst_coverage_insufficient": "[array of tickers]"
      }
    },
    
    "candidates": [
      {
        "rank": "integer (1, 2, 3, ...)",
        "ticker": "string",
        "probability_percent": "number (0-100)",
        
        "factor_analysis": {
          "growth_grade": "enum [A+, A, A-, B+, B, B-, C, D, F]",
          "profitability_grade": "enum [A+, A, A-, B+, B, B-, C, D, F]",
          "momentum_grade": "enum [A+, A, A-, B+, B, B-, C, D, F]",
          "valuation_grade": "enum [A+, A, A-, B+, B, B-, C, D, F]"
        },
        
        "quality_score": "number (0-100)",
        "tenure_days": "integer",
        "tenure_adjustment": "number (-30 to +10)",
        "final_score": "number (0-100)",
        
        "analyst_revisions": {
          "up_count": "integer",
          "down_count": "integer",
          "bullish_percent": "number (0-100)",
          "period": "enum [30d, 60d, 90d]"
        },
        
        "thesis": "string (investment rationale)",
        "risks": "[array of risk factors]",
        "sector": "string"
      }
    ],
    
    "sector_preference_ranking": [
      {
        "sector": "string",
        "rank": "integer (1 = highest preference)",
        "reason": "string (e.g., 'Fewer recent picks, diversification signal')",
        "recent_picks_count": "integer (last 60 days)"
      }
    ],
    
    "confidence_overall": "number (0.0-1.0, e.g., 0.45 for 45%)",
    "confidence_notes": "string (caveats, assumptions)",
    
    "framework_version": "string (e.g., 'v2.0')",
    "next_validation_date": "ISO_8601 (when this prediction will be validated)"
  }
}
```

### Field Definitions

**probability_percent:**
- 0-30%: Low probability (unlikely to be selected)
- 30-50%: Moderate probability (possible)
- 50-70%: High probability (likely)
- 70-100%: Very high probability (very likely)

**factor_analysis grades:**
- A+ = 5.0 (excellent)
- A = 4.5 (strong)
- B+ = 3.5 (acceptable)
- B = 3.0 (fair)
- D = 0.5 (failing)
- F = 0.0 (circuit breaker – automatic elimination)

**quality_score:**
- Calculated from weighted factor grades (Growth 25%, Profitability 15%, Momentum 25%, Valuation 10%, Reserved 25%)
- Minimum threshold: ≥65 (candidates below threshold pre-eliminated in Tier 0)
- Maximum: 100

**tenure_adjustment:**
- +10: Early emergence (75-100 days, sweet spot)
- +5: Peak timing (100-120 days, perfect sweet spot)
- -5: Late timing (120-150 days, past optimal)
- -20 to -30: Way too late (>150 days, excluded)

**analyst_revisions:**
- Measures analyst community consensus on forward performance
- 90%+ bullish: Primary selection signal (can override weak factors)
- 80-90% bullish: Strong supporting signal
- <80% bullish: Non-determinative

---

## VERSION HISTORY

**v8.0.3 (December 10, 2025):**
- **INTEGRATED:** Merged SAOutput v8.1 Enforcement Schema directly into file.
- **UPDATED:** SAOutput structure now explicitly requires `qa_gate_results`, `citations_check`, and `assumption_type`.
- **MAINTAINED:** Existing MAOutput.alpha_coordination and AlphaPicksOutput structures.
- Eliminates need for standalone `SAOutput-SCHEMA-v8.1.md`.

**v8.0.2 (December 9, 2025):**
- Added MAOutput.alpha_coordination schema (Market-Analyst v8.0.5 integration point)
- Added AlphaPicksOutput schema reference (for Alpha-Picks-Analyst.md v8.0.0)
- Defined validation rules for both output types
- Added integration points (MA → SA, Alpha → MA)
- Removed MAOutput.alpha_screening (superseded by separate AlphaPicksOutput)
- Added example data flow (December 15 selection scenario)
- Backward compatible (additions are optional)

**v8.0.1 (December 9, 2025):**
- Added alpha_screening section to MAOutput schema (superseded by v8.0.2)
- Included tier_0_results and tier_1_ranking structures
- [Archived – replaced by v8.0.2 with separate Alpha-Picks-Analyst persona]

**v8.0.0 (December 8, 2025):**
- Initial schema definition for SAOutput and MAOutput
- Core structures: macro_assessment, sector_attractiveness, sector_constraints

---

**End of AGENT-OUTPUT-SCHEMA.md v8.0.3**