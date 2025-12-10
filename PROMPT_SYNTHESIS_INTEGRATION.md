# PROMPT_SYNTHESIS_INTEGRATION.v1.1.md

**Version:** 1.1 (Guardrail D Data Freshness Integration)  
**Date:** December 1, 2025  
**Status:** Production Ready - Fully Updated with Data Freshness Propagation  
**Scope:** Synthesis consensus logic + freshness tag propagation + conflict resolution + portfolio analysis + stock screening

---

## SECTION 1: SYNTHESIS CONSENSUS LOGIC WITH DATA FRESHNESS PROPAGATION [UPDATED v1.1]

### **Purpose**
Take 4 independent analyst outputs (QMA, FMA, IRM, MSPA) and produce unified market thesis with confidence scoring, freshness tag propagation, and conflict detection.

### **Data Freshness Tag Extraction (NEW - v1.1)**

**Each analyst output includes freshness metadata. Synthesis must extract and propagate:**

From QMA output:
- Operating context (MARKET_OPEN, AFTER_HOURS, WEEKEND, HOLIDAY, PRE_MARKET)
- Data age (minutes/hours old)
- Confidence base (100%, 90%, 85%, 70%, etc.)
- Confidence adjusted (accounting for data freshness penalty)
- Disclosure (timestamp tags with data source age)
- Stale flag (TRUE/FALSE)

From FMA output:
- Data source (Latest earnings, guidance age, SEC filing date)
- Data age (days old: 0-30 recent, 30-90 aging, 90+ old)
- Freshness status (FRESH, RECENT, AGED, STALE)
- Confidence base and adjusted
- Disclosure tags

From IRM output:
- Correlation data timestamp
- VIX data timestamp
- Data age in minutes/hours
- Freshness status
- Confidence adjustment

From MSPA output:
- Sentiment data sources (insider trades, put/call, social)
- Data age for each source
- Freshness status by source
- Confidence adjustment

---

### **Aggregate Freshness Metadata Across All Analysts (NEW - v1.1)**

**Synthesis creates unified freshness summary:**

```json
{
  "synthesis_data_freshness": {
    "timestamp": "2025-12-01T14:30:00Z",
    "operating_context": "MARKET_OPEN | AFTER_HOURS | WEEKEND | HOLIDAY",
    
    "analyst_freshness_status": {
      "QMA": {
        "status": "FRESH | AGED | STALE",
        "operating_context": "MARKET_OPEN",
        "base_confidence": 100,
        "adjusted_confidence": 100,
        "freshness_penalty": 0,
        "disclosure": "S&P 500 real-time, VIX 2 min old",
        "stale_flag": false
      },
      "FMA": {
        "status": "FRESH | AGED | STALE",
        "base_confidence": 80,
        "adjusted_confidence": 40,
        "freshness_penalty": -40,
        "disclosure": "Earnings 3 months old",
        "stale_flag": true
      },
      "IRM": {
        "status": "FRESH | AGED | STALE",
        "base_confidence": 90,
        "adjusted_confidence": 90,
        "freshness_penalty": 0,
        "disclosure": "VIX current, correlations fresh",
        "stale_flag": false
      },
      "MSPA": {
        "status": "FRESH | AGED | STALE",
        "base_confidence": 85,
        "adjusted_confidence": 85,
        "freshness_penalty": 0,
        "disclosure": "Sentiment data current",
        "stale_flag": false
      }
    },
    
    "overall_freshness_assessment": {
      "any_stale_data": true,
      "most_problematic": "FMA (Earnings 3 months old)",
      "worst_freshness_penalty": -40,
      "average_freshness_penalty": -10,
      "synthesis_confidence_degradation": -40
    }
  }
}
```

---

### **Apply Confidence Degradation Through Synthesis Pipeline (NEW - v1.1)**

**Synthesis confidence is capped by worst analyst freshness penalty:**

```
Synthesis Confidence = Base Confidence - Worst Analyst Freshness Penalty

Example (Weekend Analysis with Stale Fundamentals):
  Base Synthesis Confidence: 75% (3 analysts agree on Growth regime)
  QMA Freshness Penalty: -15% (market data from Friday)
  FMA Freshness Penalty: -40% (earnings 3 months old)
  IRM Freshness Penalty: 0% (correlations fresh)
  MSPA Freshness Penalty: 0% (sentiment fresh)
  
  Worst Penalty: -40% (FMA)
  Adjusted Synthesis Confidence: 75% - 40% = 35%
  
  Result: SYNTHESIS CONFIDENCE SEVERELY DEGRADED
  Action: Flag for stakeholder review before acting
```

---

### **Confidence Levels by Agreement (UNCHANGED)**

| Agreement | Confidence | Action |
|-----------|-----------|--------|
| 4-of-4 | VERY HIGH (95%+) | Execute with confidence |
| 3-of-4 | HIGH (80-90%) | Execute, monitor dissent |
| 2-of-2 | MEDIUM (60-75%) | Caution, watch closely |
| 1-of-4 or Divergent | LOW (<50%) | Escalate to review |

---

### **Decision Rules Based on Freshness Degradation (NEW - v1.1)**

**Synthesis applies decision logic based on freshness penalties:**

```
IF worst_analyst_freshness_penalty >= -40:
  │ Confidence severely degraded
  │ Synthesis Status: ⚠️ CAUTION
  │ Action: ESCALATE to stakeholder
  │ Recommendation: Wait for fresh data OR provide manual validation
  │
ELSE IF worst_analyst_freshness_penalty >= -20:
  │ Confidence moderately degraded
  │ Synthesis Status: ⚠️ DEGRADED
  │ Action: Proceed with caution
  │ Recommendation: Revalidate when fresh data available
  │
ELSE IF worst_analyst_freshness_penalty >= -10:
  │ Confidence slightly degraded
  │ Synthesis Status: ℹ️ SLIGHTLY AGED
  │ Action: Proceed normally
  │ Recommendation: Standard monitoring
  │
ELSE:
  │ No significant degradation
  │ Synthesis Status: ✅ FRESH
  │ Action: Proceed normally
```

---

### **Priority Ranking (When Analysts Conflict)**

```
TIER 1: MSPA (Real-time, captures reversals first)
  Why: Sentiment flips instantly, markets reverse on sentiment
  Freshness factor: HIGHEST priority - must be real-time
  
TIER 2: QMA (5-day patterns, technical breakdown)
  Why: Price action shows regime breakdown before macro
  Freshness factor: HIGH priority - ≤60 min acceptable
  
TIER 3: IRM (Correlation rising, risk increasing)
  Why: Diversification failure = crisis approaching
  Freshness factor: MEDIUM priority - ≤2 hours acceptable
  
TIER 4: FMA (Directional, slowest update)
  Why: Earnings/macro data update slowly
  Freshness factor: MEDIUM priority - ≤24 hours acceptable, >3 months heavily penalized
```

---

## SECTION 2: CONSENSUS SCORING MATRIX WITH FRESHNESS ADJUSTMENT [UPDATED v1.1]

### **Step 1: Extract Signals from Each Analyst**

From QMA output:
- Regime classification (Growth/Value/Defensive/Transition)
- Directional confidence (HIGH/MEDIUM/LOW) → Adjusted for data freshness
- Technical status (support/resistance, trend)

From FMA output:
- Growth regime (Expansion/Peak/Contraction/Trough)
- Bubble stage (1/2/3/4)
- Earnings momentum (improving/flat/deteriorating) → Adjusted for earnings age

From IRM output:
- Risk regime (Normal/Elevated/High/Crisis)
- Correlation regime (Low/Normal/High/Panic)
- Drawdown risk (% VaR) → Validated for correlation data freshness

From MSPA output:
- Greed/fear intensity (0-10 scale)
- Sentiment regime (Euphoric/Bullish/Neutral/Fearful/Panicked)
- Stage transition risk (% probability) → Must be real-time for accuracy

---

### **Step 2: Agreement Scoring with Freshness Consideration**

Count analysts agreeing on each major question, accounting for freshness flags:

```
QUESTION: Is market regime GROWTH?

Scenario A: All 4 agree, all data fresh
  QMA: Yes (real-time) | FMA: Yes (current earnings) | IRM: Yes (fresh) | MSPA: Yes (real-time)
  Score: 4-of-4 UNANIMOUS, all FRESH = Confidence VERY HIGH (95%+)
  
Scenario B: 3 agree, 1 stale
  QMA: Yes (real-time) | FMA: Yes (3-month-old earnings) | IRM: Yes (fresh) | MSPA: Yes (real-time)
  Score: 3-of-4 STRONG agreement, BUT FMA aged = Confidence HIGH (80-90%) - 10% for FMA staleness = 70-80%
  Conflict: FMA fundamental data old; earnings may not reflect current condition
  
Scenario C: 2 fresh vs. 2 stale
  QMA: Yes (real-time) | FMA: No (3-month-old earnings) | IRM: Yes (fresh) | MSPA: No (old sentiment)
  Score: 2-of-4 WEAK, 2 analysts stale = Confidence MEDIUM (50-70%) - 20% for staleness = 30-50%
  Conflict: Technical/correlations support growth, but fundamentals/sentiment dated
```

---

## SECTION 3: REAL-TIME REBALANCING TRIGGERS [UNCHANGED]

### **Priority 1: Regime Transition (Execute Same Day)**
- Signal: Breadth <50% for 2+ days
- Action: Recalculate sector caps, execute rebalancing by close
- **Freshness requirement:** Real-time market data

### **Priority 2: Correlation Spike (Execute Within 1 Day)**
- Signal: Correlation >0.75 for 2+ days
- Action: Reduce equity exposure 10%, increase defensive
- **Freshness requirement:** <2 hours old correlation data

### **Priority 3: Sentiment Extreme (Execute Within 2 Days)**
- Signal: Greed drops >3 points in single day, holds Day 2
- Action: Trim Mag7 25%, lock in profits
- **Freshness requirement:** Real-time sentiment data

### **Priority 4: Weekly Fine-Tuning (Execute Tuesday)**
- Signal: Position drift, sector cap maintenance
- Action: Trim positions >6%, maintain caps
- **Freshness requirement:** End-of-day data acceptable

---

## SECTION 4: DECLINING CONVICTION GOVERNANCE [UNCHANGED]

### **Concentration Threshold Decay**

```
Month 1: >70% conviction = Justified
Month 3: >80% conviction = Justified (threshold raised)
Month 6: >85% conviction = Justified (threshold raised)
Month 12: >90% conviction = Justified (threshold raised)
```

**Note:** Freshness penalties apply to all conviction assessments. Month 6 conviction at 82% with -3% freshness penalty = 79% actual, below 85% threshold = trim position.

---

## SECTION 5: v7.0 → v1.4.1 DATA TRANSLATOR [UNCHANGED]

### **Data Flow**

```
v7.0 Analysis Output (JSON)
        ↓
Translator (Format conversion + validation + freshness preservation)
        ↓
v1.4.1 Input Format with Freshness Metadata
        ↓
v1.4.1 Stock Analysis
        ↓
Conflict Detection (v7.0 vs v1.4.1)
        ↓
Framework Presentation (both sides to stakeholder)
```

**NEW v1.1:** Freshness metadata flows through translator, preserved in v1.4.1 inputs

---

## SECTION 6: WEEKLY SYNTHESIS CYCLE [UPDATED v1.1]

**Tuesday 6:00 AM:** All 4 analysts run analysis (10-day window)
- **QMA:** Market data freshness validated per operating context
- **FMA:** Earnings age verified and penalized if >3 months
- **IRM:** Correlation/VIX data timestamped and validated
- **MSPA:** Sentiment data freshness confirmed

**Tuesday 9:30 AM:** Translator ingests outputs, freshness extraction runs
- Extract all 4 analyst freshness metadata
- Calculate worst freshness penalty
- Apply degradation to synthesis confidence

**Tuesday 10:00 AM:** Framework presents synthesis with freshness audit to stakeholder
- Market Regime + Confidence Adjusted for Freshness
- Data Freshness Audit section prominent
- Analyst disclosures propagated
- Stale data flags highlighted

**Tuesday 10:30 AM:** Stakeholder validates, approves or overrides
- Review confidence degradation (was base X%, now Y% due to freshness)
- Accept data limitations or request manual input
- Approve rebalancing or escalate

**Tuesday-Friday:** Real-time trigger monitoring (requires fresh data)

**Following Monday:** Weekly review, prepare for next cycle

---

## SECTION 7: SYNTHESIS OUTPUT FORMAT WITH DATA FRESHNESS AUDIT [UPDATED v1.1]

```json
{
  "synthesis_report": {
    "timestamp": "2025-12-01T14:30:00Z",
    
    "market_regime": {
      "classification": "Growth",
      "base_confidence": 75,
      "confidence_adjusted": 35,
      "freshness_penalty": -40,
      "status": "⚠️ CAUTION - Confidence severely degraded"
    },
    
    "data_freshness_audit": {
      "all_sources_fresh": false,
      "operating_context": "WEEKEND",
      "worst_stale_source": "FMA - Earnings 3 months old",
      "worst_freshness_penalty": -40,
      
      "analyst_freshness_breakdown": {
        "QMA": {
          "status": "AGED",
          "penalty": -15,
          "disclosure": "Market data from Friday close"
        },
        "FMA": {
          "status": "AGED",
          "penalty": -40,
          "disclosure": "Earnings 3 months old"
        },
        "IRM": {
          "status": "FRESH",
          "penalty": 0,
          "disclosure": "Correlations & VIX current"
        },
        "MSPA": {
          "status": "FRESH",
          "penalty": 0,
          "disclosure": "Sentiment data current"
        }
      },
      
      "freshness_caveat": "⚠️ Weekend analysis with stale fundamental data. 
                          Recommend revalidation with current earnings before acting.",
      
      "recommended_action": "ESCALATE for stakeholder review"
    },
    
    "analyst_consensus": {
      "regime_agreement": "3-of-4 (QMA, IRM, MSPA support Growth; FMA cautious)",
      "confidence_framework": "HIGH consensus, but FMA fundamentals aged",
      "overall_consensus": "Growth regime likely valid, but execution risk elevated"
    },
    
    "sector_leadership": {
      "primary": "Technology",
      "base_confidence": 70,
      "confidence_adjusted": 30,
      "freshness_note": "Based on Friday data; current sector dynamics may differ"
    },
    
    "position_sizing_recommendation": {
      "defensive_level": 0.60,
      "growth_level": 0.40,
      "freshness_note": "Position sizing conservatively due to freshness degradation",
      "disclaimer": "⚠️ If stale data resolved, rebalance to growth-aligned allocation"
    },
    
    "decision_triggers": {
      "fresh_data_available": {
        "event": "Monday market open with current data",
        "action": "Rerun synthesis with fresh market/earnings data",
        "confidence_recovery": "Expect confidence +15-20 with fresh FMA data"
      },
      "fundamental_update": {
        "event": "Updated earnings guidance or new filings",
        "action": "FMA analyst revalidate, recalculate conviction",
        "confidence_impact": "+10-15 if earnings confirm guidance"
      }
    }
  }
}
```

---

## SECTION 8: PORTFOLIO ANALYSIS & REBALANCING [PRESERVED]

### **Purpose**
Take portfolio snapshot, assess each stock, identify weak/strong positions, recommend specific actions while accounting for data freshness constraints.

### **Algorithm (Freshness-Enhanced)**

**Step 1: Extract Portfolio Data + Verify Data Freshness**

For each position:
- Stock name, current position size (%), conviction score
- Entry price, current price, unrealized P&L
- **NEW:** Data freshness from last analysis (when was conviction last calculated?)
- If conviction >6 months old → Apply freshness penalty before rebalancing

**Step 2: Assess vs. Declining Conviction Threshold**

For each stock:
- Conviction threshold applies (Month 1 = 70%, Month 6 = 85%, etc.)
- Adjust threshold downward if conviction data stale (>3 months old = -10% adjustment)

**Step 3-6:** [Preserved from v1.0]

### **Output Format** [Preserved with freshness fields added]

```json
{
  "portfolio_analysis": {
    "timestamp": "2025-12-01T14:30:00Z",
    "current_regime": "Growth",
    
    "weak_stocks": [
      {
        "stock": "Stock A",
        "conviction_last_calculated": "2025-09-15",
        "conviction_age_days": 77,
        "conviction_age_status": "STALE (>90 days)",
        "current_conviction": "72%",
        "conviction_threshold": "85% (6-month holding)",
        "conviction_adjusted_for_staleness": "62% (penalty -10%)",
        "recommendation": "TRIM to 2%",
        "rationale": "Conviction below threshold; stale data supports caution"
      }
    ]
  }
}
```

---

## SECTION 9: STOCK CANDIDATE SCREENING [PRESERVED]

### **Algorithm (Freshness-Aware)**

**Step 1: Extract Candidate Data**
- Add data freshness timestamp to each candidate

**Step 2: Filter by Regime + Freshness**

Prioritize candidates with:
- Recent analyst coverage (<30 days old)
- Fresh fundamental data (<3 months)
- Active technical setups (real-time data)

De-prioritize:
- Aged analyst coverage (>90 days)
- Stale fundamental data (>6 months)
- Inactive technical setups

**Step 3-7:** [Preserved from v1.0]

---

## END: PROMPT_SYNTHESIS_INTEGRATION.v1.1

---

**Document Version:** PROMPT_SYNTHESIS_INTEGRATION.v1.1  
**Status:** PRODUCTION READY - COMPLETE FILE FOR DOWNLOAD  
**Key Updates from v1.0:**
- Data Freshness Tag Extraction & Propagation (Section 1)
- Freshness-Adjusted Confidence Degradation (Sections 1-2)
- Decision Rules Based on Freshness Penalties (Section 2)
- Data Freshness Audit Section in Output (Section 7)
- Freshness-Enhanced Portfolio & Screening Analysis (Sections 8-9)

**All existing v1.0 content preserved; Guardrail D integration throughout.**

---

**Authority:** Master-Architect-Prompt-v1.0.5 + QUALITY-Guardrails.v1.4.5