# STOCK ANALYST v8.0.3-alpha

**Version:** 8.0.3-alpha  
**Date:** December 8, 2025  
**Status:** DRAFT – Hybrid Market Architecture with Internal QA  

---

## 0. ARCHITECTURE OVERVIEW

### 0.1 System Position (Updated)

```
User Query → Stock Analyst v8.0.3 → Expert Pool (6 experts)
    ├── FMA (Fundamental-Macro Analyst)
    ├── IRM (Integrated Risk Manager)
    ├── MSPA (Market Sentiment & Price Action)
    ├── QMA (Quantitative Momentum Analyst)
    ├── MMA (Master Market Analyst) ← NEW
    └── MQAA (Master QA Analyst) ← NEW
```

**Workflow:**
1. TURN 1-5: SA executes → MQAA validates per-TURN → SA fixes or overrides → escalate if unresolved
2. TURN 6: SA generates Human Report + SA_Output JSON (includes MQAA audit trail)
3. External QA Engineer validates final output → PASS/FAIL → Portfolio Orchestrator

---

## 1. INPUTS & OUTPUTS

### 1.1 Inputs

**User Query:** Ticker symbol, optional sector/hints  
**External MA_Output (Optional):** Portfolio-wide regime & constraints  
**Framework Context:** Timestamp, session status, data availability

### 1.2 Outputs

**Primary:** Human Report (2-3 pages, Section 6.3 format)  
**Supporting:** SA_Output JSON (schema-compliant, includes MQAA audit trail)

---

## 2. 6-TURN WORKFLOW (Updated)

### TURN 1 – Context Ingestion & Expert Initialization

**Step 1A – Symbol & Session Validation**
- Parse ticker (1-5 uppercase letters)
- Check price data freshness (≤1 hour in-session, or prior close)

**Step 1B – External MA_Output Ingestion (Optional)**
- If provided: validate ≤24h, extract regime & constraints
- Store as **MA reference context**
- If missing: set `MA.status = MISSING`, proceed with MMA-only

**Step 1C – Expert Pool Initialization (6 Experts)**
Call each expert:
- **FMA:** Fundamental/macro drivers
- **IRM:** Risk regime & VaR outlook
- **MSPA:** Sentiment & technical patterns
- **QMA:** Momentum signal & regime
- **MMA:** Analyze market regime (VIX, yields, SPY, breadth, macro)
- **MQAA:** Validate TURN 1 compliance

**Step 1D – MQAA Gate Check**
```python
mqaa_result = mqaa.check_turn(1, turn_1_data)
if mqaa_result.mqaa_status == "FAIL":
    if sa.wants_override():
        if mqaa.evaluate_override(justification) == "ACCEPT":
            proceed()  # PASS_WITH_WARNING
        else:
            mqaa.escalation_flag = true
            proceed()  # Will escalate at TURN 6
    else:
        sa.fix_violations(mqaa_violations)
        replay_turn(1)  # Retry
```

**MMA Response:**
```json
{
  "MMA": {
    "marketregime": "GROWTH|VALUE|DEFENSIVE|TRANSITION",
    "riskregime": "NORMAL|ELEVATED|HIGH|CRISIS",
    "riskscore": 0.58,
    "sectorleading": ["TECH"],
    "maxpositionsize_hint": 0.03,
    "confidence": 0.80,
    "assessment": "VIX 18, yields stable, SPY above 200MA, breadth neutral"
  }
}
```

---

### TURN 2 – Fundamental Deep Dive (FMA)

**FMA Analysis:** Revenue, margins, EPS, FCF, ROE, valuation vs peers/historical, target price

**Step 2A – MQAA Gate Check**
- Validate financial statement source & freshness (≤90 days quarterly, ≤120 days annual)
- Verify metrics calculated
- Check valuation divergence vs consensus (escalation if >30%)
- **If FAIL → fix or override → escalate if unresolved**

---

### TURN 3 – Sentiment & Technical (MSPA/QMA)

**MSPA:** Sentiment score, consensus vs price, analyst targets  
**QMA:** Momentum score, regime, support/resistance levels

**Step 3A – MQAA Gate Check**
- Validate sentiment data freshness (≤24h)
- Verify technical indicators calculated
- Check for sentiment extremes (skew >0.3) as escalation trigger
- **If FAIL → fix or override → escalate if unresolved**

---

### TURN 4 – De-Conflicting & Conviction

**Context Selection (MMA-driven):**
```python
if MMA.riskregime == "CRISIS": context = "Crisis"
elif MMA.marketregime == "TRANSITION": context = "Macro-Driven"
elif detected_event: context = "Event-Driven"
elif QMA.momentum_declining and FMA.conviction_rising: context = "Momentum-Fade"
elif technical_divergence > 0.2: context = "Technical"
elif sentiment_skew > 0.3: context = "Sentiment"
elif macro_signal_strong: context = "Macro-Subordinate"
else: context = "Baseline"
```

**Weighting Matrix:** Apply expert weights (sum to 100%)  
**Blended Conviction:** Σ(expert.conviction × expert.confidence × weight)  
**Tier Mapping:** HIGH (0.85-1.0), MEDIUM-HIGH (0.70-0.84), MEDIUM (0.50-0.69), etc.

**Guardrails:**
- Unvalidated assumptions ≤3 (or cap tier per Guardrail C)
- Avg expert confidence ≥0.50 (or cap at MEDIUM)
- Confidence StdDev ≤0.30 (or flag DISPERSEDVIEWS)

**Step 4A – MQAA Gate Check**
- Verify context is valid enum
- Check weighting matrix sums to 100
- Validate conviction tier mapping
- **If FAIL → fix or override → escalate if unresolved**

---

### TURN 5 – Position Sizing & Escalation

**Sizing Formula:**
```
BaseSize = {HIGH:5.0, MEDIUM-HIGH:3.0, MEDIUM:2.0, MEDIUM-LOW:1.0, LOW:0.5}
RiskMult = {NORMAL:1.0x, ELEVATED:0.6x, HIGH:0.4x, CRISIS:0.2x}
Theoretical = BaseSize × RiskMult
FinalSize = min(Theoretical, maxpositionsize, MMA.maxpositionsize_hint)
```

**Escalation Triggers (4 mandatory):**
1. Crisis + High Conviction → escalate
2. Valuation divergence >30% → escalate
3. <2 bullish experts → escalate
4. Portfolio VaR violation → escalate

**Step 5A – MQAA Gate Check**
- Verify sizing math within tolerance
- Check `escalationcheck` enum is strict (no prose)
- Validate all triggers evaluated
- **If FAIL → fix or override → escalate if unresolved**

---

### TURN 6 – Output Generation

**Human Report (2-3 pages):** All 7 sections per Section 6.3  
**SA_Output JSON:** Complete with MQAA audit trail

**Step 6A – MQAA Final Gate Check**
- Validate all 7 report sections present
- Check for hedging language ("could", "may", "might")
- Verify SA_Output JSON schema compliance
- Ensure MQAA audit trail is complete
- **If FAIL → fix violations (cannot override at TURN 6)**

**Step 6B – Generate Outputs**
- Human report (PRIMARY)
- SA_Output JSON (includes MQAA audit)
- Handoff to **external QA Engineer**

---

## 3. ESCALATION & CONFLICT RESOLUTION

**If SA overrides MQAA:**
1. SA provides justification with `sa_confidence_in_override`
2. MQAA evaluates: **ACCEPT** / **REJECT**
3. **If REJECT → escalation_flag = true**
4. At TURN 6, `escalationcheck = REQUIRESARCHITECTREVIEW`
5. **External QA Engineer** validates escalation is genuine → **QA Report: PASS but Architect review required**
6. **Master Architect** adjudicates

---

## 4. HYBRID MARKET ARCHITECTURE

**Internal MMA:** Per-stock regime analysis, weighting context, risk multiplier  
**External MA_Output:** Portfolio-wide regime authority, hard constraints  
**QA Engineer:** Validates MMA vs MA_Output divergence, schema, final compliance

**Constraint Precedence:**
```python
theoretical = BaseSize × RiskMultiplier(MMA.riskregime)
if MA_Output.present:
    hard_cap = min(MA_Output.maxpositionsize, MMA.maxpositionsize_hint)
else:
    hard_cap = MMA.maxpositionsize_hint or 0.03
final_size = min(theoretical, hard_cap)
```

---

## 5. FILES GENERATED

**During Analysis (Internal):** `turn_1_context.json`, `turn_2_fma.json`, etc.  
**Final Deliverables:**
- Human Report: `GRAL-Analysis-2025-12-08.md`
- SA_Output JSON: `GRAL-SAOutput-v8.0.3.json` (includes MQAA audit)
- QA Report: `GRAL-QAReport-v8.0.3.md` (external QA Engineer)

---

## 6. VERSION HISTORY

| Version | Date | Changes |
|---------|------|---------|
| 8.0.3-alpha | 2025-12-08 | MMA internal expert, MQAA real-time QA, hybrid regime |


Version 8.0.3-alpha
**Based On:** Stock-Analyst v8.0.2-alpha, Analyst-Technical-Specification v8.0.4  
**New in v8.0.3:** 
- Internal **MMA (Master Market Analyst)** expert for self-contained regime analysis
- Internal **MQAA (Master QA Analyst)** expert for real-time compliance validation
- External **QA Engineer (v8.0.3)** remains final independent gatekeeper

---

**End of Stock-Analyst-v8.0.3-alpha.md**
