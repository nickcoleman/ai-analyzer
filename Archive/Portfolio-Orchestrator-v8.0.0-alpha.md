# Portfolio Orchestrator v8.0.0-alpha

**Status:** IMPLEMENTATION COMPLETE  
**Date:** December 8, 2025  
**Version:** 8.0.0-alpha  
**Scope:** Root Agent Logic Engine for V8 Portfolio Allocation & Trade Generation

---

## EXECUTIVE SUMMARY

The **Portfolio Orchestrator (PO)** is the root decision engine within the V8 Analyst System. It is NOT an analytical agent—it is a logic orchestrator that synthesizes outputs from subordinate agents into executable trade instructions.

**Core Function:**  
`TradeInstruction = Orchestrate(SAOutput, MAOutput, QAReport, PortfolioState)`

The PO enforces the **Regime-Gated Decision Tree** and implements the critical **Contrarian Exception**, ensuring that high-conviction signals are maximized under supportive conditions, yet protected during hostile regimes.

---

## 1. INPUT CONTRACTS INGESTION LAYER

### Stock Analyst Output (SAOutput)

The PO receives a validated SAOutput JSON from Stock Analyst TURN 6.

**Required Fields:**
- `meta.symbol`, `meta.timestamp`, `meta.version`
- `thesis.convictiontier`, `thesis.convictionscore`, `thesis.strategytype`, `thesis.targetprice`, `thesis.timehorizon`
- `expertinputs`: FMA, IRM, MSPA, QMA (each with assessment and confidence)
- `reasoning.weightingcontext`, `reasoning.deconflictinglogic`, `reasoning.escalationcheck`

### Market Analyst Output (MAOutput)

The PO receives regime and constraint data from Market Analyst.

**Required Fields:**
- `meta.timestamp`, `meta.version`
- `regime.marketregime`, `regime.riskregime`, `regime.riskscore`
- `sectors.leading`, `sectors.lagging`, `sectors.avoid`
- `constraints.maxpositionsize`, `constraints.cashbuffermin`

### QA Report (QAReport)

Before orchestration, QA validates both inputs and flags any compliance failures.

**Required Field:**
- `status`: PASS | FAIL
- If FAIL: Stop and escalate to Master Architect

### Portfolio State (PortfolioState)

Current positions, cash, and concentration limits.

**Required Fields:**
- `timestamp`, `cashavailable`, `totalportfoliovalue`
- `positions[]` with symbol, quantity, marketprice, marketvalue

---

## 2. THE REGIME-GATED DECISION TREE - CORE LOGIC

The **Regime-Gated Decision Tree** is the heart of the Portfolio Orchestrator. It maps stock conviction signals against market regime to produce a final allocation multiplier.

### Decision Tree Logic Flow

```
IF SAOutput.thesis.convictiontier ∈ [LOW, MEDIUM-LOW]
  action = HOLD
  allocation = 0.0x
  rationale = "Conviction insufficient for action"

ELSE IF MAOutput.regime.riskregime = HOSTILE
  IF SAOutput.thesis.strategytype = CONTRARIAN
    action = BUY
    allocation = 0.2x (Pilot Size)
    rationale = "CONTRARIAN EXCEPTION: Pilot deployment in hostile regime"
  ELSE
    action = BLOCKED
    allocation = 0.0x
    rationale = "Market regime hostile; non-contrarian thesis blocked"

ELSE IF MAOutput.regime.riskregime = HIGH
  allocation = 0.4x (Base Position Sizing applies)
  action = BUY
  rationale = "High risk regime; scaled allocation"

ELSE IF MAOutput.regime.riskregime = ELEVATED
  allocation = 0.6x (Base Position Sizing applies)
  action = BUY
  rationale = "Elevated risk regime; reduced allocation"

ELSE (NORMAL regime)
  allocation = 1.0x (Base Position Sizing applies)
  action = BUY
  rationale = "Normal market regime; full conviction sizing"
```

### Regime-Gated Decision Matrix

| Conviction | Regime | Strategy | Allocation | Action | Rationale |
|-----------|--------|----------|-----------|--------|-----------|
| HIGH | SUPPORTIVE | GROWTH | 1.0x | BUY | Full conviction sizing |
| HIGH | SUPPORTIVE | VALUE | 1.0x | BUY | Full conviction sizing |
| HIGH | HOSTILE | CONTRARIAN | 0.2x | BUY | Pilot EXCEPTION |
| HIGH | HOSTILE | GROWTH | 0.0x | BLOCKED | Hostile blocks non-contrarian |
| MED-HIGH | SUPPORTIVE | ANY | 1.0x | BUY | Sufficient conviction |
| MED-HIGH | HOSTILE | CONTRARIAN | 0.2x | BUY | Pilot EXCEPTION |
| MEDIUM | ANY | ANY | 1.0x | HOLD | Insufficient for action |
| MED-LOW | ANY | ANY | 0.0x | HOLD | Insufficient for action |
| LOW | ANY | ANY | 0.0x | HOLD | Insufficient for action |

---

## 3. POSITION SIZING ENGINE

### Base Position Sizing from Stock Analyst

The Stock Analyst TURN 5 calculates base size using the Position Sizing Framework:

| Tier | Base Size |
|------|-----------|
| HIGH | 5.0 |
| MEDIUM-HIGH | 3.0 |
| MEDIUM | 2.0 |
| MEDIUM-LOW | 1.0 |
| LOW | 0.5 |

**Risk Multiplier from MAOutput.regime:**
- NORMAL: 1.0x
- ELEVATED: 0.6x
- HIGH: 0.4x
- CRISIS: 0.2x

**Formula:** `Theoretical Size = Base Size × Risk Multiplier`

### Regime Multiplier from PO Decision Tree

The Portfolio Orchestrator applies an additional multiplier based on the Regime-Gated Tree:

| Regime | Multiplier |
|--------|-----------|
| SUPPORTIVE | 1.0x |
| NEUTRAL | 1.0x |
| NEGATIVE | 0.6x |
| HOSTILE | 0.0x (or 0.2x if CONTRARIAN) |

**Formula:** `Final Size = Theoretical Size × Regime Multiplier`

### Constraint Enforcement

After calculating final size, the Orchestrator enforces portfolio-level constraints:

1. **Max Position Size:** If `Final Size > MAOutput.constraints.maxpositionsize`, cap at constraint.
2. **Cash Available:** If target dollars exceed available cash, partial fill.
3. **Cash Buffer Minimum:** Ensure post-trade cash meets minimum buffer requirement.

---

## 4. CONTRARIAN EXCEPTION LOGIC - CRITICAL FEATURE

### Purpose

In hostile market regimes, conventional strategies are suppressed to protect capital. However, contrarian theses are specifically designed to profit from extreme market dislocations. The **Contrarian Exception** allows pilot deployments during hostile regimes, enabling asymmetric upside capture with controlled downside risk.

### Trigger Conditions

The Contrarian Exception activates when **ALL** of the following are true:

1. **Strategy Type:** `SAOutput.thesis.strategytype = CONTRARIAN`
2. **Regime:** `MAOutput.regime.riskregime` maps to HOSTILE
3. **Conviction:** `SAOutput.thesis.convictiontier ∈ [MEDIUM-HIGH, HIGH]`
4. **Escalation Status:** `SAOutput.reasoning.escalationcheck = PASS`

### Exception Logic

```python
if (isContrarian AND isHostile AND sufficientConviction AND escalationPass):
  BaseSize = convictionToBase(SAOutput.thesis.convictiontier)
  RiskMult = getRiskMultiplier(MAOutput.regime.riskregime)
  BaseFinal = BaseSize × RiskMult
  
  # Apply contrarian multiplier (0.2x = 20% of normal)
  PilotSize = BaseFinal × 0.2
  
  # Enforce constraints
  FinalSize = min(PilotSize, MAOutput.constraints.maxpositionsize)
  
  return {
    action: "BUY",
    allocation: FinalSize,
    rationale: f"CONTRARIAN EXCEPTION {FinalSize*100}% pilot deployment in hostile regime",
    flagExceptionApplied: true
  }
else:
  return None  # Exception does not apply
```

### Example Scenario

**Tech Stock in Market Crisis**

- SAOutput.symbol: AAPL
- SAOutput.thesis.convictiontier: HIGH (base 5.0)
- SAOutput.thesis.strategytype: CONTRARIAN
- SAOutput.reasoning.escalationcheck: PASS
- MAOutput.regime.riskregime: CRISIS (0.2x multiplier)
- MAOutput.constraints.maxpositionsize: 3.0
- Portfolio Value: $500,000

**Calculation:**
1. Base Size: HIGH = 5.0
2. Risk Multiplier: CRISIS = 0.2x
3. BaseFinal: 5.0 × 0.2 = 1.0
4. Apply Contrarian Multiplier: 1.0 × 0.2 = 0.2
5. Enforce Max: min(0.2, 3.0) = 0.2
6. FinalSize: 0.2% of $500k = $1,000

**TradeInstruction Output:**
```json
{
  "symbol": "AAPL",
  "action": "BUY",
  "quantitypct": 0.002,
  "ordertype": "LIMIT",
  "rationale": "CONTRARIAN EXCEPTION 0.2% pilot deployment in market crisis",
  "exceptionApplied": true
}
```

---

## 5. OUTPUT GENERATION ORCHESTRATION LAYER

### Trade Instruction JSON Schema

```json
{
  "meta": {
    "portfolioorchestratorversion": "v8.0.0-alpha",
    "timestamp": "ISO8601",
    "executiondate": "YYYY-MM-DD",
    "instructionid": "PO20251208001AAPL"
  },
  "trade": {
    "symbol": "AAPL",
    "action": "BUY | SELL | HOLD | BLOCKED",
    "quantitypct": 0.025,
    "quantitydollars": 12500.0,
    "targetprice": 250.0,
    "currentprice": 230.0,
    "upsidedownside": 0.087,
    "ordertype": "MARKET | LIMIT | STOPLOSS | TRAILINGSTOP | NONE",
    "convictiontier": "HIGH | MEDIUM-HIGH | MEDIUM | MEDIUM-LOW | LOW",
    "convictionscore": 0.85,
    "strategytype": "GROWTH | CONTRARIAN | VALUE",
    "timehorizon": "3MONTHS | 6MONTHS | 12MONTHS"
  },
  "reasoning": {
    "regime": "SUPPORTIVE | NEUTRAL | NEGATIVE | HOSTILE",
    "riskregime": "NORMAL | ELEVATED | HIGH | CRISIS",
    "marketregime": "GROWTH | VALUE | DEFENSIVE | TRANSITION",
    "strategy": "GROWTH | CONTRARIAN | VALUE",
    "baseallocation": 0.05,
    "riskmultiplier": 1.0,
    "regimemultiplier": 1.0,
    "finalallocation": 0.025,
    "exceptionapplied": false,
    "constraintsapplied": ["MAXPOSITIONSIZE"],
    "rationale": "HIGH conviction 0.85 GROWTH thesis in supportive market regime. Position capped at 2.5% per maxpositionsize constraint."
  },
  "validation": {
    "qastatus": "PASS | FAIL",
    "escalationflag": "PASS | ALERT",
    "allconstraintsmet": true,
    "datafreshness": {
      "saoutputageminutes": 12.5,
      "maoutputageminutes": 45.0,
      "portfoliostateageminutes": 5.0,
      "freshnessstatus": "FRESH | STALEWARNING | STALECRITICAL"
    }
  }
}
```

### Batch Output Format

For multiple stocks analyzed in a cycle:

```json
{
  "meta": {
    "batchid": "PO20251208001",
    "timestamp": "ISO8601",
    "executiondate": "YYYY-MM-DD",
    "portfoliosnapshot": {
      "totalvalue": 500000.0,
      "cashavailable": 50000.0,
      "numpositions": 12
    }
  },
  "instructions": [
    { "TradeInstruction 1" },
    { "TradeInstruction 2" },
    { "TradeInstruction 3" }
  ],
  "summary": {
    "totalbuysignals": 2,
    "totalblocked": 1,
    "totalholds": 0,
    "totalallocationrequested": 0.05,
    "cashimpact": -25000.0,
    "estimatedportfoliovalue": 525000.0
  }
}
```

---

## Version History

| Version | Date | Status | Changes |
|---------|------|--------|---------|
| v8.0.0-alpha | 2025-12-08 | IMPLEMENTATION COMPLETE | Root orchestrator logic engine. Regime-gated decision tree for stock conviction vs market regime. Contrarian exception handler for pilot deployments in hostile regimes. TradeInstruction producer. Authority: DIRECTIVE-E-PORTFOLIO-ORCHESTRATOR.md and Analyst-Technical-Specification v8.0.4 Section 4.6. |

---

## Related Files

- Stock-Analyst.md v8.0.3 (SAOutput producer)
- Market-Analyst.md v8.0.0 (MAOutput producer)
- Trade-Instruction-Schema.json v8.0.0 (output schema)
- Analyst-Technical-Specification.md v8.0.4 (authority)

---
