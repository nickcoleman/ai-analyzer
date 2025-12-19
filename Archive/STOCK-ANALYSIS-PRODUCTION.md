# STOCK ANALYSIS - PRODUCTION DELIVERY PROMPT
## Generate Final PROMPT_REPORT_OUTPUT_ACTIONABLE.md Report (Internal Steps Suppressed)

**Version:** 1.0 (Dec 7, 2025)  
**Framework:** Based on ANALYZE.md v1.4.3 + Stock-Analyst.md v1.4.7  
**Objective:** Execute complete institutional-grade stock analysis for [TICKER] using Stock Analysis Methodology v1.4.7, with all internal steps executed but only the final 3-4 page actionable report delivered to stakeholder.

**Methodology:** Stock-Analyst.md v1.4.7 + PROMPT_TURN6_ACTIONABLE.md v1.4.3 + PROMPT_REPORT_OUTPUT_ACTIONABLE.md v1.4.1

**Current Context:**
- Date: Saturday, December 7, 2025
- Time: [CURRENT_TIME] MST (AFTER_HOURS weekend market context)
- Ticker: [TICKER_SYMBOL] ([COMPANY_NAME])
- Analysis Type: Complete institutional stock analysis → final 3-4 page actionable report only

---

## WHAT THIS PROMPT DOES

This production prompt executes the **complete framework defined in ANALYZE.md v1.4.3** while delivering only the final actionable output:

✅ **Step 1A:** Data Freshness Validation (STARTUP-REQUIREMENTS.md v2.1)  
✅ **Steps 2-5:** Startup Requirements validation gates (data availability, framework alignment, compliance readiness, analysis readiness)  
✅ **TURN 1-5:** Full multi-agent analysis per ANALYZE.md v1.4.3 (fundamental, technical, macro, sentiment)  
✅ **Guardrail C v1.4.4:** Conviction-cap tier system (assumption counting, validation, tier assignment)  
✅ **Section 5:** Mandatory Quality Control Gate (4-part verification: dependencies, deletion, backtesting, consistency)  
✅ **Section 6:** Format Conversion to final actionable output (Stock-Analyst.md v1.4.7 mandate)  
✅ **Delivery:** Final 3-4 page report in PROMPT_REPORT_OUTPUT_ACTIONABLE.md v1.4.1 format  
✅ **Certification:** Delivery certification confirming all gates passed  

**All internal working steps are executed silently. Only final report is displayed.**

---

## INSTRUCTIONS

### 1. INTERNAL EXECUTION (Do Not Display Intermediate Work)

Execute the following steps internally, but **do NOT show working documents or intermediate reports**:

**Step 1A: Data Freshness Validation**
- Confirm operating context (AFTER_HOURS weekend, last trading day data acceptable)
- Verify data baseline and freshness per STARTUP-REQUIREMENTS.md v2.1
- Proceed silently

**Steps 2-5: Startup Requirements Validation**
- Execute all startup gates (data availability, framework alignment, compliance readiness, analysis readiness)
- Proceed silently if all gates pass
- STOP and report if any gate fails

**TURN 1-5: Multi-Agent Analysis**
- TURN 1: Fundamental, Technical, Macro, Sentiment synthesis per ANALYZE.md v1.4.3
- TURN 2: Quantitative validation and backtesting
- TURN 3: Bull/Base/Bear scenario modeling with probabilities
- TURN 4: Risk assessment, conviction-cap tier determination, Guardrail C v1.4.4 application
- TURN 5: Iterative refinement and final recommendation
- Proceed silently with all working analysis; keep internal only

**Guardrail C v1.4.4 Application (within TURN 4):**
- Identify all key assumptions in thesis
- Validate each assumption (VALIDATED / PARTIALLY-VALIDATED / UNVALIDATED)
- Count unvalidated assumptions
- Apply conviction-cap tier based on count:
  - 0-1 unvalidated → 70-75% conviction cap (HIGH tier)
  - 2-3 unvalidated → 55-60% conviction cap (SPECULATIVE tier)
  - 4+ unvalidated → Exploratory (WATCH, no BUY/SELL)
- Do NOT hide assumptions; all must be disclosed transparently
- Proceed silently

**Section 5: Quality Control Gate (Internal)**
- Execute all 4-part verification:
  1. Framework-Dependencies verification (before any file operations)
  2. Deletion-Approval gate (if any file deletion recommended)
  3. Backtesting-Claims audit (if any backtesting claims made)
  4. Self-Consistency check (all sections coherent, no contradictions)
- STOP if any part fails; report which part failed and why
- Proceed silently if all parts pass

**Section 6: Format Conversion (Internal)**
- Convert internal 5-section TURN 6 working format to final actionable format per PROMPT_TURN6_ACTIONABLE.md v1.4.3 Section 6
- Do NOT show 5-section internal report; only deliver final product
- All conversion steps (6A-6D) executed internally, results only displayed

### 2. FINAL DELIVERY (Display Only the Final Actionable Report)

**Output:** 3-4 page report in PROMPT_REPORT_OUTPUT_ACTIONABLE.md v1.4.1 format

**Structure (Exactly as shown):**

#### **SECTION 1: EXECUTIVE SUMMARY (1 page)**
- Recommendation (BUY/HOLD/SELL) in first sentence
- Conviction % stated explicitly (from Guardrail C tier)
- Current price with date
- Bull/Base/Bear targets with probabilities and weighted expected return
- 3 thesis drivers (high-level, with assumption types noted)
- 3 key decision triggers (high-level)

#### **SECTION 2: TECHNICAL ANALYSIS (1 page)**
- Current market regime (UPTREND/DOWNTREND/RANGE/VOLATILE)
- 3 support levels (specific prices)
- 3 resistance levels (specific prices)
- Entry trigger (specific price range, not vague)
- Initial stop loss (specific price, hard stop, no exceptions)
- Profit targets (specific prices, 1-2 targets)
- Risk/reward ratio

#### **SECTION 3: FUNDAMENTAL CASE (1 page)**
- Top 3 investment thesis drivers (quantified facts, assumption types per Guardrail C)
- Business quality score (XX/100)
- 3+ near-term catalysts with CEV-verified dates
- 3+ key risks with probability and impact
- Valuation roadmap (current vs. Bull/Base/Bear)

#### **SECTION 4: POSITION MANAGEMENT (½-1 page)**
- Current position and sizing recommendation (specific % from conviction tier allocation)
- Entry strategy (phases and specific prices)
- Profit-taking targets (scale-out strategy)
- Hard stop loss (specific price)
- 3-5 decision triggers (specific, actionable)

### 3. MANDATORY VERIFICATION CHECKLIST (Execute Silently, Display Only Results)

Before delivering, internally verify ALL items below. If ANY item fails, STOP and report failure instead of delivering report.

**CRITICAL HARD STOPS:**
- ☐ Step 1A data freshness validation: PASS
- ☐ Steps 2-5 startup requirements: ALL PASS
- ☐ TURN 1-5 analysis: COMPLETE
- ☐ Guardrail C v1.4.4 assumption identification and tier application: COMPLETE
- ☐ Section 5 Quality Control Gate: ALL 4 PARTS PASS
- ☐ Section 6 format conversion: COMPLETE
- ☐ Final report pages 1-4: VERIFY EACH
  - ☐ Executive Summary exactly 1 page
  - ☐ Technical Analysis exactly 1 page
  - ☐ Fundamental Case exactly 1 page
  - ☐ Position Management ½-1 page
  - ☐ Total primary: 3-4 pages
- ☐ All prices specific (no vague language like "near support")
- ☐ All position sizes specific percentages (no vague language like "moderate")
- ☐ All decision triggers actionable (user can execute immediately)
- ☐ Recommendation visible page 1, first sentence
- ☐ Conviction % explicitly stated (from Guardrail C tier calculation)
- ☐ Assumption types noted in fundamental drivers
- ☐ CEV verification noted for all catalysts

**If ANY item fails, STOP delivery and report:**
```
⚠️ DELIVERY HALTED
Failed Verification: [SPECIFIC ITEM]
Reason: [Why it failed]
Remediation Required: [What needs to be fixed]
```

### 4. DELIVERY CERTIFICATION (Display at End of Report)

Include this certification block at the end of the final report:

```
═════════════════════════════════════════════════════════════════════════════════
DELIVERY CERTIFICATION
═════════════════════════════════════════════════════════════════════════════════

Primary Report Format: PROMPT_REPORT_OUTPUT_ACTIONABLE.md v1.4.1
Conversion Completed: [DATE/TIME, Dec 7, 2025]
Quality Control Status: ✓ Section 5 (ALL 4 PARTS PASS)
Data Freshness: Last trading day (Friday Dec 6, 2025 close)
Analysis Context: AFTER_HOURS (Saturday [TIME] MST)
Framework: ANALYZE.md v1.4.3 with Guardrail C v1.4.4

Methodology Compliance:
✓ Step 1A: Data Freshness Validation – COMPLETE
✓ Steps 2-5: Startup Requirements – ALL PASS
✓ TURN 1-5: Multi-agent analysis – COMPLETE
✓ Guardrail C v1.4.4: Assumption identification & conviction tier – APPLIED
✓ Section 5: Quality Control Gate – ALL 4 PARTS PASS
✓ Section 6: Format Conversion – COMPLETE

Section Verification:
✓ Section 1: Executive Summary – 1 page, recommendation on page 1
✓ Section 2: Technical Analysis – 1 page, entry/stop/targets specific
✓ Section 3: Fundamental Case – 1 page, 3 drivers + catalysts + risks
✓ Section 4: Position Management – ½-1 page, allocation specific

Primary Report Total Length: [X] pages (3-4 required) ✓
All Prices Specific: ✓ YES
All Triggers Actionable: ✓ YES
User Can Decide in 5 Minutes: ✓ YES

═════════════════════════════════════════════════════════════════════════════════
Ready for stakeholder delivery.
═════════════════════════════════════════════════════════════════════════════════
```

---

## EXECUTION SUMMARY (Display Only This if Delivery Halted)

If analysis completes successfully, DO NOT DISPLAY THIS SECTION — deliver the 4-page report directly.

If analysis fails any verification, display:

```
⚠️ ANALYSIS INCOMPLETE — DELIVERY HALTED

Step 1A Data Freshness: [PASS/FAIL]
Steps 2-5 Startup: [PASS/FAIL - list which step failed]
TURN 1-5 Execution: [PASS/FAIL]
Guardrail C Application: [PASS/FAIL]
Section 5 QC Gate: [PASS/FAIL - list which part failed]
Section 6 Conversion: [PASS/FAIL]
Final Format Verification: [PASS/FAIL - list which item failed]

Reason for Halt: [SPECIFIC REASON]
Remediation Required: [WHAT NEEDS TO BE FIXED]
Next Steps: [WHAT TO DO TO RESTART ANALYSIS]
```

---

## KEY RULES (Critical for Production Use)

1. **No intermediate reports.** Do NOT show internal 5-section TURN 6 report, Section 5 QC Gate details, or Section 6 conversion steps. Only show the final 3-4 page actionable report.

2. **Hard stops are mandatory.** If ANY verification item fails, STOP immediately and report failure before attempting to deliver. Do not attempt to "fix" and redelivery without explicit user request.

3. **All prices must be specific.** No vague language ("near support", "around resistance", "strong level"). All prices specific (e.g., "$145-150", not "near resistance").

4. **All triggers must be actionable.** User should recognize decision points immediately (e.g., "Buy $145-150", "Sell $160-170", "Stop at $130").

5. **5-minute rule.** Final report must allow stakeholder to read Section 1, understand recommendation + conviction + targets, and make decision in 5 minutes.

6. **Conviction tier enforcement.** Conviction % must be within cap determined by unvalidated assumption count per Guardrail C v1.4.4:
   - 0-1 unvalidated → conviction ≤ 70-75% (HIGH tier)
   - 2-3 unvalidated → conviction ≤ 55-60% (SPECULATIVE tier)
   - 4+ unvalidated → conviction exploratory (WATCH, no BUY/SELL)
   - Position sizing must match conviction tier per ANALYZE.md

7. **CEV verification for catalysts.** All catalysts in thesis must be CEV-verified (live date verification per Master-Architect.md). If catalyst date cannot be verified with confidence, label as "Estimated" or downgrade conviction.

8. **Assumption transparency.** All assumptions must be disclosed honestly in fundamental drivers section. Do NOT hide assumptions to avoid conviction penalty. Use ANALYZE.md assumption type taxonomy (Binary, Durability, Macro, Execution).

9. **No example-as-rules.** Do not hardcode ticker-specific assumptions or static lists. All analysis must be principle-based and applicable to any equity.

10. **Guardrail C compliance.** Assumption identification, validation status, and conviction-cap tier determination must be transparent and documented in Section 3 (fundamental drivers) and implied in conviction % shown in Section 1.

---

## USAGE EXAMPLE

**Template for requesting stock analysis:**

```
Assume Stock Analyst persona.

Execute STOCK-ANALYSIS-PRODUCTION.md for [TICKER]:
- [TICKER_SYMBOL]: [COMPANY_NAME]
- Current price: $[PRICE] (as of Friday, Dec 6, 2025 close)

Deliver only the final 3-4 page actionable report.
If any verification fails, halt and report which step failed.

Execute now.
```

**Real example:**

```
Assume Stock Analyst persona.

Execute STOCK-ANALYSIS-PRODUCTION.md for INCY:
- INCY: Incyte Corp
- Current price: $102.80 (as of Friday, Dec 6, 2025 close)

Deliver only the final 3-4 page actionable report.
If any verification fails, halt and report which step failed.

Execute now.
```

---

## RELATIONSHIP TO ANALYZE.md v1.4.3

This prompt **implements** the complete framework defined in ANALYZE.md v1.4.3:

| Framework Component | Implemented By |
|---|---|
| 6-TURN methodology | TURN 1-5 execution (Steps silently) |
| Guardrail C v1.4.4 | Within TURN 4 (Step silently, display conviction tier) |
| Assumption taxonomy | Identified in fundamental drivers, type notation in Section 3 |
| Conviction-cap tiers | Determined by unvalidated assumption count, conviction % reflected in Section 1 |
| Quality Control Gate | Section 5 (executed silently, certified in delivery certification) |
| Format Conversion | Section 6 (executed silently, final report displayed) |
| Final delivery format | PROMPT_REPORT_OUTPUT_ACTIONABLE.md v1.4.1 (3-4 pages) |

**For learning the framework in detail, refer to ANALYZE.md v1.4.3.**  
**For executing analyses, use this production prompt.**

---

## READY TO EXECUTE

Proceed with analysis for **[TICKER]**.

Execute all steps silently. Deliver only the final 3-4 page actionable report (or halt and report failure if any verification fails).

**Begin analysis now.**
