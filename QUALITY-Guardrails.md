# QUALITY-GUARDRAILS v8.1.0
## Evidence-Based Penalties & Conviction Ranges (Phase 2 Enhancement)

**Version:** v8.1.0 (Phase 2)  
**Previous:** v1.4.7  
**Status:** Production Ready  
**Date:** December 13, 2025, 5:53 PM MST  
**Framework:** Stock-Analyst v8.13  
**Phase:** Phase 2 - Evidence-Based Penalties + Conviction Ranges  
**Changes:** Evidence-based formula replaces fixed penalties; conviction ranges added; tier ceilings softened (guidance vs. hard cap distinction)

---

## PHASE 2 CHANGES SUMMARY (v1.4.7 → v8.1.0)

### Major Enhancements
1. **Evidence-Based Penalties:** Fixed penalties replaced with `Penalty = (1 - analyst_confidence) × base_penalty`
2. **Conviction Ranges:** Point estimates become ranges (±5–10 pts typical)
3. **Softened Tier Ceilings:** Guidance vs. hard cap distinction; analyst can override guidance with evidence
4. **Scenario Testing Status:** Tiers for macro/durability validation reduce base penalties
5. **Conviction Sensitivity:** Analysts show conviction scenarios (what-if assumptions validated/fail)
6. **Quality Metadata Integration:** Evidence fields added for transparency

### Framework Invariants Maintained
- ✓ Guardrails A–D structure (all four guardrails enhanced, not replaced)
- ✓ 4-type assumption taxonomy (Binary/Durability/Macro/Execution)
- ✓ Conviction-cap tier system (now with soft caps and override paths)
- ✓ Position sizing linked to conviction (maintained)
- ✓ Data freshness governance (maintained)

---

## GUARDRAIL A: CONVICTION ASSESSMENT WITH EVIDENCE-BASED PENALTIES

### A.1 Core Principle (Unchanged)

**Definition:** Conviction score (0-100%) represents the percentage confidence that the investment thesis will achieve its stated objective, based on validated evidence.

**Core Formula (ENHANCED):**
```
Conviction % = (Evidence Base × Validation Depth × Timeliness) - Evidence-Based Penalties
```

Where:
- **Evidence Base:** Quality and breadth of supporting data (fundamental, technical, macro)
- **Validation Depth:** How thoroughly each assumption has been tested
- **Timeliness:** Data freshness (older data = higher penalties)
- **Evidence-Based Penalties:** Calculated from analyst confidence in each assumption

### A.2 Evidence-Based Penalty Formula (NEW - Phase 2)

**Principle:** Analyst estimates confidence in outcome; system calculates penalty transparently.

**Formula:**
```
Calculated Penalty = (1 - analyst_confidence_in_outcome%) × base_penalty

Where:
- analyst_confidence_in_outcome% = Analyst's subjective confidence (0-100%, must be justified)
- base_penalty = Maximum penalty for assumption type (reference value)
```

**Base Penalties by Assumption Type:**

| Type | Base Penalty | Rationale |
|------|-------------|-----------|
| Binary | -15 points | Either true or false; execution-critical |
| Durability | -10 points | Current favorable conditions must persist |
| Macro | -12 points | Environment assumptions (rates, commodities, credit) |
| Execution | -8 points | Company achieves operational targets |

### A.3 Assumption Documentation Template (ENHANCED)

**Required for every assumption:**

```markdown
## Assumption: [Name]

**Type:** [Binary | Durability | Macro | Execution]

**Description:** [Clear statement of what must be true for thesis to succeed]

**Evidence:** 
- [Market data / historical precedent / management track record]
- [Specific source with date]
- [Supporting metrics or examples]

**Analyst Confidence %:** [0-100%, required]

**Confidence Justification:** 
[Why this confidence level? What evidence supports it?]

**Base Penalty:** [Reference max penalty for type]

**Calculated Penalty:** (1 - [confidence%]) × [base_penalty] = [result pts]

**Validation Status:** [UNTESTED | SCENARIO-TESTED | STRESS-VALIDATED]

**Example:**
```

**CONCRETE EXAMPLE: Macro Assumption (Federal Reserve Pauses Rates)**

```markdown
## Assumption: Federal Reserve Pauses Rate Hikes in 2026

**Type:** Macro

**Description:** Fed maintains rates at current 4.25-4.50% range through 2026; does not raise above 5.0%

**Evidence:** 
- Fed Chairman Powell statements (Dec 13, 2025): "Considering rate pauses"
- Futures market pricing: 85% probability of pause by Q1 2026
- Bloomberg consensus: 2-3 potential pauses in 2026
- Historical precedent: Similar cycle in 2019 (3 rate cuts after hiking ended)

**Analyst Confidence %:** 80%

**Confidence Justification:** 
Market consensus and Fed guidance strongly suggest pause cycle. However, inflation surprise risk (5% tail case) could force resumption. Confidence reduced from 90% due to geopolitical upside inflation risks (oil shock from Middle East escalation).

**Base Penalty:** -12 points (Macro assumption type)

**Calculated Penalty:** (1 - 0.80) × 12 = -2.4 points

**Validation Status:** SCENARIO-TESTED-MILD
- Tested impact with +100 bps rates (to 5.25%): Thesis still valid, returns reduced ~8%
- Tested impact with +200 bps rates (to 6.25%): Thesis severely stressed, returns negative

**Result:** Only -2.4 points penalty (not -12) due to high analyst confidence and successful scenario testing
```

**CONCRETE EXAMPLE: Binary Assumption (M&A Deal Closes)**

```markdown
## Assumption: CCS Acquisition of Communications Corp Closes H1 2026

**Type:** Binary

**Description:** Binding merger agreement signed; deal closes with no material renegotiation or FTC block; RemainCo becomes independent public company

**Evidence:**
- Binding agreement signed: November 15, 2025 (confirmed in SEC 8-K)
- FTC review timeline: Expected completion by April 2026 (standard 6-month review)
- Deal certainty: Standard antitrust review, no unique regulatory hurdles identified
- Historical precedent: Similar CCS deals (2019, 2021) both cleared with minor divestitures

**Analyst Confidence %:** 75%

**Confidence Justification:**
Binding agreement eliminates deal risk (not speculative anymore). Standard FTC review with no unique blockers. Confidence at 75% (not 95%) because: (1) Any FTC renegotiation could delay/restructure deal (5% risk), (2) Geopolitical shifts could trigger review delays (3% risk), (3) RemainCo value still dependent on post-deal margins (execution dependent).

**Base Penalty:** -15 points (Binary assumption type)

**Calculated Penalty:** (1 - 0.75) × 15 = -3.75 points

**Validation Status:** PARTIALLY-TESTED
- Binding agreement: Execution risk eliminated
- FTC approval timing: Standard 6-month timeline, likely cleared
- RemainCo margins: Not yet validated (post-deal uncertainty)

**Result:** Only -3.75 points penalty (not -15) due to binding agreement + likely FTC clearance
```

**CONCRETE EXAMPLE: Durability Assumption (Lithium Cycle Continues)**

```markdown
## Assumption: Lithium Cycle Remains Intact Through 2027

**Type:** Durability

**Description:** Lithium demand continues to grow at ≥20% CAGR through 2027; prices remain above $10,000/MT; EV adoption accelerates or maintains current trajectory

**Evidence:**
- 2024-2025 EV penetration: 18% global average, 25% China, growing at +3 pts/year
- Lithium demand growth: Official estimates 20-25% CAGR (IEA, BloombergNEF)
- Price floor: Sustained above $12,000/MT for past 12 months (vs. $8,000 prior cycle)
- Supply/demand: Industry consensus shows undersupply 2025-2027
- Validation: Production ramps (Spodumene) from multiple miners confirmed

**Analyst Confidence %:** 80%

**Confidence Justification:**
EV adoption trajectory is locked in by regulatory mandates (EU 2035 ICE ban, China ZEV quota). Lithium supply additions are real (not speculative), already under construction. Confidence at 80% (not 95%) because: (1) Economic recession could stall EV sales (15% tail risk), (2) Battery tech shifts to sodium-ion could reduce Li demand (10% risk), (3) Geopolitical supply disruptions could spike prices but compress margins (manageable).

**Base Penalty:** -10 points (Durability assumption type)

**Calculated Penalty:** (1 - 0.80) × 10 = -2 points

**Validation Status:** MULTI-CYCLE-VALIDATED
- 2024 vs. 2023 comparison: EV adoption +3 pts, validation positive
- Supply/demand balance: Confirmed via official forecasts
- Historical precedent: Lithium cycle sustained 2020-2025 (5-year validation)

**Result:** Only -2 points penalty (not -10) due to historical validation + supply confirmation
```

**CONCRETE EXAMPLE: Execution Assumption (Revenue Guidance Hit)**

```markdown
## Assumption: Company Achieves $500M Revenue in 2026

**Type:** Execution

**Description:** Company hits guided revenue of $500M+ for FY2026; represents 12% YoY growth vs. FY2025 ~$446M guidance

**Evidence:**
- Management guidance: $450-520M range issued in Q3 2025 earnings
- Recent quarterly trends: Q1-Q3 2025 tracking at 11% growth (on pace for guidance)
- Management track record: Last 3 years, achieved guided revenue 95%+ (miss only once in 2023 by -2%)
- Analyst consensus: Average FY2026 estimate $505M (vs. guided $460M midpoint)

**Analyst Confidence %:** 85%

**Confidence Justification:**
Management track record is strong and recent guidance confirmed in Q3. Company on pace to achieve guidance. Confidence at 85% (not 95%) because: (1) Market slowdown could reduce enterprise spending (10% downside risk), (2) FX headwinds (20% of revenue is exports) could reduce reported revenue (5% risk), (3) One large customer concentration (12% of revenue) could churn (3% risk).

**Base Penalty:** -8 points (Execution assumption type)

**Calculated Penalty:** (1 - 0.85) × 8 = -1.2 points

**Validation Status:** STRESS-VALIDATED
- Recent quarters: Trending at 11% growth (validates execution path)
- Guidance history: 95% hit rate on revenue guidance
- Peer comparison: In line with industry growth rates (reduces execution risk)

**Result:** Only -1.2 points penalty (not -8) due to strong track record + recent validation
```

### A.4 Assumption Validation Status Tiers (NEW - Phase 2)

**For MACRO Assumptions:**

| Status | Base Penalty | Adjustment | Use Case |
|--------|-------------|------------|----------|
| UNTESTED | -12 pts | 1.0x | No scenario validation performed |
| SCENARIO-TESTED-MILD | -8 pts | 0.67x | Tested with +100 bps rates or equivalent shock |
| SCENARIO-TESTED-SEVERE | -6 pts | 0.5x | Tested with +200 bps rates or severe shock |
| STRESS-VALIDATED | -4 pts | 0.33x | Tested across multiple scenarios, survives stress |

**For DURABILITY Assumptions:**

| Status | Base Penalty | Adjustment | Use Case |
|--------|-------------|------------|----------|
| UNTESTED | -10 pts | 1.0x | New condition, no historical data |
| ONE-CYCLE-VALIDATED | -6 pts | 0.6x | Validated through one market cycle (12+ months) |
| MULTI-CYCLE-VALIDATED | -3 pts | 0.3x | Validated across 2+ market cycles (24+ months) |
| TREND-REVERSAL-TESTED | -4 pts | 0.4x | Tested and survived trend reversal scenario |

**For BINARY & EXECUTION Assumptions:**

| Status | Base Penalty | Adjustment | Use Case |
|--------|-------------|------------|----------|
| UNTESTED | Full penalty | 1.0x | No evidence or track record |
| PARTIALLY-TESTED | 50% of penalty | 0.5x | Binding agreement or partial validation |
| VALIDATED | 0 pts | 0.0x | Definitive evidence (court ruling, executed transaction) |

### A.5 Adjusted Penalty Calculation (ENHANCED)

**Formula:**
```
Adjusted Base Penalty = Base Penalty × Validation Status Adjustment Factor
Calculated Penalty = (1 - analyst_confidence%) × Adjusted Base Penalty

Example 1: Macro assumption with UNTESTED status
  Base Penalty: -12 points
  Validation Status: UNTESTED (1.0x adjustment)
  Analyst Confidence: 70%
  Adjusted Penalty = -12 × 1.0 = -12 points
  Calculated Penalty = (1 - 0.70) × 12 = -3.6 points
  
Example 2: Macro assumption with SCENARIO-TESTED-SEVERE status
  Base Penalty: -12 points
  Validation Status: SCENARIO-TESTED-SEVERE (0.5x adjustment)
  Analyst Confidence: 85%
  Adjusted Penalty = -12 × 0.5 = -6 points
  Calculated Penalty = (1 - 0.85) × 6 = -0.9 points
  
Example 3: Durability assumption with MULTI-CYCLE-VALIDATED status
  Base Penalty: -10 points
  Validation Status: MULTI-CYCLE-VALIDATED (0.3x adjustment)
  Analyst Confidence: 80%
  Adjusted Penalty = -10 × 0.3 = -3 points
  Calculated Penalty = (1 - 0.80) × 3 = -0.6 points
```

---

## GUARDRAIL B: DATA VALIDATION (Unchanged from v1.4.7)

*All section B content from v1.4.7 maintained; no changes in Phase 2*

Framework dependencies, data source validation, and alignment verification remain as documented in v1.4.7.

---

## GUARDRAIL C: CONVICTION-CAP TIER SYSTEM (SOFTENED - Phase 2)

### C.1 The Conviction-Cap Tier Framework (Core Governance Rule - SOFTENED)

**Central Principle:** Unvalidated assumptions create a conviction **guidance range**, not a hard ceiling. Analyst can override guidance with evidence; hard cap prevents extreme overconfidence.

**Key Change:** Guidance vs. Hard Cap distinction

### C.2 Softened Conviction-Cap Tier Table (PRIMARY REFERENCE)

**Use this table to determine conviction tier and position sizing:**

| Unvalidated Count | Guidance Range | Hard Cap | Max Position | Tier Name | Analyst Override |
|------|------|------|--------|-----------|---------|
| 0–1 | 70–75 | 65–80 | 3–5% | HIGH | Evidence required if <65 or >80 |
| 2–3 | 55–60 | 50–70 | 1.5–3% | MEDIUM | Evidence required if <50 or >70 |
| 4+ | 55 | 45–65 | 0.5–1.5% | LOW | Evidence required if <45 or >65 |

**KEY DISTINCTION (NEW in Phase 2):**
- **Guidance Range:** Target conviction zone (analyst should aim here)
- **Hard Cap:** Absolute boundaries (analyst can violate guidance, but NOT hard cap)
- **Hard Cap Violation:** Requires Master-Architect escalation and approval

### C.3 TURN 5 Enforcement Logic (UPDATED - Phase 2)

**In Stock-Analyst TURN 5, apply this logic:**

```
FOR each analysis:
  conviction_final = [calculated from TURN 4]
  unvalidated_count = [count of UNVALIDATED assumptions]
  
  IF unvalidated_count == 0-1:
    guidance_min = 70
    guidance_max = 75
    hard_cap_min = 65
    hard_cap_max = 80
    tier = HIGH
    
  ELSE IF unvalidated_count == 2-3:
    guidance_min = 55
    guidance_max = 60
    hard_cap_min = 50
    hard_cap_max = 70
    tier = MEDIUM
    
  ELSE (unvalidated_count >= 4):
    guidance_min = 55
    guidance_max = 55
    hard_cap_min = 45
    hard_cap_max = 65
    tier = LOW
  
  # Check conviction against tiers
  IF conviction_final >= hard_cap_min AND conviction_final <= hard_cap_max:
    # Within hard cap
    
    IF conviction_final < guidance_min OR conviction_final > guidance_max:
      # Outside guidance, but within cap
      QA_FLAG: "Conviction [XX] outside guidance range [YY-ZZ]; evidence required"
      result: PROCEED (with flag)
    ELSE:
      # Within guidance
      result: PROCEED (no flag)
      
  ELSE IF conviction_final < hard_cap_min OR conviction_final > hard_cap_max:
    # Outside hard cap
    QA_FLAG: "Conviction [XX] exceeds hard cap [YY-ZZ]; escalate to Master-Architect"
    result: ESCALATE_TO_MA (blocking)
```

### C.4 Case Study: How Softened Tiers Changed Analysis (NEW - Phase 2)

**Example: Lithium Producer Stock**

**Old Approach (v1.4.7 - Hard Cap):**
- 1 unvalidated assumption (macro: lithium cycle)
- Conviction calculated: 72%
- Cap: 70-75% (hard ceiling)
- Result: 72% → ACCEPTED (at cap ceiling)
- Issue: Analyst could not justify 72% vs. 75%; capped artificially

**New Approach (v8.1.0 - Softened Cap):**
- 1 unvalidated assumption (macro: lithium cycle)
- Conviction calculated: 72%
- Guidance: 70-75%, Hard Cap: 65-80%
- Result: 72% → ACCEPTED (within guidance + cap)
- Analyst shows evidence: "Lithium demand 20%+ CAGR confirmed; cycle validated 5 years; analyst confidence 85% → only -1.5 pts penalty"
- Conviction: 72% (within guidance; no override needed)

**Key Learning:** Softened tiers allow honest conviction scoring without artificial constraints.

---

## GUARDRAIL D: DATA FRESHNESS & CEV (Unchanged from v1.4.7)

*All section D content from v1.4.7 maintained; no changes in Phase 2*

Critical Event Verification (CEV) protocol, data freshness standards, and governance remain unchanged.

---

## GUARDRAIL E: FRAMEWORK COMPLIANCE (UPDATED - Phase 2)

### E.1 Evidence-Based Documentation Requirement (NEW)

**In addition to existing E.1 standards, all analyses must now include:**

```markdown
## EVIDENCE-BASED PENALTY TRANSPARENCY

**Assumption Confidence Levels:**

| Assumption | Type | Confidence % | Justification | Calculated Penalty |
|-----------|------|-------------|----------------|-------------------|
| [Name 1] | [Type] | [X]% | [Brief evidence] | [Calculated] pts |
| [Name 2] | [Type] | [Y]% | [Brief evidence] | [Calculated] pts |
| [Name 3] | [Type] | [Z]% | [Brief evidence] | [Calculated] pts |

**Total Evidence-Based Penalties:** [Sum] pts

**Conviction Calculation Transparency:**
- Base Conviction: XX%
- Evidence-Based Penalties: YY pts
- Conviction Range: [Min–Max]
- Final Point Estimate: ZZ%
- Conviction Tier: [HIGH | MEDIUM | LOW]
```

### E.2 Non-Negotiable Standards (UPDATED - Phase 2)

| Standard | Requirement | Violation = |
|----------|-----------|---------|
| Evidence-Based Penalties | All assumptions must show confidence % + justification | FAIL |
| Confidence Ranges | Conviction must include range (±5–10 pts typical) | FAIL |
| Validation Status | Macro/Durability assumptions must show scenario validation status | FAIL |
| Tier Ceiling Compliance | Final conviction within hard cap (or escalated to MA) | FAIL |
| Scenario Sensitivity | Conviction ranges show: if-assumption-validated, if-assumption-fails | FAIL |

---

## CONVICTION RANGE GENERATION (NEW - Phase 2)

### Overview

Instead of point estimate `conviction = 72`, generate confidence range: `[68–76]`

### Formula

```
conviction_range_min = final_conviction - max(assumption_risk_points)
conviction_range_max = final_conviction + min(upside_adjustment, 10)

Where:
- assumption_risk_points = sum of downside if each assumption fails
- upside_adjustment = additional upside if assumptions exceed expectations
```

### Concrete Example

```
Analysis: Apple Inc. (AAPL)
Base Conviction: 75%

Assumptions & Risk:
1. iPhone 16 sales +12% (Durability, 85% confidence)
   - If fails: -4 pts
2. Services growth 18% (Execution, 80% confidence)
   - If fails: -3 pts
3. Margin expansion to 48% (Durability, 75% confidence)
   - If fails: -5 pts

Downside Risk Pool: 4 + 3 + 5 = 12 pts maximum
Upside Scenario: If all 3 beat consensus, +8 pts additional

Calculated Penalties: -2.8 pts (sum of evidence-based)

Final Point Estimate: 75 - 2.8 = 72.2%

Range Calculation:
- Min: 72.2 - 8 (all assumptions fail) = 64.2%
- Max: 72.2 + 5 (assumptions validated + upside) = 77.2%

Conviction Range: [64–77] with point estimate 72%
```

---

## QUALITY METADATA UPDATES (NEW - Phase 2)

**Extended conviction_calculation object in AGENT-OUTPUT-SCHEMA.md v8.0.5:**

```json
{
  "conviction_calculation": {
    "base": 75,
    "penalties_applied": -2.8,
    "adjustments_applied": 8,
    "final_point_estimate": 75.2,
    "range": {
      "min": 65,
      "max": 78,
      "range_rationale": "5.5 pts from macro assumption risk + execution uncertainty"
    },
    "assumption_evidence": [
      {
        "assumption": "iPhone 16 sales +12% YoY",
        "type": "Durability",
        "analyst_confidence_pct": 85,
        "validation_status": "SCENARIO-TESTED-MILD",
        "base_penalty": -10,
        "adjusted_penalty": -6,
        "calculated_penalty": -0.9,
        "justification": "Recent quarters show acceleration; peer data confirms market strength"
      }
    ],
    "sensitivity_scenarios": {
      "if_iphone_sales_beat": 78,
      "if_iphone_sales_miss": 64,
      "if_services_accelerate": 75,
      "if_margin_expansion_fails": 70
    },
    "final": 75.2,
    "range": [65, 78],
    "tier": "HIGH",
    "transparency": "75 - 2.8 (penalties) = 72.2 base, range ±6 pts = [66–78]"
  }
}
```

---

## COMPLIANCE CHECKLIST (UPDATED - Phase 2)

**Required before ANY analysis delivery:**

**GUARDRAIL A - Conviction Assessment (Evidence-Based):**
- ☐ All assumptions identified, typed, and confidence % provided
- ☐ Confidence % justified with specific evidence
- ☐ Base penalties assigned per assumption type
- ☐ Validation status assigned (UNTESTED / SCENARIO-TESTED / VALIDATED)
- ☐ Adjusted penalties calculated: (1 - confidence) × adjusted_base
- ☐ Conviction range generated (±5–10 pts typical)
- ☐ Conviction documented in first sentence

**GUARDRAIL C - Conviction-Cap Tier System (Softened):**
- ☐ Unvalidated assumption count documented
- ☐ Guidance range identified from table
- ☐ Hard cap identified from table
- ☐ Final conviction within hard cap (or escalated to MA)
- ☐ If within guidance: No override needed
- ☐ If outside guidance but within hard cap: Flag with evidence
- ☐ Recommendation tier assigned (HIGH/MEDIUM/LOW)
- ☐ Position size derived from tier table

**GUARDRAIL E - Framework Compliance (Enhanced):**
- ☐ Evidence-Based Penalty Transparency section completed
- ☐ Assumption confidence table shows % + justification + calculated penalty
- ☐ Conviction range with sensitivity scenarios shown
- ☐ All 4 E.2 standards met (evidence-based, ranges, validation, compliance)

---

## VERSION HISTORY

| Version | Date | Summary | Status |
|---------|------|---------|--------|
| v1.4.7 | Dec 7, 2025 | Original framework (Guardrails A–D complete) | Superseded |
| **v8.1.0** | **Dec 13, 2025** | **Phase 2: Evidence-based penalties, conviction ranges, softened tier ceilings** | **Active** |

---

**END OF QUALITY-GUARDRAILS v8.1.0**

**Status:** Production Ready - All Guardrails A–E Complete  
**Last Updated:** December 13, 2025, 5:53 PM MST  
**Phase Authority:** Master-Architect (Phase 2 Execution)  
**Approval:** Phase 2 DIRECTIVE AUTHORIZED  
**Next Update:** Phase 3 (Stock-Analyst v8.13 updates for conviction ranges)