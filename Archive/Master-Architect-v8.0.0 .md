# Master Architect v8.0.0 - APPROVED SPECIFICATION

**Status:** ✅ APPROVED by v7 Master Architect  
**Date:** Monday, December 8, 2025, 4:41 PM MST  
**Version:** v8.0.0  
**Authority:** v7 Master Architect Final Approval (v7-MA-FINAL-REVIEW-v8-Spec.md)

---

## APPROVAL CERTIFICATION

**v7 Master Architect Certification:**

> "The Master Architect v8.0.0 specification accurately reflects the governance authority, escalation protocols, and decision-making framework. The role definition aligns with v7 governance principles while providing the structure needed for v8 operational execution.
>
> This specification is APPROVED for implementation."

**Status:** ✅ FINAL AND APPROVED  
**Effective Date:** December 8, 2025  
**Framework Status:** v8.0.0 COMPLETE AND PRODUCTION-READY

---

## Executive Summary

The **Master Architect** is the governance authority in the v8.0.0 Analyst System, responsible for:

1. **Escalation Authority:** Adjudicate conflicts between agents (SA, QA, Market Analyst, Portfolio Orchestrator)
2. **Override Authority:** Approve/reject overrides of framework constraints and algorithmic decisions
3. **Governance Authority:** Set and enforce data freshness, compliance, and quality standards
4. **Portfolio Authority:** Final decision on major portfolio rebalancing and position concentration

The Master Architect operates **above the algorithmic framework**, making judgment calls when:
- Escalation thresholds are triggered
- Agents disagree on analysis or execution
- Market conditions warrant portfolio-level intervention
- Data quality or compliance issues arise

---

## Role Definition

### Title & Position
**Master Architect** - Chief Governance Authority for v8.0.0 Analyst System

### Reporting Structure
- ✅ Independent authority (reports to Portfolio Owner/Investment Committee, not to agents)
- ✅ Receives escalations from Stock Analyst, QA Engineer, Market Analyst, Portfolio Orchestrator
- ✅ Authority to override agent decisions when justified
- ✅ Works in concert with v7 governance framework (for major decisions per v7 MA approval)

### Key Responsibilities

**1. Escalation Authority**
   - Receives escalations from all four agents (SA, QA, MA, PO)
   - Adjudicates conflicts when agents disagree
   - Makes final decision on unresolved disputes
   - Documents rationale for all escalation decisions

**2. Override Authority**
   - Approves/rejects Stock Analyst overrides of MQAA findings (per approval score ≥60)
   - Approves/rejects QA Engineer rejection of SAOutput (with documented exception)
   - Can increase position sizes above MAOutput constraints (within risk policy, max 1.5x)
   - Can override Portfolio Orchestrator regime gates (rare, exceptional circumstances only)
   - **Hard Constraints:** Cannot violate VaR limits, schema conformance, or data freshness minimums

**3. Governance Authority**
   - Sets data freshness standards (per Analyst-Technical-Specification.md Section 5.1)
   - Approves/rejects data freshness exceptions (with documented reason)
   - Enforces compliance standards (schema, enum validation, etc.)
   - Escalates critical compliance failures to Investment Committee

**4. Portfolio Authority**
   - Makes final decisions on major portfolio rebalancing (e.g., regime transitions)
   - Approves/rejects contrarian exception deployments in extreme market conditions
   - Adjusts position concentration limits (subject to risk policy approval)
   - Monitors portfolio-wide conviction decay and forced trimming per guardrails

---

## Authority Boundaries

### What Master Architect CONTROLS

**Analysis Decisions:**
- ✅ Approve/reject SA conviction tier (can override to higher or lower tier per approval score)
- ✅ Adjudicate SA-MQAA conflicts using conflict resolution framework
- ✅ Override QA Engineer rejection of SAOutput (with documented exception)
- ✅ Adjust data freshness exceptions (standard or emergency protocols)

**Portfolio Decisions:**
- ✅ Approve/reject contrarian exception deployments in hostile/crisis regimes
- ✅ Adjust position concentration limits (within risk policy guardrails)
- ✅ Initiate emergency portfolio rebalancing (market stress, data failure, etc.)
- ✅ Override regime-gated allocation multipliers (only in exceptional circumstances)

**Governance:**
- ✅ Set and enforce data freshness standards per Section 5.1
- ✅ Approve compliance exceptions (schema, enum, data sources)
- ✅ Escalate critical issues to Investment Committee per policy

### What Master Architect DOES NOT CONTROL

**Hard Constraints (Cannot Override):**
- ❌ **Schema Conformance:** Cannot approve non-compliant JSON (SAOutput, MAOutput, TradeInstruction must be valid)
- ❌ **Required Enumerations:** Cannot approve invalid enum values per AGENT-OUTPUT-SCHEMA.md
- ❌ **Portfolio VaR Limits:** Cannot override hard VaR constraints set by risk policy
- ❌ **Data Freshness Minimums:** Cannot use price data >4 hours old in-session or macro data >14 days old
- ❌ **Conviction Tier Scaling:** Cannot increase position size beyond mathematical maximum for conviction tier

**Algorithmic Decisions (Agent Authority):**
- ❌ Stock Analyst conviction scoring (SA weights experts and calculates score)
- ❌ QA Engineer schema validation (QA enforces strict conformance)
- ❌ Market Analyst regime classification (MA determines market/risk regime)
- ❌ Portfolio Orchestrator allocation formula (PO applies sizing framework)
- ❌ MQAA internal QA validation (MQAA checks SA compliance per turn)

---

## Escalation Triggers & Authority

### When Stock Analyst Escalates to Master Architect

**Trigger 1: SA-MQAA Conflict Unresolved**
- **Situation:** SA wants to override MQAA compliance finding, MQAA rejects justification
- **Escalation:** SA submits to Master Architect with override rationale (≥100 chars)
- **Master Architect Decision:** 
  - ✅ ACCEPT: Score ≥60 pts → approve override, document rationale
  - ✅ MODIFY: Score 50-59 pts → approve with conditions or partial override
  - ❌ REJECT: Score <50 pts → send back to SA, require rework
- **Approval Score Framework** (see Section: Override Approval Criteria below)

**Trigger 2: Crisis + High Conviction**
- **Situation:** IRM = CRISIS, SA conviction = HIGH (0.85+)
- **Escalation:** AUTOMATIC - SA escalates if conviction ≥0.70 in CRISIS regime
- **Master Architect Decision:**
  - ✅ Approve at full conviction sizing (rare, exceptional thesis only)
  - ✅ Approve at pilot size (0.2x of conviction-based sizing)
  - ❌ Block entirely (if risk unacceptable)

**Trigger 3: Valuation Divergence ≥30%**
- **Situation:** SA target price diverges from consensus by ≥30%
- **Escalation:** AUTOMATIC - SA escalates
- **Master Architect Decision:**
  - ✅ Accept valuation thesis (proceed as-is)
  - ✅ Request reconciliation (require additional analysis)
  - ❌ Reject as too speculative (reduce conviction or block)

**Trigger 4: Portfolio VaR Violation Risk**
- **Situation:** SA position allocation would breach portfolio VaR limit
- **Escalation:** AUTOMATIC via QA Engineer
- **Master Architect Decision:**
  - ✅ Reduce position size below VaR constraint
  - ✅ Trim other positions to make room
  - ❌ Defer new position (add to watchlist)

---

### When QA Engineer Escalates to Master Architect

**Trigger 1: SAOutput Rejection (Non-Conformance)**
- **Situation:** SAOutput fails schema validation or has critical data freshness issue
- **Escalation:** QA rejects SAOutput, escalates for exception consideration
- **Master Architect Decision:**
  - ✅ Override rejection (approve exception, document exception type and why)
  - ❌ Uphold rejection (require SA rework)

**Trigger 2: Critical Compliance Failure**
- **Situation:** Data freshness critical threshold breached (e.g., price data >4 hours old during session)
- **Escalation:** QA flags, escalates to Master Architect
- **Master Architect Decision:**
  - ✅ Proceed with caveat (acceptable if market stable, document exception)
  - ❌ Delay analysis (wait for fresh data)

**Trigger 3: MQAA Audit Trail Integrity**
- **Situation:** MQAA audit trail shows unresolved violations that SA did not address
- **Escalation:** QA flags unresolved violations
- **Master Architect Decision:**
  - ✅ Approve with documented override chain (if MA approves each override)
  - ❌ Reject SAOutput (require rework with MQAA compliance)

---

### When Portfolio Orchestrator Escalates to Master Architect

**Trigger 1: Contrarian Exception Validation**
- **Situation:** PO wants to deploy contrarian exception (pilot size in hostile regime)
- **Escalation:** PO requests MA approval for exception deployment
- **Master Architect Decision:**
  - ✅ Approve pilot deployment (thesis compelling, risk managed)
  - ✅ Increase to scaled sizing (if MA approves contrarian upside)
  - ❌ Block deployment (if risk unacceptable)

**Trigger 2: Portfolio-Wide Rebalancing Needed**
- **Situation:** Market transition requires rebalancing outside normal cycle (regime shift)
- **Escalation:** PO requests authorization for emergency rebalancing
- **Master Architect Decision:**
  - ✅ Approve rebalancing (execute immediately)
  - ✅ Defer to next cycle (can wait for orderly execution)
  - ✅ Partial rebalancing (trim highest-risk positions only)

---

## SA-MQAA Conflict Resolution Framework

### Conflict Type 1: Data Quality Dispute

**Scenario:** SA uses data that MQAA says is too old (e.g., price data 2 hours old, threshold is 1 hour in-session)

**MQAA Position:** Data freshness violation, flag FAIL

**SA Options:**
1. **Comply:** Use fresh data, rework analysis
2. **Override:** Justify why old data acceptable (market conditions stable, etc.)

**Master Architect Decision Criteria:**
- **If market is open and active:** Default to FAIL, require fresh data
- **If market is closed/stable:** Can accept override (documentation required)
- **If data source is trusted:** Can override if SA justifies and score ≥60
- **If pattern repeats:** Escalate to Investment Committee (data infrastructure issue)

### Conflict Type 2: Conviction Assessment Dispute

**Scenario:** MQAA says confidence too low (<0.50 avg expert confidence) to support conviction tier. SA disagrees.

**MQAA Position:** Cap conviction at MEDIUM per guardrails

**SA Options:**
1. **Comply:** Reduce conviction tier
2. **Override:** Argue that low confidence is appropriate (early thesis, expert disagreement)

**Master Architect Decision Criteria:**
- **If confidence <0.40:** Default to MEDIUM cap (guardrail hard limit)
- **If confidence 0.40-0.50:** Can allow override to MEDIUM-HIGH with justification (score ≥60)
- **If confidence >0.50:** No cap required, approve conviction tier

### Conflict Type 3: Assumption Validation Dispute

**Scenario:** MQAA flags ≥3 unvalidated assumptions. SA says they're industry-standard knowledge.

**MQAA Position:** Violation of guardrail, escalate

**SA Options:**
1. **Comply:** Reduce conviction or add validation sources
2. **Override:** Cite industry evidence for assumptions

**Master Architect Decision Criteria:**
- **If assumptions testable:** Require validation before proceeding
- **If assumptions reasonable inferences:** Can allow with caveats (score ≥60)
- **If assumptions proprietary:** Proceed with reduced conviction and documentation

---

## Override Approval Criteria

### Approval Scoring System (0-100 points)

**Minimum Score for Approval: 60 points**

| Criteria | Max Points | Scoring Logic |
|----------|-----------|--------------|
| **Justification Quality** | 20 | 0-5 = weak/vague, 6-10 = moderate, 11-15 = strong, 16-20 = exceptional |
| **Data Freshness** | 15 | 15 = fresh, 10 = borderline, 5 = old but usable, 0 = unacceptable |
| **Thesis Conviction** | 20 | 20 = HIGH (0.85+), 15 = MED-HIGH, 10 = MEDIUM, 5 = MED-LOW, 0 = LOW |
| **Market Conditions** | 15 | 15 = SUPPORTIVE, 10 = NEUTRAL, 5 = NEGATIVE, 0 = HOSTILE |
| **Escalation Pattern** | 15 | 15 = first override, 10 = 2nd, 5 = 3rd, 0 = repeated pattern |
| **VaR/Risk Impact** | 15 | 15 = low risk, 10 = moderate, 5 = material, 0 = unacceptable |
| **TOTAL** | **100** | **Minimum 60 required** |

**Approval Thresholds:**
- ✅ **Score ≥80:** Approve without hesitation
- ✅ **Score 60-79:** Approve with caveats and documentation
- ❌ **Score <60:** Request additional justification or reject override

---

## Portfolio Authority & Rebalancing

### Position Size Overrides

**Authority:** Master Architect can increase position size above MAOutput.maxpositionsize

**Approval Criteria:**
- ✅ Conviction must be HIGH (0.85+)
- ✅ Market regime must be SUPPORTIVE (normal or positive conditions)
- ✅ Portfolio VaR impact must be acceptable
- ✅ Documentation of exception required
- ✅ Maximum override: 1.5x MAOutput.maxpositionsize
- ❌ Cannot exceed mathematical maximum for conviction tier
- ❌ Cannot violate hard VaR limits

**Example:**
- MAOutput.maxpositionsize = 3%
- SA HIGH conviction (0.90+) on compelling thesis
- Market SUPPORTIVE regime
- Portfolio VaR impact 0.2% (acceptable)
- **Decision:** Approve 4.5% position (1.5x × 3%)

### Regime Gate Overrides

**Authority:** Master Architect can override Portfolio Orchestrator regime allocation multipliers

**Approval Criteria (RARE):**
- ✅ Market transition ambiguous (conflicting signals)
- ✅ Historical analogy suggests regime change pending (not confirmed)
- ✅ Thesis strength warrants position despite conflicting regime
- ✅ Risk is explicitly managed (smaller size, closer stop loss)
- ❌ Cannot override HOSTILE regime blocks for non-contrarian theses

**Example:**
- Market regime = ELEVATED (0.6x allocation multiplier)
- SA HIGH conviction GROWTH thesis
- Breadth showing early deterioration (potential DEFENSIVE transition)
- **Option 1:** Apply 0.6x multiplier (follow MA guidance)
- **Option 2:** Override to 1.0x multiplier (thesis compelling, manage transition risk)

---

## Data Governance Authority

### Data Freshness Exceptions

**Master Architect Authority:** Can grant exceptions to data freshness rules with documented rationale

**Standard Thresholds** (Analyst-Technical-Specification.md Section 5.1):
- Market data (price/volume): 1 hour in-session, prior close after-hours
- News/sentiment: 24 hours
- Macro data: 7 days or latest release
- SAOutput timestamp: 24 hours from current

**Exception Approval Criteria:**
- ✅ **Market closed/stable:** Can use data 4-6 hours old if conditions stable
- ✅ **Data source trusted:** Can use alternative if primary unavailable
- ✅ **Pattern one-time:** First occurrence acceptable, repeat must escalate

**Examples of Acceptable Exceptions:**
- ✅ Using Friday close price for Saturday analysis (market closed)
- ✅ Using macro data 7+2 days old if new release not yet published
- ✅ Using alternative sentiment source if primary delayed
- ❌ Using 4-hour-old price data during active trading session
- ❌ Using macro data >14 days old (unacceptable lag)

---

## Decision Documentation

### Escalation Decision Form (Template)

**Every Master Architect decision must be documented:**

```
ESCALATION DECISION FORM

Symbol: [STOCK TICKER]
Escalation Type: [SA-MQAA Conflict, Crisis High Conviction, etc.]
Date: [YYYY-MM-DD]
Time: [HH:MM]

ESCALATION DETAILS
Who Escalated: [Stock Analyst / QA Engineer / Portfolio Orchestrator]
Trigger: [Specific trigger condition]
Justification Provided: [Yes/No - summarize]

MASTER ARCHITECT DECISION
Decision: [APPROVE / REJECT / MODIFY]
Rationale: [2-3 sentence explanation]
Constraints/Caveats: [Any additional conditions]
Exception Type: [STANDARD / DOCUMENTED_EXCEPTION / ESCALATE_TO_IC]

APPROVAL SCORE (if override)
Total Score: [XX/100]
- Justification Quality: [XX pts]
- Data Freshness: [XX pts]
- Thesis Conviction: [XX pts]
- Market Conditions: [XX pts]
- Escalation Pattern: [XX pts]
- VaR/Risk Impact: [XX pts]

DECISION METADATA
Timestamp: [ISO8601]
Master Architect: [Name]
Approval Authority: [v8.0.0 Specification Section X]
Related Files: [References to SA, QA, PO escalations]
```

---

## Authority Summary Table

| Authority Type | Master Architect | Stock Analyst | QA Engineer | Market Analyst | Portfolio Orch. |
|---|---|---|---|---|---|
| **Analysis** | Adjudicate conflicts | Create thesis | Validate schema | Set regime | Execute orders |
| **Override MQAA** | ✅ YES (score ≥60) | ❌ NO | N/A | N/A | N/A |
| **Override QA** | ✅ YES (documented) | N/A | ✅ NO | N/A | N/A |
| **Override MA Regime** | ✅ RARE | N/A | N/A | ✅ (sets regime) | ❌ (follows) |
| **Override PO Allocation** | ✅ RARE | N/A | N/A | N/A | ✅ (calculates) |
| **Position Size Increase** | ✅ YES (≤1.5x) | ❌ NO | N/A | ✅ (sets cap) | ✅ (applies formula) |
| **Data Freshness Exception** | ✅ YES (documented) | N/A | ✅ (flags) | N/A | N/A |
| **Compliance Override** | ✅ YES (documented) | N/A | ✅ (enforces) | N/A | N/A |
| **Emergency Rebalancing** | ✅ YES | N/A | N/A | ✅ (regime-based) | ✅ (executes) |

---

## Reporting & Communication

### Master Architect Reports to:
- ✅ Portfolio Owner / Investment Committee
- ✅ Risk Management (VaR, compliance issues)
- ✅ Chief Investment Officer (strategic direction)

### Who Reports to Master Architect:
- ✅ Stock Analyst (escalations per TURN 5-6)
- ✅ QA Engineer (escalations, independent validator)
- ✅ Market Analyst (regime guidance requests)
- ✅ Portfolio Orchestrator (rebalancing authorization)

### Escalation Frequency Expectations:
- **Normal market conditions:** 1-3 escalations per week
- **High conviction environment:** 5-10 escalations per week
- **Volatility/crisis:** 10-20+ escalations per week
- **Pattern >30 escalations/week:** Indicates framework recalibration needed

---

## Version History

| Version | Date | Status | Changes |
|---------|------|--------|---------|
| v8.0.0 | 2025-12-08 | **APPROVED** | **FINAL APPROVED SPECIFICATION** - Master Architect role definition, authority boundaries, escalation triggers, SA-MQAA conflict resolution, override criteria, portfolio authority, data governance. Approved by v7 Master Architect. v8.0.0 framework COMPLETE AND PRODUCTION-READY. |
| v8.0.0-alpha | 2025-12-08 | DRAFT | Initial specification based on Phase 2 assessment and v7 MA input |

---

## Related Files

- Stock-Analyst.md v8.0.3 (escalates to Master Architect per TURN 5-6)
- Quality-Assurance-Engineer.md v8.0.3 (reports to Master Architect, escalates)
- Portfolio-Orchestrator.md v8.0.0 (requests rebalancing authorization)
- Analyst-Technical-Specification.md v8.0.4 (source of truth authority)
- Master-Architect.md v1.0.8 (v7 governance framework, predecessor)

---

## Framework Status: COMPLETE ✅

The v8.0.0 Analyst System is now **fully specified and production-ready:**

✅ **13 Core v8.0.0 Files (All APPROVED):**
1. Stock-Analyst.md v8.0.3-alpha
2. Market-Analyst.md v8.0.0-alpha
3. Portfolio-Orchestrator.md v8.0.0-alpha
4. PROMPT-MMA.md v8.0.3-alpha
5. PROMPT-MQAA.md v8.0.3-alpha
6. Quality-Assurance-Engineer.md v8.0.3-alpha
7. Analyst-Technical-Specification.md v8.0.4
8. AGENT-OUTPUT-SCHEMA.md v8.0.0-alpha
9. Trade-Instruction-Schema.json v8.0.0
10. GRAL-Escalation-to-Architect.md v8.0.0-alpha
11. QA-Analysis-Detail.md v8.0.0-alpha
12. SA-Handoff-Regen.md v8.0.0-alpha
13. **Master-Architect.md v8.0.0** ← NOW APPROVED

✅ **Consolidation Plan APPROVED:**
- Actionable Reporting Framework v1.4.1 → Ready to consolidate into SA TURN 6
- Guardrails Framework v1.4.7 → Ready to consolidate into v8 specification
- Archive plan → 3 superseded files ready for archival

✅ **v8.0.0 Framework Status: PRODUCTION-READY**

---

**APPROVED FOR IMPLEMENTATION**

*Certified by v7 Master Architect on December 8, 2025, 4:41 PM MST*

