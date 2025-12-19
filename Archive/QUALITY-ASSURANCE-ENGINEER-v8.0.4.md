# QUALITY ASSURANCE ENGINEER v8.0.4
## Portfolio Constraint Validation Specification

**Version:** 8.0.4  
**Date Created:** December 8, 2025  
**Status:** TIER 4.1 - Portfolio Integration for QA  
**Previous Version:** 8.0.3-alpha (pre-portfolio integration)  
**Integration Point:** Stock-Analyst v8.0.5 + Market-Analyst v8.0.1  

---

## EXECUTIVE SUMMARY

Quality Assurance Engineer v8.0.4 extends the existing QA framework to validate all portfolio constraints added in TIER 3. The QA Engineer acts as the independent gatekeeper ensuring:

1. **Portfolio constraints enforced** (6% position cap, 35% sector cap, 15% cash buffer)
2. **Position sizing formula correct** (all 8 multipliers applied)
3. **Escalation triggers proper** (6 triggers checked before execution)
4. **Data freshness validated** (PORTFOLIO.md ≤5 trading days)
5. **Account placement feasible** (cash available in target account)

QA Engineer operates as a **pre-execution gate**, blocking any recommendation that violates TIER 3 portfolio constraints before it reaches Portfolio-Orchestrator.

---

## CORE RESPONSIBILITIES (v8.0.4 - NEW)

**Previous Responsibilities (v8.0.3):**
- ✅ SAOutput schema validation
- ✅ Data freshness on stock-specific inputs
- ✅ Conviction tier basis verification
- ✅ Sizing constraint compliance (6% position cap)

**NEW Responsibilities (v8.0.4):**
- ✅ **Portfolio constraint validation against PORTFOLIO.md**
- ✅ **Sector cap enforcement (35% portfolio maximum)**
- ✅ **Sector headroom verification** (position won't breach sector cap)
- ✅ **Position headroom verification** (sector has room for new position)
- ✅ **Account cash availability** (target account has sufficient cash)
- ✅ **Deployment capacity validation** (position fits in portfolio cash)
- ✅ **Escalation trigger checking** (all 6 triggers evaluated)
- ✅ **Risk-adjusted buffer validation** (cash buffer ≥ regime-adjusted minimum)
- ✅ **8-step formula verification** (all multipliers applied correctly)
- ✅ **Cross-validation with MAOutput** (position sizing matches constraints)

---

## PRE-FLIGHT QUALITY GATES (v8.0.4)

### Gate 1: Portfolio Data Freshness (NEW)

**Trigger:** QA receives SAOutput from Stock-Analyst TURN 5

**Check 1A: PORTFOLIO.md Timestamp Validation**
```
IF SAOutput.portfolio_context missing THEN
  GATE_FAIL: "Portfolio context not provided by Stock-Analyst TURN 1"
  ACTION: Return to Stock-Analyst for TURN 1 redo
  
IF SAOutput.portfolio_timestamp > 5 trading days old THEN
  GATE_FAIL: "Portfolio data stale (>5 trading days)"
  ACTION: Escalate to Master-Architect
  REASON: Cannot validate against outdated portfolio state
  
IF SAOutput.portfolio_freshness = "ESCALATED" THEN
  GATE_FAIL: "Portfolio freshness unconfirmed"
  ACTION: Escalate to Master-Architect
  REASON: Stock-Analyst flagged portfolio as unconfirmed
ELSE
  GATE_PASS: Portfolio data current and reliable
```

**Check 1B: PORTFOLIO.md Structure Validation**
```
REQUIRED fields in SAOutput.portfolio_context:
  - total_value: Float (e.g., 720000)
  - current_cash_percent: Float (e.g., 0.569)
  - target_cash_percent: Float (e.g., 0.15)
  - excess_cash_dollars: Float (e.g., 302000)
  - sector_allocations: Array with [sector, current%, target%, cap%, headroom%]
  - current_positions: Array with [ticker, %_of_portfolio, account]
  
IF any required field MISSING THEN
  GATE_FAIL: "Incomplete portfolio context"
  ACTION: Return to Stock-Analyst for data re-extraction
ELSE
  GATE_PASS: Portfolio context complete
```

---

### Gate 2: Position Sizing Formula Verification (UPDATED)

**Trigger:** SAOutput received from Stock-Analyst TURN 5 Step 5H

**Check 2A: All 8 Multiplier Steps Present**
```
REQUIRED in SAOutput.position_sizing_detail:
  1. conviction_base_size: Float (5.0 → 0.5)
  2. risk_regime_multiplier: Float (1.0x → 0.2x)
  3. sector_fill_multiplier: Float (0.3x → 1.0x) [NEW in v8.0.5]
  4. position_fill_multiplier: Float (0.2x → 1.0x) [NEW in v8.0.5]
  5. deployment_phase_multiplier: Float (0.7x → 1.5x) [NEW in v8.0.5]
  6. account_cash_multiplier: Float (0.5x → 1.0x) [NEW in v8.0.5]
  7. final_size_before_cap: Float
  8. final_size_after_cap: Float (≤ 0.06)
  
IF any multiplier step MISSING THEN
  GATE_FAIL: "Incomplete position sizing formula"
  ACTION: Return to Stock-Analyst TURN 5 for re-calculation
  DETAIL: Show which step(s) missing
ELSE
  GATE_PASS: All steps present, proceed to formula verification
```

**Check 2B: Formula Calculation Accuracy**
```
CALCULATE: Expected_Final_Size = 
  conviction_base × risk_multiplier × sector_multiplier 
  × position_multiplier × deployment_multiplier × account_multiplier

COMPARE: Expected_Final_Size vs. SAOutput.final_size_after_cap

IF |Expected - Actual| > 0.001 (0.1% tolerance) THEN
  GATE_FAIL: "Position sizing formula miscalculated"
  ACTION: Return to Stock-Analyst TURN 5 for recalculation
  DETAIL: Show expected vs. actual with step-by-step breakdown
ELSE
  GATE_PASS: Formula calculated correctly
```

**Check 2C: Hard 6% Cap Enforced**
```
IF SAOutput.final_size_after_cap > 0.06 THEN
  GATE_FAIL: "Position exceeds 6% hard cap"
  ACTION: Reject output
  DETAIL: Position = {X}%, cap = 6%
  REASON: Hard constraint violated
ELSE
  GATE_PASS: Position within 6% cap
```

---

### Gate 3: Sector Cap Validation (NEW)

**Trigger:** SAOutput received with sector allocation details

**Check 3A: Current Sector Allocation**
```
FROM SAOutput.sector_analysis:
  sector_name: String (e.g., "Metals")
  current_sector_percent: Float (from PORTFOLIO.md)
  sector_cap: Float (0.35)
  recommended_position_size: Float (from position sizing)
  
CALCULATE: post_trade_sector_percent = 
  (current_sector_percent + recommended_position_size)

IF post_trade_sector_percent > 0.35 THEN
  GATE_FAIL: "Position would exceed sector cap"
  ACTION: Escalate to Master-Architect
  DETAIL: 
    Current sector: {current}%
    Sector cap: 35%
    Headroom: {headroom}%
    Requested position: {size}%
    Post-trade sector: {projected}%
  REASON: Sector cap breach imminent (escalation trigger 1)
  
ELSE IF post_trade_sector_percent > 0.33 THEN
  GATE_WARN: "Position approaching sector cap"
  DETAIL: Only {headroom_remaining}% headroom left after trade
  ACTION: Log warning but allow with QA note
  
ELSE
  GATE_PASS: Sector cap validation successful
```

**Check 3B: Sector Headroom Calculation Verification**
```
FROM PORTFOLIO.md sector allocations:
  sector_cap = 0.35
  current_allocation = Float from portfolio
  
CALCULATE: sector_headroom = sector_cap - current_allocation

COMPARE with SAOutput.sector_headroom_percent

IF |Calculated - Reported| > 0.005 (0.5% tolerance) THEN
  GATE_FAIL: "Sector headroom miscalculated"
  ACTION: Return to Market-Analyst Step 0C
  DETAIL: Expected {calc}%, got {reported}%
ELSE
  GATE_PASS: Sector headroom correct
```

---

### Gate 4: Position Headroom Validation (NEW)

**Trigger:** SAOutput includes position details for existing sector stocks

**Check 4A: Position Concentration Status**
```
FROM SAOutput.position_fill_detail:
  current_position_percent: Float (e.g., 0.06)
  position_cap: Float (0.06)
  
CALCULATE: position_headroom = position_cap - current_position_percent

IF position_headroom < 0 THEN
  GATE_FAIL: "Existing position already at/over cap"
  ACTION: Escalate to Master-Architect
  DETAIL: Ticker {ticker} at {current}%, cap 6%
  REASON: Escalation trigger 2 (over-cap position exists)
  RECOMMENDATION: Consider trimming existing position or selecting different stock
  
ELSE IF position_headroom < 0.01 (1%) THEN
  GATE_WARN: "Existing position near cap"
  DETAIL: Only {headroom}% headroom available
  ACTION: Log and allow, but note constraint
  
ELSE
  GATE_PASS: Position headroom acceptable
```

**Check 4B: New Position Feasibility**
```
IF position is NEW to portfolio THEN
  GATE_PASS: New position, no headroom constraint
ELSE IF position is ADDITION to existing holding THEN
  FROM SAOutput.position_fill_multiplier (Step 5D):
    multiplier_applied: Float (0.2x → 1.0x based on headroom)
    
  VERIFY: multiplier matches headroom state
    <1% headroom → 0.2x multiplier
    1-2% headroom → 0.5x multiplier
    >2% headroom → 1.0x multiplier
    
  IF multiplier INCORRECT THEN
    GATE_FAIL: "Position fill multiplier incorrect"
    ACTION: Return to Stock-Analyst TURN 5D
    DETAIL: Show expected vs. applied multiplier
  ELSE
    GATE_PASS: Position multiplier correct
```

---

### Gate 5: Account Cash Availability (NEW)

**Trigger:** SAOutput specifies target account (Nick Trad or Carol Trad)

**Check 5A: Target Account Cash Verification**
```
FROM SAOutput.account_placement:
  target_account: String ("Nick Trad" or "Carol Trad")
  position_size_dollars: Float
  
FROM PORTFOLIO.md account breakdown:
  account_cash_available: Float
  
CALCULATE: cash_ratio = account_cash_available / position_size_dollars

IF cash_ratio < 1.0 THEN
  GATE_FAIL: "Target account has insufficient cash"
  ACTION: 
    IF alternative account available with sufficient cash THEN
      RECOMMEND: Alternative account placement
      DETAIL: {alt_account} has ${alt_cash} available
      ALLOW: Conditional pass with account change
    ELSE
      ESCALATE to Master-Architect
      DETAIL: No account has sufficient cash for ${position_size}
      REASON: Escalation trigger 4 (account cash insufficient)
      
ELSE IF cash_ratio < 1.5 THEN
  GATE_WARN: "Target account cash marginal"
  DETAIL: Account has {ratio}x position size in cash
  ACTION: Allow but log warning
  RECOMMENDATION: Consider smaller position or cash raise
  
ELSE
  GATE_PASS: Account cash available
```

**Check 5B: Account Cash Multiplier Verification**
```
FROM SAOutput.account_cash_multiplier (Step 5F):
  multiplier_applied: Float (0.5x → 1.0x)
  account_cash_available: Float
  position_size: Float
  
CALCULATE: cash_ratio = account_cash_available / (position_size before multiplier)

VERIFY: multiplier matches cash_ratio
  >1.0x ratio → 1.0x multiplier
  1.5-1.0x ratio → 0.9x multiplier
  <1.5x ratio → 0.7x multiplier
  
IF multiplier INCORRECT THEN
  GATE_FAIL: "Account cash multiplier incorrect"
  ACTION: Return to Stock-Analyst TURN 5F
  DETAIL: Show expected vs. applied multiplier
ELSE
  GATE_PASS: Account multiplier correct
```

---

### Gate 6: Deployment Capacity Validation (NEW)

**Trigger:** SAOutput final position sizing complete

**Check 6A: Portfolio Excess Cash Verification**
```
FROM PORTFOLIO.md portfolio state:
  total_portfolio_value: Float
  current_cash: Float
  target_cash_percent: Float (0.15)
  
CALCULATE: 
  target_cash_dollars = total_portfolio_value × target_cash_percent
  excess_cash = current_cash - target_cash_dollars
  
FROM SAOutput.position_size_dollars:
  requested_position: Float
  
IF requested_position > excess_cash THEN
  GATE_FAIL: "Position exceeds available deployment capacity"
  ACTION: Escalate to Master-Architect
  DETAIL:
    Excess cash available: ${excess_cash}
    Requested position: ${requested_position}
    Shortfall: ${requested_position - excess_cash}
  REASON: Escalation trigger 6 (deployment capacity exceeded)
  RECOMMENDATION: Reduce position size or raise cash
  
ELSE IF requested_position > (excess_cash × 0.8) THEN
  GATE_WARN: "Position uses significant deployment capacity"
  DETAIL: Uses {pct}% of available excess cash
  ACTION: Allow but note concentration
  
ELSE
  GATE_PASS: Deployment capacity adequate
```

**Check 6B: Post-Trade Cash Buffer Validation**
```
FROM SAOutput + PORTFOLIO.md:
  current_cash: Float
  position_size_dollars: Float
  target_cash_percent: Float
  
CALCULATE:
  post_trade_cash = current_cash - position_size_dollars
  post_trade_cash_percent = post_trade_cash / total_portfolio_value
  
FROM MAOutput.constraints.cashbuffermin:
  required_buffer: Float (0.15 default, risk-adjusted)
  
IF post_trade_cash_percent < required_buffer THEN
  GATE_FAIL: "Position would violate cash buffer minimum"
  ACTION: Reject or reduce position size
  DETAIL:
    Current cash: {current}% (${current_dollars})
    Required buffer: {required}%
    Post-trade cash: {post_trade}% (${post_trade_dollars})
    Shortfall: {shortfall}%
  REASON: Hard constraint violation (cash buffer)
  RECOMMENDATION: Reduce position or raise cash
  
ELSE
  GATE_PASS: Cash buffer maintained
```

---

### Gate 7: Escalation Trigger Comprehensive Check (NEW)

**Trigger:** All previous gates passed, final pre-execution validation

**Check 7A: Escalation Trigger 1 - Sector Cap Breach**
```
IF SAOutput.escalation_trigger_1_sector_cap_breach = TRUE THEN
  GATE_FAIL: "Escalation trigger 1: Sector cap breach imminent"
  ACTION: Escalate to Master-Architect
  BLOCK: Do not proceed to execution
```

**Check 7B: Escalation Trigger 2 - Over-Cap Position Exists**
```
FROM PORTFOLIO.md positions:
  FOR EACH position in portfolio:
    IF position_percent > 0.06 THEN
      GATE_FAIL: "Escalation trigger 2: Over-cap position exists"
      DETAIL: {ticker} at {pct}% (cap 6%)
      ACTION: Escalate to Master-Architect
      BLOCK: Do not proceed to execution
      RECOMMENDATION: Trim over-cap position first
```

**Check 7C: Escalation Trigger 3 - Sector Full**
```
FROM PORTFOLIO.md sector allocations:
  FOR EACH sector:
    IF sector_current_percent >= 0.35 THEN
      IF SAOutput.sector = full_sector THEN
        GATE_FAIL: "Escalation trigger 3: Cannot add to full sector"
        DETAIL: {sector} at {pct}% (cap 35%)
        ACTION: Escalate to Master-Architect
        BLOCK: Do not proceed
```

**Check 7D: Escalation Trigger 4 - Account Cash Insufficient**
```
FROM SAOutput.account_placement:
  target_account: String
  position_size_dollars: Float
  
IF account has insufficient cash THEN
  GATE_FAIL: "Escalation trigger 4: Account cash insufficient"
  ACTION: Escalate to Master-Architect
  BLOCK: Do not proceed to execution
```

**Check 7E: Escalation Trigger 5 - Portfolio Freshness Unconfirmed**
```
IF SAOutput.portfolio_freshness = "ESCALATED" THEN
  GATE_FAIL: "Escalation trigger 5: Portfolio freshness unconfirmed"
  ACTION: Escalate to Master-Architect
  BLOCK: Do not proceed to execution
```

**Check 7F: Escalation Trigger 6 - Deployment Capacity Exceeded**
```
IF position_size_dollars > excess_cash THEN
  GATE_FAIL: "Escalation trigger 6: Deployment capacity exceeded"
  ACTION: Escalate to Master-Architect
  BLOCK: Do not proceed to execution
```

---

## CROSS-VALIDATION WITH MARKET ANALYST OUTPUT

### Gate 8: MAOutput Constraint Alignment (NEW)

**Trigger:** SAOutput received; MAOutput available from regime analysis

**Check 8A: Position Size vs. maxpositionsize Constraint**
```
FROM MAOutput.constraints.maxpositionsize:
  sector: String
  max: Float (per-sector maximum)
  reason: String
  
FROM SAOutput.recommended_position:
  sector: String
  size: Float
  
IF SAOutput.sector matches MAOutput sector THEN
  IF SAOutput.size > MAOutput.max THEN
    GATE_FAIL: "Position exceeds MAOutput per-sector constraint"
    ACTION: Return to Stock-Analyst for re-sizing
    DETAIL: 
      MAOutput max: {max}%
      SAOutput recommended: {size}%
      Reason for constraint: {reason}
ELSE
  GATE_PASS: Position within MAOutput constraints
```

**Check 8B: Cash Buffer Alignment**
```
FROM MAOutput.constraints.cashbuffermin:
  required: Float (risk-adjusted)
  
FROM SAOutput.post_trade_analysis:
  post_trade_cash_percent: Float
  
IF post_trade_cash_percent < MAOutput.required THEN
  GATE_FAIL: "Post-trade cash below MAOutput requirement"
  ACTION: Reject or reduce position
  DETAIL:
    MAOutput required: {required}%
    Post-trade: {post_trade}%
ELSE
  GATE_PASS: Cash buffer meets MAOutput standard
```

**Check 8C: Risk Regime Multiplier Consistency**
```
FROM MAOutput.regime.riskregime:
  regime: String (NORMAL/ELEVATED/HIGH/CRISIS)
  riskscore: Float
  
FROM SAOutput.position_sizing_detail.risk_regime_multiplier:
  applied_multiplier: Float
  
VERIFY: Risk regime matches
  NORMAL (0.0-0.3): 1.0x multiplier
  ELEVATED (0.3-0.6): 0.8x multiplier
  HIGH (0.6-0.8): 0.5x multiplier
  CRISIS (0.8-1.0): 0.2x multiplier
  
IF multiplier doesn't match regime THEN
  GATE_FAIL: "Risk multiplier inconsistent with MAOutput"
  ACTION: Return to Stock-Analyst for correction
ELSE
  GATE_PASS: Risk alignment confirmed
```

---

## QUALITY ASSURANCE REPORT (QAReport v8.0.4)

### QAReport JSON Schema

When QA gates complete, output **QAReport** with complete validation record:

```json
{
  "meta": {
    "timestamp": "ISO8601",
    "qa_version": "8.0.4",
    "stock_ticker": "String",
    "analyst_turn": 5,
    "portfolio_timestamp": "ISO8601",
    "portfolio_age_trading_days": Integer
  },
  "gates": {
    "gate_1_portfolio_freshness": {
      "status": "PASS | FAIL | WARN",
      "checks": {
        "timestamp_valid": Boolean,
        "freshness": "FRESH | STALE | ESCALATED",
        "structure_complete": Boolean
      },
      "detail": "String"
    },
    "gate_2_position_sizing_formula": {
      "status": "PASS | FAIL",
      "checks": {
        "all_steps_present": Boolean,
        "conviction_base_size": Float,
        "risk_multiplier": Float,
        "sector_fill_multiplier": Float,
        "position_fill_multiplier": Float,
        "deployment_multiplier": Float,
        "account_cash_multiplier": Float,
        "formula_accurate": Boolean,
        "hard_6_percent_cap_enforced": Boolean
      },
      "detail": "String"
    },
    "gate_3_sector_cap_validation": {
      "status": "PASS | FAIL | WARN",
      "checks": {
        "current_sector_percent": Float,
        "post_trade_sector_percent": Float,
        "sector_cap": Float,
        "sector_headroom": Float,
        "cap_breach_imminent": Boolean
      },
      "detail": "String"
    },
    "gate_4_position_headroom_validation": {
      "status": "PASS | FAIL | WARN",
      "checks": {
        "existing_position_percent": Float,
        "position_cap": Float,
        "position_headroom": Float,
        "over_cap_position_exists": Boolean,
        "position_multiplier_correct": Boolean
      },
      "detail": "String"
    },
    "gate_5_account_cash_availability": {
      "status": "PASS | FAIL | WARN",
      "checks": {
        "target_account": "String",
        "position_size_dollars": Float,
        "account_cash_available": Float,
        "cash_ratio": Float,
        "sufficient_cash": Boolean,
        "account_multiplier_correct": Boolean
      },
      "detail": "String",
      "alternative_account": "String | null"
    },
    "gate_6_deployment_capacity_validation": {
      "status": "PASS | FAIL | WARN",
      "checks": {
        "excess_cash_available": Float,
        "position_size_requested": Float,
        "deployment_capacity_sufficient": Boolean,
        "post_trade_cash_percent": Float,
        "required_buffer_percent": Float,
        "buffer_maintained": Boolean
      },
      "detail": "String"
    },
    "gate_7_escalation_triggers": {
      "status": "PASS | FAIL",
      "triggers": {
        "trigger_1_sector_cap_breach": Boolean,
        "trigger_2_over_cap_position": Boolean,
        "trigger_3_sector_full": Boolean,
        "trigger_4_account_cash_insufficient": Boolean,
        "trigger_5_portfolio_freshness_unconfirmed": Boolean,
        "trigger_6_deployment_capacity_exceeded": Boolean
      },
      "triggered_list": ["Trigger names if any fired"],
      "detail": "String"
    },
    "gate_8_market_analyst_alignment": {
      "status": "PASS | FAIL | WARN",
      "checks": {
        "position_within_ma_constraints": Boolean,
        "cash_buffer_meets_ma_standard": Boolean,
        "risk_multiplier_consistent": Boolean,
        "sector_leadership_alignment": Boolean
      },
      "detail": "String"
    }
  },
  "overall_status": "APPROVE | REJECT | ESCALATE | REQUEST_REVISION",
  "decision": {
    "approved": Boolean,
    "reason": "String",
    "recommendations": ["Array of strings"],
    "escalation_path": "String if ESCALATE",
    "revision_requested": "String if REQUEST_REVISION"
  },
  "portfolio_impact": {
    "position_percent_of_portfolio": Float,
    "position_percent_of_sector": Float,
    "post_trade_cash_percent": Float,
    "post_trade_sector_percent": Float,
    "sector_headroom_remaining": Float,
    "account_cash_remaining": Float
  },
  "audit_trail": {
    "gates_passed": Integer,
    "gates_failed": Integer,
    "gates_warned": Integer,
    "total_checks": Integer,
    "qa_analyst_id": "String",
    "timestamp": "ISO8601"
  }
}
```

---

## DECISION LOGIC: APPROVE/REJECT/ESCALATE

### Final QA Decision Tree

```
START: QAReport gates complete

├─ IF any gate status = FAIL
│  ├─ OVERALL_STATUS = REJECT
│  ├─ DO NOT APPROVE
│  ├─ RETURN to Stock-Analyst with specific gate failure
│  └─ ACTION: Indicate which gate(s) failed + reason
│
├─ IF any escalation trigger = TRUE
│  ├─ OVERALL_STATUS = ESCALATE
│  ├─ DO NOT APPROVE
│  ├─ ESCALATE to Master-Architect
│  └─ DETAIL: Which trigger(s) fired + reason
│
├─ IF multiple gates = WARN
│  ├─ OVERALL_STATUS = REQUEST_REVISION
│  ├─ DO NOT APPROVE (unless explicitly overridden)
│  ├─ RECOMMEND changes to reduce risk
│  └─ ALLOW re-submission with changes
│
├─ IF all gates = PASS AND no escalations
│  ├─ OVERALL_STATUS = APPROVE
│  ├─ GENERATE QAReport
│  ├─ PASS to Portfolio-Orchestrator
│  └─ ACTION: Proceed to execution
│
└─ IF mixed (some PASS, some WARN, none FAIL)
   ├─ OVERALL_STATUS = APPROVE (with caveats)
   ├─ GENERATE QAReport
   ├─ Include warnings in audit trail
   ├─ PASS to Portfolio-Orchestrator
   └─ NOTE: Logged warnings for monitoring
```

---

## REJECTION SCENARIOS (REQUEST REVISION)

### When to Reject SAOutput

**Reject if:**
1. ❌ Gate 1: Portfolio data stale (>5 days) or unconfirmed
2. ❌ Gate 2: Position sizing formula incomplete or miscalculated
3. ❌ Gate 2: Position exceeds 6% hard cap
4. ❌ Gate 3: Position would exceed sector cap
5. ❌ Gate 4: Over-cap position already exists in portfolio
6. ❌ Gate 5: No account has sufficient cash for position
7. ❌ Gate 6: Position exceeds deployment capacity
8. ❌ Gate 6: Post-trade cash below buffer minimum
9. ❌ Gate 7: Any escalation trigger fired
10. ❌ Gate 8: Position violates MAOutput constraints

**Rejection Output:**
```
{
  "qa_decision": "REJECT",
  "reason": "Position sizing formula incomplete",
  "failed_gate": "Gate 2 - Position Sizing Formula Verification",
  "specific_issue": "Position fill multiplier (Step 5D) missing",
  "return_to": "Stock-Analyst TURN 5, Step 5D",
  "required_action": "Recalculate position fill multiplier based on sector headroom",
  "detail": "Sector headroom: 15%, should apply 1.0x multiplier. Currently missing.",
  "qa_analyst_id": "QA-001",
  "timestamp": "ISO8601"
}
```

---

## APPROVAL SCENARIOS

### When to Approve SAOutput

**Approve if:**
✅ All gates: PASS or (WARN with acceptable mitigations)  
✅ No escalation triggers fired  
✅ Position within 6% hard cap  
✅ Sector allocation within 35% cap  
✅ Account has sufficient cash  
✅ Post-trade cash ≥ required buffer  
✅ All 8 multiplier steps calculated  
✅ Formula accuracy within tolerance  
✅ MAOutput constraints respected  

**Approval Output:**
```
{
  "qa_decision": "APPROVE",
  "reason": "All gates passed, constraints satisfied",
  "gates_summary": "8 gates passed, 0 failed, 0 warnings",
  "position_sizing": {
    "ticker": "AU",
    "position_percent": 0.032,
    "position_dollars": 23040,
    "conviction_basis": "HIGH (A+ fundamentals + analyst revisions)",
    "formula_cascade": "5.0% × 0.6x × 1.0x × 1.0x × 1.2x × 0.9x = 3.24%"
  },
  "portfolio_impact": {
    "post_trade_sector_percent": 0.325,
    "sector_headroom_remaining": 0.025,
    "post_trade_cash_percent": 0.524,
    "account": "Nick Trad",
    "account_cash_remaining": 206960
  },
  "escalation_triggers": "0 of 6 fired",
  "ready_for_execution": true,
  "qa_analyst_id": "QA-001",
  "timestamp": "ISO8601"
}
```

---

## ESCALATION SCENARIOS (ESCALATE TO MASTER-ARCHITECT)

### When to Escalate

**Escalate if:**
⚠️ Portfolio data stale (>5 trading days, cannot confirm)  
⚠️ Sector cap breach imminent (>33% allocated)  
⚠️ Over-cap position exists in portfolio (>6%)  
⚠️ Sector at/over cap, cannot add position  
⚠️ No account has sufficient cash for position  
⚠️ Position exceeds deployment capacity  
⚠️ Post-trade cash below required buffer  
⚠️ Any escalation trigger fired  
⚠️ SAOutput conflicts with MAOutput constraints  

**Escalation Output:**
```
{
  "qa_decision": "ESCALATE",
  "reason": "Escalation trigger 3: Sector at cap",
  "escalation_trigger": "Trigger 3 - Sector Full",
  "detail": {
    "sector": "Metals",
    "current_sector_percent": 0.350,
    "sector_cap": 0.35,
    "headroom_available": 0.0,
    "requested_position": 0.04,
    "issue": "Cannot add Metals position; sector at maximum capacity"
  },
  "recommended_actions": [
    "Option 1: Trim existing Metals position (currently 30.7%) to make room",
    "Option 2: Select stock from different sector with more headroom",
    "Option 3: Wait for existing Metals position to be rotated out"
  ],
  "escalate_to": "Master-Architect",
  "reason_for_escalation": "Portfolio concentration decision required",
  "qa_analyst_id": "QA-001",
  "timestamp": "ISO8601"
}
```

---

## REVISION REQUEST SCENARIOS

### When to Request Revision

**Request revision if:**
⚠️ Multiple WARN gates (acceptable but not optimal)  
⚠️ Position uses >80% of deployment capacity  
⚠️ Position approaches sector cap (>33% post-trade)  
⚠️ Account cash ratio marginal (1.0-1.5x)  
⚠️ Post-trade cash approaching buffer minimum  

**Revision Output:**
```
{
  "qa_decision": "REQUEST_REVISION",
  "reason": "Position size can be optimized",
  "warnings": [
    "Gate 5 WARN: Account cash ratio marginal (1.2x)",
    "Gate 6 WARN: Position uses 75% of deployment capacity",
    "Gate 3 WARN: Post-trade sector approaches concentration (34.8%)"
  ],
  "recommendations": [
    "Reduce position from 3.24% to 2.5% to improve margins",
    "Alternative: Increase cash from $410k to $450k before execution"
  ],
  "resubmit_with": "Revised position size and reasoning",
  "optional": true,
  "proceed_without_revision": "Optional, but caveats logged in audit trail",
  "qa_analyst_id": "QA-001",
  "timestamp": "ISO8601"
}
```

---

## AUDIT TRAIL & COMPLIANCE

### Complete QA Audit Record

Every QAReport includes complete audit trail:

```json
{
  "audit_trail": {
    "qa_version": "8.0.4",
    "stock_ticker": "AU",
    "saoutput_timestamp": "ISO8601",
    "qa_completion_timestamp": "ISO8601",
    "gates_checked": 8,
    "gates_passed": 8,
    "gates_failed": 0,
    "gates_warned": 0,
    "escalation_triggers_fired": 0,
    "constraints_enforced": [
      "6% position cap",
      "35% sector cap",
      "15% cash buffer (risk-adjusted)",
      "Account cash availability",
      "Deployment capacity"
    ],
    "data_freshness": {
      "portfolio_timestamp": "ISO8601",
      "portfolio_age_trading_days": 2,
      "portfolio_freshness": "FRESH"
    },
    "formula_verification": {
      "steps_verified": 8,
      "all_multipliers_checked": true,
      "formula_accuracy_tolerance": "±0.1%",
      "actual_variance": "0.0%"
    },
    "cross_validation": {
      "maoutput_available": true,
      "constraints_aligned": true,
      "risk_multiplier_consistent": true
    },
    "qa_analyst_id": "QA-001",
    "qa_analyst_confidence": 0.98,
    "final_qa_approval": "APPROVE",
    "ready_for_portfolio_orchestrator": true
  }
}
```

---

## QUALITY GATES CHECKLIST

### Pre-Execution QA Validation (v8.0.4 Complete)

**GATE EXECUTION CHECKLIST:**

- [ ] **Gate 1:** Portfolio freshness (timestamp ≤5 trading days)
- [ ] **Gate 1:** Portfolio context complete (all required fields)
- [ ] **Gate 2:** All 8 position sizing steps present
- [ ] **Gate 2:** Formula calculated correctly (±0.1% tolerance)
- [ ] **Gate 2:** Position ≤6% hard cap
- [ ] **Gate 3:** Current sector allocation ≤35%
- [ ] **Gate 3:** Post-trade sector allocation ≤35%
- [ ] **Gate 3:** Sector headroom calculation correct
- [ ] **Gate 4:** No existing over-cap positions (>6%)
- [ ] **Gate 4:** Position fill multiplier correct for headroom
- [ ] **Gate 5:** Target account has sufficient cash
- [ ] **Gate 5:** Account cash multiplier correct
- [ ] **Gate 6:** Position ≤ excess cash available
- [ ] **Gate 6:** Post-trade cash ≥ required buffer
- [ ] **Gate 7:** Escalation trigger 1 (sector cap): NOT fired
- [ ] **Gate 7:** Escalation trigger 2 (over-cap position): NOT fired
- [ ] **Gate 7:** Escalation trigger 3 (sector full): NOT fired
- [ ] **Gate 7:** Escalation trigger 4 (account cash): NOT fired
- [ ] **Gate 7:** Escalation trigger 5 (portfolio freshness): NOT fired
- [ ] **Gate 7:** Escalation trigger 6 (deployment capacity): NOT fired
- [ ] **Gate 8:** Position within MAOutput per-sector max
- [ ] **Gate 8:** Cash buffer meets MAOutput standard
- [ ] **Gate 8:** Risk multiplier matches MAOutput regime

**If ALL checkboxes checked: APPROVE for Portfolio-Orchestrator**

---

## VERSION HISTORY

| Version | Date | Changes |
|---------|------|---------|
| 8.0.3-alpha | Oct 1, 2025 | Pre-portfolio integration |
| 8.0.4 | Dec 8, 2025 | Added portfolio constraint validation (Gates 3-7), cross-validation with MAOutput (Gate 8), updated QAReport schema, added escalation trigger verification |

---

## NEXT PHASE: IMPLEMENTATION

When ready to implement v8.0.4:

1. **Update AGENT-OUTPUT-SCHEMA.md** with portfolio section requirements
2. **Create gate validation logic** for each of 8 gates
3. **Implement QAReport JSON schema** output
4. **Create test cases** for constraint violations
5. **Validate against sample positions** (should catch all constraint breaks)

---

**QUALITY ASSURANCE ENGINEER v8.0.4 - COMPLETE SPECIFICATION**

Ready for Portfolio-Orchestrator handoff when approved.

