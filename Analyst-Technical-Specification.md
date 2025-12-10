# Analyst-Technical-Specification.md v8.0.4-UPDATED - GUARDRAILS INTEGRATED

**Version:** 8.0.4-UPDATED  
**Updated:** December 8, 2025, 7:32 PM MST  
**Status:** COMPLETE - Guardrails Framework v1.4.7 Integrated  
**New Section:** 5.2 Guardrails & Conviction-Cap Tier System

---

## SECTION 5.2: GUARDRAILS & CONVICTION-CAP TIER SYSTEM (NEW)

**Authority:** QUALITY-Guardrails.md v1.4.7  
**Integration Date:** December 8, 2025  
**Status:** Production-ready

### 5.2.1 Core Principle: Conviction-Cap Tiers

**Definition:** Unvalidated assumptions create a conviction ceiling. More unvalidated assumptions → lower maximum conviction → lower position sizing.

**Why:** Prevents overconfidence in event-driven or execution-dependent theses while allowing honest analysis of manageable risks.

### 5.2.2 Conviction-Cap Tier Table (Reference)

| Unvalidated Assumptions | Max Conviction | Conviction Tier | Position Size | Recommendation Type |
|---|---|---|---|---|
| **0-1** | 70-75% | **HIGH** | 3-5% portfolio | Standard BUY/HOLD/SELL |
| **2-3** | 55-60% | **MEDIUM** | 1.5-3% portfolio | SPECULATIVE/CONDITIONAL |
| **4+** | <55% | **LOW** | 0.5-1.5% or SKIP | WATCH (no BUY/SELL) |

**KEY ENFORCEMENT RULE:** Final conviction % cannot exceed ceiling for your unvalidated assumption count. Evidence can be strong, but unknowns cap conviction.

### 5.2.3 Assumption Taxonomy & Penalties

**Four types of assumptions, different penalties:**

| Type | Definition | Validation Required | Penalty if Unvalidated | Examples |
|---|---|---|---|---|
| **Binary** | Either true or false; no middle ground | Definitive evidence (binding agreements, regulatory approvals, court rulings) | **-15 points** | M&A closes, dividend approved, regulatory OK granted |
| **Durability** | Current favorable conditions persist (cycle, margins, growth) | Multi-quarter track record + forward indicators (guidance, industry data) | **-10 points** | Market cycle continues, margins sustain, DOCSIS strength holds |
| **Macro** | Macro environment remains within stable range | Cannot be fully validated; test via scenarios | **-12 points** | Rates stay 4-5%, credit markets function, oil stays $70-90 |
| **Execution** | Company achieves operational/financial targets | Management track record + recent quarterly confirmation | **-8 points** | Company hits $500M revenue, EBITDA 35%, FCF = $300M |

### 5.2.4 Conviction Calculation with Guardrails

**Step-by-Step Process:**

**Step 1: Establish Baseline Conviction (before assumptions)**
- Quality and breadth of supporting data
- Depth of validation across fundamental/technical/quantitative
- Data freshness penalties applied
- Typical baseline: 65-75% (high-quality analysis without external event risk)

**Step 2: Identify All Key Assumptions (3-5 maximum)**
- Frame as: "This thesis requires X to be true"
- Identify BEFORE gathering evidence (prevents bias)
- Type each: Binary / Durability / Macro / Execution

**Step 3: Validate Each Assumption**
- Mark: VALIDATED / PARTIALLY-VALIDATED / UNVALIDATED
- Show evidence supporting each status

**Step 4: Count Unvalidated Assumptions (strict count)**
- Count only UNVALIDATED (not partially-validated)
- Find conviction cap in tier table above

**Step 5: Calculate Final Conviction**
```
Final Conviction % = (Baseline %) - (Sum of penalties) - (Data freshness deduction)
BUT CAPPED AT: Maximum allowed by conviction-cap tier (based on unvalidated count)

Example:
Baseline Conviction: 72%
Assumption 1 (Binary, UNVALIDATED): -15 points
Assumption 2 (Durability, VALIDATED): 0 points
Assumption 3 (Macro, UNVALIDATED): -12 points
Data Freshness Penalty: -2 points
Calculated: 72 - 15 - 0 - 12 - 2 = 43%

But Unvalidated Count = 2 → Conviction-cap tier = 55-60% (MEDIUM)
Final Conviction = 55% (capped to tier minimum since calculated fell below)
```

**Step 6: Assign Recommendation Tier & Position Size**
- HIGH (70-75%): Standard BUY/HOLD/SELL, 3-5% position
- MEDIUM (55-60%): SPECULATIVE BUY/CONDITIONAL HOLD, 1.5-3% position
- LOW (<55%): WATCH only (no BUY/SELL), 0.5-1.5% or skip

### 5.2.5 Mandatory Guardrail Disclosure (in all analyses)

**All Stock Analyst analyses MUST document:**

```
CONVICTION CALCULATION & GUARDRAIL TIER:
═════════════════════════════════════════════════════════════════

Baseline Conviction: [XX]% 
  (Fundamental + Technical + Quantitative analysis strength)

Key Assumptions:
  1. [Assumption Type: Binary/Durability/Macro/Execution]
     Status: VALIDATED / PARTIALLY-VALIDATED / UNVALIDATED
     Evidence: [One sentence justification]
     Penalty if unvalidated: [-X points]
  
  2. [Assumption Type]
     Status: [Status]
     Evidence: [Justification]
     Penalty if unvalidated: [-X points]
  
  3. [Assumption Type]
     Status: [Status]
     Evidence: [Justification]
     Penalty if unvalidated: [-X points]

Unvalidated Assumption Count: [N]
Conviction-Cap Tier: [HIGH (70-75%) / MEDIUM (55-60%) / LOW (<55%)]

Penalty Calculation:
  - Baseline: 72%
  - Assumption penalties: -15, 0, -12 (sum: -27%)
  - Data freshness: -2%
  - Calculated: 72% - 27% - 2% = 43%
  - Capped to: 55% (tier minimum for MEDIUM tier)

FINAL CONVICTION: 55% → CONVICTION TIER: MEDIUM

Recommendation Tier: SPECULATIVE BUY (not standard BUY due to 2 unvalidated assumptions)
Position Sizing: 1.5-3% portfolio maximum (MEDIUM tier allocation)

Disclosure: "Thesis depends on market cycle continuing and macro stability. 
Execution risk is manageable (track record strong), but cycle/macro risks are present. 
Analyst 55% confident despite two key unknowns."
```

### 5.2.6 Enforcement: Stock-Analyst TURN 4 Gate Check

**During Stock-Analyst TURN 4 (De-conflicting Conviction):**

**Guardrail Enforcement Steps:**

1. **Calculate conviction with penalties shown**
   - Show all assumption types, validation statuses, penalties

2. **Count unvalidated assumptions**
   - Strict count: UNVALIDATED only
   - Consult conviction-cap tier table

3. **Check conviction vs. cap**
   - If calculated conviction EXCEEDS tier ceiling:
     - **VIOLATION:** Final conviction capped to tier maximum
     - Analyst MUST acknowledge and document tier cap being enforced
   
   - If calculated conviction WITHIN tier ceiling:
     - ✓ PASS: Proceed to TURN 5

4. **If guardrail violation detected:**
   - Document: "Conviction [X]% exceeds tier cap [Y]%. Conviction capped to [Y]%."
   - No override possible at TURN 4 level
   - Proceeds to TURN 6 QA gate for external validation

### 5.2.7 Quality Assurance Engineer - Guardrails Validation

**QA Engineer validates (in QA-Engineer.md v8.0.3):**

✅ **Assumption Documentation**
- All key assumptions identified and typed (Binary/Durability/Macro/Execution)
- Validation status documented for each
- Evidence provided for each status

✅ **Conviction Calculation**
- Baseline conviction properly established
- Assumption penalties correctly applied per type
- Data freshness penalties included
- Final conviction % calculated and shown

✅ **Conviction-Cap Tier Enforcement**
- Unvalidated assumption count correct
- Conviction-cap tier properly identified from table
- Final conviction respects tier ceiling (no violations)
- Recommendation tier matches conviction (HIGH/MEDIUM/LOW)
- Position size matches conviction tier allocation table

✅ **Guardrail Compliance Score**
- QA generates guardrail_compliance_score (0-100%)
- Violations flagged in QAReport
- If violations detected: QAReport.status = "PASS but Guardrail Violations" (escalation required)

### 5.2.8 Master Architect Review - Guardrail Exceptions

**Master-Architect.md v8.0.0 process for guardrail overrides:**

**Scenario: Stock Analyst overrides guardrail conviction cap**

Example: Analyst calculates 60% conviction but tier cap is 55% (MEDIUM tier). Analyst argues market data supports higher conviction and requests override.

**Master Architect Adjudication:**
1. Review analyst's justification for override
2. Review guardrail calculation (confirm tier cap is correct)
3. Evaluate: Is override justified? Can guardrail be relaxed?
4. Decision options:
   - ✓ **ACCEPT:** Override approved, conviction stands at requested level
   - ✗ **REJECT:** Override denied, conviction capped to tier ceiling
   - ⚖️ **COMPROMISE:** Accept partial override (conviction 57% vs requested 60%)
5. Document decision with rationale
6. Conviction becomes final with Master Architect stamp

**Guardrail override is RARE and requires explicit Master Architect approval** (see Master-Architect.md for full process)

### 5.2.9 Case Study 1: Using Guardrails - COMM (Event-Driven)

**Thesis:** CCS deal creates two-part valuation (deal value + RemainCo value)

**Assumptions Identified:**
1. CCS deal closes H1 2026 (Binary)
2. RemainCo EBITDA sustains guidance (Durability)  
3. Multiple expansion post-deal (Macro)

**Validation Assessment:**
- Assumption 1: PARTIALLY-VALIDATED (binding agreement signed, FTC approval pending)
- Assumption 2: VALIDATED (recent quarters confirm guidance)
- Assumption 3: UNVALIDATED (historical precedent but macro-dependent, not proven)

**Conviction Calculation:**
```
Baseline: 72% (strong fundamental + technical case)
Assumption 1 (Binary, PARTIALLY): -0 points (binding agreement = risk mitigated)
Assumption 2 (Durability, VALIDATED): 0 points
Assumption 3 (Macro, UNVALIDATED): -12 points (macro risk unproven)
Data freshness: 0 points (all data <7 days)
Calculated: 72% - 0 - 0 - 12 - 0 = 60%

Unvalidated count: 1 (Assumption 3)
Conviction-cap tier: 70-75% (HIGH tier)
Final Conviction: 60% (calculated is within HIGH tier cap)
```

**Result:**
- ✅ Conviction passes guardrail (60% is within 70-75% ceiling)
- Recommendation: SPECULATIVE BUY at 60% confidence
- Position: 2-3% (MEDIUM confidence sizing, conservative for event)
- Disclosure: "Thesis depends on multiple expansion post-deal; macro-dependent. Execution risk (deal + RemainCo guidance) is manageable; macro risk is primary unknown."

---

### 5.2.10 Case Study 2: Using Guardrails - STRL (Execution-Focused)

**Thesis:** Lithium production ramp + margin expansion post-tariff normalization

**Assumptions Identified:**
1. Production ramp hits targets (Execution)
2. Margins sustain post-tariff (Durability)
3. Lithium cycle remains intact (Macro)

**Validation Assessment:**
- Assumption 1: VALIDATED (third-party engineering confirmed)
- Assumption 2: PARTIALLY-VALIDATED (guidance given, peer recovery confirmed but company-specific execution risk)
- Assumption 3: UNVALIDATED (market-dependent, cannot be proven, scenario-tested only)

**Conviction Calculation:**
```
Baseline: 71% (production ramp proven, margin trend positive)
Assumption 1 (Execution, VALIDATED): 0 points
Assumption 2 (Durability, PARTIALLY): -0 points (company has track record, risk modest)
Assumption 3 (Macro, UNVALIDATED): -12 points (macro cycle unproven)
Data freshness: 0 points (all data <7 days)
Calculated: 71% - 0 - 0 - 12 - 0 = 59%

Unvalidated count: 1 (Assumption 3)
Conviction-cap tier: 70-75% (HIGH tier)
Final Conviction: 59% (calculated within HIGH tier cap)
```

**Result:**
- ✅ Conviction passes guardrail (59% is within 70-75% ceiling)
- Recommendation: BUY at 59% confidence (HIGH tier, but conservative on macro)
- Position: 3-4% (HIGH tier sizing for execution strength)
- Disclosure: "Thesis depends on lithium cycle remaining intact through 2026. Execution risk is low (third-party validated); macro risk is manageable via diversification."

---

## SECTION 5.3: QUALITY CONTROL GATE - GUARDRAILS SECTION

**All analyses must pass guardrail validation in QA Gate (Section 5, Part 2):**

**Part 2: Assumption Disclosure Complete**
- ☐ All key assumptions identified (minimum 3, max 5)
- ☐ Each assumption typed: Binary / Durability / Macro / Execution
- ☐ Validation status documented: VALIDATED / PARTIALLY / UNVALIDATED
- ☐ **Conviction penalties calculated per assumption type** ← NEW
- ☐ **Conviction-cap tier justified by unvalidated count** ← NEW
- ☐ **Final conviction % shown with calculation steps** ← NEW
- ☐ **Final conviction respects tier ceiling (no violations)** ← NEW

---

## INTEGRATION SUMMARY

**v8.0.4 Changes:**
- Added Section 5.2: Guardrails & Conviction-Cap Tier System
- Integrated conviction-cap tier enforcement into TURN 4 gate check
- Updated QA Engineer validation rules to check guardrail compliance
- Connected Master Architect override process for guardrail exceptions
- Added two case studies showing guardrail application

**Files Updated (Integrated):**
- Stock-Analyst.md v8.0.4 (TURN 4 guardrail gate check)
- Quality-Assurance-Engineer.md v8.0.3 (guardrail validation rules added)
- Analyst-Technical-Specification.md v8.0.4 (this file - Section 5.2 NEW)

**Reference Authority:**
- QUALITY-Guardrails.md v1.4.7 (master guardrails framework)

---

**Analyst-Technical-Specification.md v8.0.4-UPDATED - COMPLETE**

Guardrails framework fully integrated into v8.0.0 specification.

