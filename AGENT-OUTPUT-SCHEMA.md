# AGENT-OUTPUT-SCHEMA.md v8.0.2

**Version:** v8.0.2 - AlphaPicksOutput Schema & Coordination  
**Framework:** v8 - Tested & Compliant  
**Status:** Ready for upload  
**Last Updated:** December 9, 2025, 3:25 PM MST

---

## OVERVIEW

AGENT-OUTPUT-SCHEMA defines JSON structure for SAOutput (Stock-Analyst output) and MAOutput (Market-Analyst output). This update adds AlphaPicksOutput schema and Market-Analyst coordination mechanism for Patch 5 integration with separate Alpha-Picks-Analyst persona.

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

### Integration with Market-Analyst v8.0.5

**Market-Analyst STEP 4 produces alpha_coordination:**

1. **Receives:** AlphaPicksOutput from Alpha-Picks-Analyst
2. **Validates:** Each candidate against STEP 3 sector_constraints
3. **Produces:** alpha_coordination object with validation results
4. **Includes:** Coordinated recommendations for constrained candidates
5. **Escalates:** If top candidates violate hard caps (HIGH severity)

**Market-Analyst STEP 5 includes alpha_coordination in MAOutput:**

```json
{
  "ma_timestamp": "...",
  "macro_assessment": {...},
  "sector_attractiveness": [...],
  "sector_constraints": [...],
  "alpha_coordination": {...},  // NEW in v8.0.5
  "special_conditions": "..."
}
```

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

## Example: Complete Data Flow

### Scenario: December 15 Selection Day

**Market-Analyst v8.0.5 Receives:**

1. PORTFOLIO.md (current holdings, sector allocations)
2. AlphaPicksOutput from Alpha-Picks-Analyst:
   ```json
   {
     "candidates": [
       {"rank": 1, "ticker": "AU", "probability": 0.40, "sector": "Materials"},
       {"rank": 2, "ticker": "ALLY", "probability": 0.30, "sector": "Financials"}
     ]
   }
   ```

**Market-Analyst STEP 4 Validation:**

```
Checking AU (Materials):
  Current allocation: 30.7%
  Hard cap: 35%
  Available headroom: 4.3%
  
  Validation: ALLOWED (fits within 4.3% headroom)
  
Checking ALLY (Financials):
  Current allocation: 12%
  Hard cap: 35%
  Available headroom: 23%
  
  Validation: ALLOWED (fits within 23% headroom)
  
Result: No violations, both candidates allowed
```

**Market-Analyst produces MAOutput.alpha_coordination:**

```json
{
  "alpha_coordination": {
    "alpha_picks_status": "AVAILABLE",
    "validation_results": {
      "total_candidates_checked": 2,
      "candidates_allowed": 2,
      "candidates_violating_cap": 0
    },
    "coordinated_recommendations": [
      {
        "alpha_rank": 1,
        "alpha_ticker": "AU",
        "alpha_probability": 0.40,
        "ma_status": "ALLOWED",
        "recommendation": "Candidate AU (Materials) allowed – fits within sector headroom"
      },
      {
        "alpha_rank": 2,
        "alpha_ticker": "ALLY",
        "alpha_probability": 0.30,
        "ma_status": "ALLOWED",
        "recommendation": "Candidate ALLY (Financials) allowed – fits within sector headroom"
      }
    ],
    "escalations": [],
    "integration_notes": "All Alpha candidates validated. No constraint violations."
  }
}
```

**Stock-Analyst v8.0.7 Reads MAOutput:**

- Gets macro assessment (regime multiplier)
- Gets sector constraints (headroom available)
- Gets alpha_coordination (AU and ALLY both allowed)
- Proceeds with research on top candidates

---

## Validation Rules

### AlphaPicksOutput Validation

```
FOR each candidate in candidates:
  
  1. rank must be unique integers (1, 2, 3, ..., no gaps)
  2. ticker must be valid stock symbol
  3. probability_percent 0-100
  4. factor_analysis: all grades must be valid enums
  5. quality_score = function of factor grades (validate formula)
  6. final_score = quality + tenure adjustment
  7. thesis and risks must be non-empty

GLOBAL:
  - sum(candidates) count must match tier_0_screening.passed_tier_0
  - sector_preference_ranking must only include passing Tier 0 sectors
  - next_validation_date must be after timestamp
```

### MAOutput.alpha_coordination Validation

```
FOR each validation_detail:
  
  1. ticker must be valid stock symbol
  2. alpha_rank must match a candidate in AlphaPicksOutput
  3. sector must be valid sector name
  4. validation_status ∈ [ALLOWED, CONSTRAINED, VIOLATES_CAP]
  5. sector_headroom must match sector_constraints from STEP 3
  6. If ALLOWED: sector_headroom >= estimated position size
  7. If VIOLATES_CAP: sector_headroom <= 0 or insufficient

coordinated_recommendations:
  - For each CONSTRAINED candidate: must include alternative_sector
  - Alternative sector must be OPEN (headroom > 0)
  - Alternative rank must reference Alpha's sector_preference_ranking

escalations:
  - severity must be enum [INFO, WARNING, HIGH]
  - If severity HIGH: action_required must be true
```

---

## Integration Points

### Market-Analyst → Stock-Analyst

**Stock-Analyst TURN 1 reads:**

```json
{
  "macro_assessment": {...},
  "sector_attractiveness": [...],
  "sector_constraints": [...],
  "alpha_coordination": {
    "alpha_picks_status": "AVAILABLE | UNAVAILABLE | STALE",
    "coordinated_recommendations": [...],
    "escalations": [...]
  }
}
```

**Stock-Analyst uses alpha_coordination:**

```
IF alpha_picks_status = "AVAILABLE" AND no escalations:
  Use coordinated_recommendations as context for stock selection
  Priority: Alpha top candidates (if allowed)
  
ELIF escalations.severity = "WARNING":
  Alert: "Alpha data may be stale"
  Use fundamental analysis primary, Alpha secondary
  
ELIF escalations.severity = "HIGH":
  Block: "Alpha candidates violate sector constraints"
  Escalate to Master-Architect
  Do not proceed with constrained candidates
```

### Alpha-Picks-Analyst → Market-Analyst

**Market-Analyst STEP 4 reads AlphaPicksOutput:**

```
Input: {
  "candidates": [AU 40%, ALLY 30%, ...],
  "sector_preference_ranking": [Equities 1, Healthcare 2, ...]
}

Process: Validate each candidate against sector constraints
Output: MAOutput.alpha_coordination with validation results

If violations: Mark as CONSTRAINED, suggest alternatives from sector_preference_ranking
```

---

## Backward Compatibility

**v8.0.1 to v8.0.2 changes:**

- **Added:** MAOutput.alpha_coordination (optional, if AlphaPicksOutput available)
- **Added:** AlphaPicksOutput schema reference (for Alpha-Picks-Analyst.md v8.0.0)
- **Removed:** MAOutput.alpha_screening (was intermediate spec in v8.0.1, superseded)
- **Kept:** All existing MAOutput fields (macro_assessment, sector_attractiveness, sector_constraints)

**Consumer compatibility:**

- Existing SAOutput and QAOutput unchanged
- MAOutput additions are optional (system works without alpha_coordination)
- Stock-Analyst can safely ignore alpha_coordination if not present
- Fallback: If AlphaPicksOutput unavailable, MA produces MAOutput without coordination section

---

## VERSION HISTORY

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

**End of AGENT-OUTPUT-SCHEMA.md v8.0.2**

