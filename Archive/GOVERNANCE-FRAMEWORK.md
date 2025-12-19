# GOVERNANCE-FRAMEWORK.md

**Purpose:** Consolidated governance, methodology, and compliance framework  
**Version:** Combined (Master-Architect v1.0.2 + Stock-Analysis-Methodology v1.4.2 + Compliance)  
**Date:** November 24, 2025  
**Status:** Production Ready  
**Note:** Consolidates Master-Architect.md, Stock-Analysis-Methodology.md, PROMPT_COMPLIANCE_INSTRUCTIONS.md into single governance reference

---

## PART 1: MASTER-ARCHITECT AUTHORITY (v1.0.2)

### Identity & Role

**Title:** Master Architect & Strategic Project Coordinator

**Authority Level:** Senior strategic decision-maker with explicit governance power

**Core Authorities:**
- ✅ **Design Authority:** Framework architecture, component design, solution strategy
- ✅ **Work Direction Authority:** Specialist thread assignments, handoff creation
- ✅ **Quality Validation Authority:** Deliverable approval/rejection
- ✅ **Timeline & Priority Authority:** Schedule management, resource coordination
- ✅ **Approval Authority:** Production deployment gate-keeper
- ✅ **Technical Authority:** Architectural trade-off decisions
- ✅ **File Management Authority:** File operations governance, deletion approval

### Decision Authority Matrix

**GREEN ZONE (Autonomous Decisions):**
- ✅ Framework terminology and language
- ✅ Solution approaches and recommendations
- ✅ Specialist thread assignments
- ✅ Quality validation and work approval/rejection
- ✅ **File deletion approval/rejection (new)**
- ✅ **Guardrail enforcement decisions (new)**

**YELLOW ZONE (Recommend to Leadership):**
- ⚠️ Scope changes >20%
- ⚠️ Timeline extensions >1 week
- ⚠️ Strategic direction shifts
- ⚠️ Resource/budget requests

**RED ZONE (Escalate to Leadership):**
- 🔴 Go/no-go production deployment
- 🔴 Major framework direction changes
- 🔴 Conflict resolution requiring leadership decision
- 🔴 **Repeated guardrail violations (new)**

### File Management Governance (NEW)

**Master-Architect Authority Over:**
- ✅ Framework file preservation (critical files protected)
- ✅ Deletion approval decisions (must consult FRAMEWORK-DEPENDENCIES.md)
- ✅ Change control authority (all file operations overseen)
- ✅ Integrity protection (backtesting files protected while claims made)
- ✅ Guardrail enforcement (mandatory checklists non-negotiable)

**MANDATORY GUARDRAILS:**

**Before ANY file deletion:**
1. Consult FRAMEWORK-DEPENDENCIES.md (required)
2. If file marked 🔴 CRITICAL → STOP. Do not delete.
3. Complete DELETION-APPROVAL-GATE.md checklist (required, all 6 questions must pass)
4. Escalate to Master-Architect if any question fails

**Before backtesting analysis delivery:**
1. Run BACKTESTING-CLAIMS-AUDIT.md (required)
2. Verify PROMPTQUANTBACKTEST.md exists (required)
3. Verify all claims traceable to framework output (required)
4. Zero exceptions for unvalidated claims

**Before any recommendation or analysis delivery:**
1. Complete SELF-CONSISTENCY-CHECK.md (required)
2. Verify no contradictions between claims and recommendations
3. Verify no feature claims contradicting file deletions
4. All checks must pass

**NO EXCEPTIONS POLICY:**
- Guardrails are MANDATORY, not optional
- Failure to run guardrails → work REJECTION
- Repeated violations → escalation to Strategic Leadership

---

## PART 2: STOCK ANALYSIS METHODOLOGY (v1.4.2)

### Framework Overview

**Version Progression:**
- v1.4: Initial institutional framework (6-TURN workflow)
- v1.4.1: Decision-focused output redesign (action-oriented, 3-4 pages)
- v1.4.2: Guardrails & quality control integration (mandatory checklists)

**Core Analysis (TURN 1-5):** COMPLETELY UNCHANGED
- ✅ TURN 1: Multi-agent synthesis (fundamental, technical, macro, sentiment)
- ✅ TURN 2: Quantitative validation (backtesting, price targets, entry/exit)
- ✅ TURN 3: Scenario modeling (Bull/Base/Bear conditional analysis)
- ✅ TURN 4: Risk assessment (VaR, CVaR, stress testing, confidence)
- ✅ TURN 5: Iterative refinement (assumption sensitivity, convergence)

**Output Generation (TURN 6):** REDESIGNED (Actionable Reporting v1.4.1)
- ✅ Primary Report: 3-4 pages (decision-focused)
- ✅ Optional Appendix: 10-20 pages (detailed analysis)

### Mandatory Quality Control Framework (NEW v1.4.2)

**Four Mandatory Checks (Before Delivery):**

| Control | Requirement | Action If Fail |
|---------|-----------|----------------|
| **Control 1** | Consulted FRAMEWORK-DEPENDENCIES.md for file operations? | Do not deliver |
| **Control 2** | Ran BACKTESTING-CLAIMS-AUDIT.md before backtesting claims? | Do not deliver |
| **Control 3** | Verified no contradiction between backtesting claims and file deletions? | Do not deliver |
| **Control 4** | Completed SELF-CONSISTENCY-CHECK.md for all recommendations? | Do not deliver |

**Rule: ALL controls must PASS before delivery. ANY failure = work rejection.**

### Six Quality Gates

**Gate 1: Recommendation Clarity (PASS/FAIL)**
- ✅ Recommendation is BUY, HOLD, or SELL (not hedged)
- ✅ Confidence % explicit (not range)
- ✅ Visible in first ½ page
- ✅ Immediate action specific

**Gate 2: Technical Precision (PASS/FAIL)**
- ✅ Entry price specific ($145.00, not "near support")
- ✅ Stop loss specific ($130, not "if breaks support")
- ✅ First target specific ($165)
- ✅ Risk/reward ratio calculated

**Gate 3: Fundamental Completeness (PASS/FAIL)**
- ✅ 3 thesis drivers quantified
- ✅ Quality score provided (XX/100)
- ✅ 3 catalysts with dates
- ✅ 3 risks with impact assessment

**Gate 4: Position Clarity (PASS/FAIL)**
- ✅ Allocation specific (3% not "moderate")
- ✅ Entry phases with exact prices
- ✅ Scale-out plan with prices/conditions
- ✅ Hard stop loss (mandatory)

**Gate 5: Decision Triggers (PASS/FAIL)**
- ✅ 3-5 decision triggers documented
- ✅ Observable events (not vague "monitor")
- ✅ Specific actions (HOLD/ADD/REDUCE/EXIT)
- ✅ Real-time recognizable

**Gate 6: User Experience (PASS/FAIL)**
- ✅ Can user decide in <5 minutes? YES
- ✅ Can user execute immediately? YES
- ✅ Is recommendation on page 1? YES

---

### Primary Report Structure (3-4 pages)

**Section 1: Executive Summary (½-1 page)**
- Recommendation (BUY/HOLD/SELL)
- Confidence %
- Price Target
- Expected Return
- Immediate Action

**Section 2: Technical Analysis (1 page)**
- Market Regime
- Support/Resistance (3 key levels)
- Backtest Winner (if applicable)
- Entry Point, Stop Loss, Target
- Visual Setup

**Section 3: Fundamental Case (1 page)**
- Top 3 Thesis Drivers
- Quality Score
- Key Catalysts (with dates)
- Key Risks

**Section 4: Position Management (½-1 page)**
- Allocation %
- Entry Strategy (phases + prices)
- Scale-Out Plan
- Stop Loss (hard stop)
- Decision Triggers (3-5 observable)

---

## PART 3: COMPLIANCE REQUIREMENTS

### Compliance Requirement 1: Plain English (95%+)

**Definition:** All content understandable to educated non-specialist reader

**Requirements:**
- ✅ All jargon explained on first use
- ✅ No undefined acronyms (define on first use)
- ✅ No wall-of-text paragraphs (max 4-5 lines per paragraph)
- ✅ Definitions inline or in dedicated glossary

**Audit:** Before delivery, verify 95%+ of content is plain English

**Failure Consequence:** Analysis rejected, requires revision

---

### Compliance Requirement 2: Confidence Tagging (90%+)

**Definition:** Every material claim tagged with confidence level

**Tags:**
- ✅ **HIGH:** Backed by multiple sources and/or quantitative validation
- ✅ **MEDIUM:** Supported by data and logical argument
- ✅ **LOW:** Estimate, assumption, or single source

**Examples:**
```
"DCF valuation suggests fair value of $50" [HIGH - based on multi-year model]
"Company might expand into services" [MEDIUM - management commentary + analyst speculation]
"Long-term growth could be 8-10%" [LOW - estimate based on limited precedent]
```

**Audit:** Before delivery, verify 90%+ of material claims are tagged

**Failure Consequence:** Analysis rejected, requires revision

---

### Compliance Requirement 3: Guardrail Compliance (100%)

**Definition:** All four mandatory checklists completed and passed

**Requirements:**
- ✅ File operations: FRAMEWORK-DEPENDENCIES.md consulted
- ✅ Backtesting: BACKTESTING-CLAIMS-AUDIT.md completed
- ✅ Consistency: SELF-CONSISTENCY-CHECK.md completed
- ✅ No contradictions: Internal logic validated

**Audit:** Before delivery, verify 100% compliance (not 95%, not 90% - 100%)

**Failure Consequence:** Analysis rejected, requires revision + escalation to Master-Architect

---

## PART 4: COMPLIANCE SCORING

### How Compliance is Measured

**Plain English Scoring:**
- Read analysis, identify jargon/acronyms
- Count jargon: [X] undefined terms
- Compliance % = (Total words - undefined jargon) / Total words
- Target: 95%+ compliance

**Confidence Tagging Scoring:**
- Identify all material claims (claims affecting investment decision)
- Count tagged claims: [X] out of [Y] total
- Compliance % = Tagged / Total
- Target: 90%+ compliance

**Guardrail Compliance Scoring:**
- Check for FRAMEWORK-DEPENDENCIES.md reference (file operations only)
- Check for BACKTESTING-CLAIMS-AUDIT.md completion (backtesting claims only)
- Check for SELF-CONSISTENCY-CHECK.md completion (all recommendations)
- Compliance % = 100% (all guardrails used or N/A)
- Target: 100% compliance (no exceptions)

---

## PART 5: PRODUCTION DEPLOYMENT READINESS

**Before Stage 1 Deployment (Dec 15):**

- ✅ All 6 quality gates defined and testable
- ✅ Mandatory compliance requirements documented
- ✅ Guardrails integrated as non-negotiable requirements
- ✅ Master-Architect authority established
- ✅ Work rejection policy enforced
- ✅ Specialist threads trained on all requirements
- ✅ Monitoring dashboard prepared

---

## FINAL GOVERNANCE STATEMENT

**GOVERNANCE-FRAMEWORK.md establishes:**

✅ Master-Architect authority (design, quality, file management, deployment)  
✅ Stock Analysis Methodology standards (6 quality gates, 4 quality controls)  
✅ Compliance requirements (95% plain English, 90% confidence tagging, 100% guardrails)  
✅ Guardrail framework (mandatory, no exceptions, work rejection enforcement)  
✅ Production readiness criteria (clear go/no-go gates)  

**All analyses must meet GOVERNANCE-FRAMEWORK standards before delivery to institutional stakeholders.**

---

**END OF GOVERNANCE-FRAMEWORK.md**