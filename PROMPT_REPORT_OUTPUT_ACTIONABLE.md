# PROMPT_REPORT_OUTPUT_ACTIONABLE.md

**VERSION:** 1.4.1 (Decision-Focused Redesign)  
**STATUS:** Modified for Actionable Reporting Output Structure  
**PURPOSE:** Restructure report from process-centric (25-40 pages) to decision-centric (3-4 pages + optional appendix)  
**REPLACES:** Old PROMPT_REPORT_OUTPUT.md (v6.10)

---

## EXECUTIVE SUMMARY: The Output Change

### Current Process (v6.10 - What We're Replacing)

The old report structure printed all intermediate work:

```
SECTION 1: TURN 1 Synthesis Results (3-5 pages)
SECTION 2: TURN 2 Validation Results (2-3 pages)
SECTION 3: TURN 3 Scenario Modeling (2-3 pages)
SECTION 4: TURN 4 Risk Assessment (2-3 pages)
SECTION 5: TURN 5 Refinement (2-3 pages)
SECTION 6-14: Final Compiled Report (8-10 pages)

TOTAL: 21-30 pages
USER EXPERIENCE: Recommendation buried on page 20-25
TIME TO DECISION: 30-45 minutes
```

### New Structure (v1.4.1 - Decision-Focused)

The new output delivers decision immediately:

```
PRIMARY REPORT (3-4 pages):
  SECTION 1: Executive Summary (Recommendation on page 1)
  SECTION 2: Technical Analysis (1-page condensed)
  SECTION 3: Fundamental Case (1-page condensed)
  SECTION 4: Position Management (½-1 page action plan)

OPTIONAL APPENDIX (10-20 pages):
  SECTION 5-14: All TURN 1-5 detailed work
  (Available for download if user wants verification depth)

TOTAL PRIMARY: 3-4 pages | TOTAL WITH APPENDIX: 15-25 pages
USER EXPERIENCE: Recommendation on page 1, action plan on page 4
TIME TO DECISION: 3-5 minutes
```

---

## NEW REPORT OUTPUT STRUCTURE

### SECTION 1: EXECUTIVE SUMMARY (1 Page)

**TEMPLATE SOURCE:** PROMPT_EXECUTIVE_SUMMARY.md

**Content Required:**
- Recommendation (BUY/HOLD/SELL) in **first paragraph**
- Confidence % (e.g., 85%)
- Current price with date
- Three price targets: Bull/Base/Bear with probabilities
- Weighted expected return
- Specific immediate action (what user should do TODAY)
- Three investment thesis drivers (bullet points, quantified)
- Three decision triggers (when you'd exit)

**Format:**
```
═══════════════════════════════════════════════════════════════════════════════
RECOMMENDATION & CONVICTION
═══════════════════════════════════════════════════════════════════════════════

RECOMMENDATION: BUY | Confidence: 85%
Current Price: $X | Current Position: Y% | Status: ↑

[Rest of executive summary...]
```

**Page Length:** EXACTLY 1 page (not 1.5, not 0.5)
**Key Characteristic:** User can read in 1-2 minutes and understand full recommendation

---

### SECTION 2: TECHNICAL ANALYSIS (1 Page)

**TEMPLATE SOURCE:** PROMPT_ACTIONABLE_TECHNICALS.md

**Content Required:**
- Current regime (UPTREND/DOWNTREND/RANGE/VOLATILE)
- Three support levels with specific prices
- Three resistance levels with specific prices
- RSI value and interpretation
- MACD status (BULLISH/BEARISH/NEUTRAL)
- Moving average status (50-day, 200-day)
- Volume trend
- Backtest winner (which strategy worked historically, performance metrics)
- Specific entry price with condition
- Specific stop loss price
- First and second profit targets with conditions
- Risk/reward ratio and probability-weighted expected value

**Format:**
```
═══════════════════════════════════════════════════════════════════════════════
CURRENT REGIME & PRICE STRUCTURE
═══════════════════════════════════════════════════════════════════════════════

Market Regime: [UPTREND/DOWNTREND/RANGE]
[Details...]

═══════════════════════════════════════════════════════════════════════════════
KEY INDICATOR STATUS
═══════════════════════════════════════════════════════════════════════════════

[Momentum, Trend Confirmation, Volatility...]

═══════════════════════════════════════════════════════════════════════════════
BACKTEST WINNER: [Strategy Name]
═══════════════════════════════════════════════════════════════════════════════

[Return, Sharpe, Win Rate, Validation...]

═══════════════════════════════════════════════════════════════════════════════
ENTRY & EXIT SETUP
═══════════════════════════════════════════════════════════════════════════════

Entry: $X with [condition]
Stop: $Y
Target 1: $Z
Target 2: $W
```

**Page Length:** EXACTLY 1 page (condensed technical summary)
**Key Characteristic:** User knows exact entry price, stop loss, and exit targets

---

### SECTION 3: FUNDAMENTAL CASE (1 Page)

**TEMPLATE SOURCE:** PROMPT_ACTIONABLE_FUNDAMENTALS.md

**Content Required:**
- Three investment thesis drivers (specific, quantified)
- Business quality score (XX/100)
- Profitability metrics (margins, growth rates)
- Financial health metrics (debt ratios, liquidity, FCF)
- Capital efficiency metrics (ROE, asset turnover)
- Near-term catalysts (3 specific events with dates and impact)
- Key risks (3-5 specific risks with probability and impact)
- Valuation analysis (current vs. bull/base/bear case)
- Fair value estimates for each scenario

**Format:**
```
═══════════════════════════════════════════════════════════════════════════════
TOP 3 INVESTMENT THESIS DRIVERS
═══════════════════════════════════════════════════════════════════════════════

DRIVER 1: [Narrative with facts]
DRIVER 2: [Narrative with facts]
DRIVER 3: [Narrative with facts]

═══════════════════════════════════════════════════════════════════════════════
BUSINESS QUALITY ASSESSMENT
═══════════════════════════════════════════════════════════════════════════════

Quality Score: XX/100
Profitability: [Margins and growth]
Financial Health: [Debt and liquidity]
Capital Efficiency: [ROE, efficiency metrics]

═══════════════════════════════════════════════════════════════════════════════
NEAR-TERM CATALYSTS
═══════════════════════════════════════════════════════════════════════════════

Catalyst 1: [Date] - Impact: [Range]
Catalyst 2: [Date] - Impact: [Range]
Catalyst 3: [Date] - Impact: [Range]

═══════════════════════════════════════════════════════════════════════════════
KEY RISKS TO THESIS
═══════════════════════════════════════════════════════════════════════════════

Risk 1: [Scenario] - Probability: [HIGH/MED/LOW] - Impact: [-XX%]
Risk 2: [Scenario] - Probability: [HIGH/MED/LOW] - Impact: [-XX%]
Risk 3: [Scenario] - Probability: [HIGH/MED/LOW] - Impact: [-XX%]

═══════════════════════════════════════════════════════════════════════════════
VALUATION ROADMAP
═══════════════════════════════════════════════════════════════════════════════

Bull Case: $[X] ([+XX]%)
Base Case: $[Y] ([+XX]%)
Bear Case: $[Z] ([-XX]%)
```

**Page Length:** EXACTLY 1 page (condensed fundamental summary)
**Key Characteristic:** User understands why business is attractive and what could go wrong

---

### SECTION 4: POSITION MANAGEMENT (½-1 Page)

**TEMPLATE SOURCE:** PROMPT_POSITION_MANAGEMENT.md

**Content Required:**
- Current position and sizing recommendation
- Entry strategy with 2-3 phases and specific prices
- Scale-out/profit-taking strategy with targets
- Hard stop loss (specific price, no exceptions)
- Thesis-breaking events
- 3-5 decision triggers with specific actions
- Monitoring checklist

**Format:**
```
═══════════════════════════════════════════════════════════════════════════════
ALLOCATION & SIZING DECISION
═══════════════════════════════════════════════════════════════════════════════

Current Position: X%
Recommendation: [INITIATE/ADD/HOLD/REDUCE] Y% allocation

═══════════════════════════════════════════════════════════════════════════════
ENTRY STRATEGY
═══════════════════════════════════════════════════════════════════════════════

Phase 1: [X]% at $[A]-$[B]
Phase 2: [X]% at $[C]-$[D]
Total: [Z]%

═══════════════════════════════════════════════════════════════════════════════
PROFIT-TAKING STRATEGY
═══════════════════════════════════════════════════════════════════════════════

Target 1: Sell [X]% at $[A]
Target 2: Sell [X]% at $[B]
Hold: [X]% until [condition]

═══════════════════════════════════════════════════════════════════════════════
DECISION TRIGGERS
═══════════════════════════════════════════════════════════════════════════════

🚨 Trigger 1: [Event] → Action
🚨 Trigger 2: [Event] → Action
🚨 Trigger 3: [Event] → Action
```

**Page Length:** ½-1 page (condensed action plan)
**Key Characteristic:** User has exact entry, stop, and exit prices + knows what could change recommendation

---

## OPTIONAL: TECHNICAL APPENDIX (10-20 Pages)

### Purpose of Appendix

The appendix contains all detailed TURN 1-5 work for users who want to verify methodology or dive deeper. It's **optional** (not delivered by default, available on request).

### Appendix Structure

**SECTION 5: TURN 1 - SYNTHESIS OUTPUT** (3-5 pages)
- All agent outputs (fundamental, technical, macro) shown in full
- Conflicts documented and resolved
- Synthesis narrative explaining how decisions were made
- All assumptions stated

**SECTION 6: TURN 2 - QUANTITATIVE VALIDATION** (2-3 pages)
- All formulas and calculations shown
- Backtest methodology explained
- All test results documented
- Out-of-sample validation results

**SECTION 7: TURN 3 - SCENARIO MODELING** (2-3 pages)
- All three scenarios modeled with math
- Probability distributions shown
- Sensitivity analysis data
- Monte Carlo results (if applicable)

**SECTION 8: TURN 4 - RISK ASSESSMENT** (2-3 pages)
- All VaR/CVaR calculations shown
- Stress testing results documented
- All 5-10 scenarios tested with results
- Gate matrix with pass/fail criteria
- Self-critique documented

**SECTION 9: TURN 5 - REFINEMENT & ITERATION** (2-3 pages)
- All assumption variations tested
- Sensitivity analysis matrix
- What changed from initial hypothesis
- Final recommendation validation
- Certainty assessment methodology

**SECTION 10-14: HISTORICAL CONTEXT** (Optional, 0-5 pages)
- Peer comparisons (if included)
- Historical financial data (if included)
- Industry background (if included)
- Additional research (if included)

---

## OUTPUT GENERATION PROCESS (Modified TURN 6)

### Phase 1: Standard Analysis (Internal)

**Steps 1-5:** Execute as normal
- TURN 1: Synthesize all agents
- TURN 2: Validate quantitatively
- TURN 3: Model scenarios
- TURN 4: Assess risks + gates
- TURN 5: Refine and iterate

**Output from Phases 1-5:** Raw material for report

### Phase 2: Decision-Focused Report Generation (New)

**NEW STEP 6A: Extract Key Points**
- Pull recommendation from TURN 1 + confidence from TURN 4 gates
- Extract top 3 thesis drivers from TURN 1 synthesis
- Pull price targets from TURN 3 scenario modeling
- Extract backtest winner and entry/exit from TURN 2 validation
- Get decision triggers from TURN 4 risk assessment
- Get allocation size from conviction score (confidence %)

**NEW STEP 6B: Format for Sections 1-4**
- Format executive summary (Section 1) per PROMPT_EXECUTIVE_SUMMARY.md
- Format technical analysis (Section 2) per PROMPT_ACTIONABLE_TECHNICALS.md
- Format fundamental case (Section 3) per PROMPT_ACTIONABLE_FUNDAMENTALS.md
- Format position management (Section 4) per PROMPT_POSITION_MANAGEMENT.md

**NEW STEP 6C: Assemble Primary Report**
- Combine Sections 1-4 into single 3-4 page document
- Verify total page count = 3-4 pages
- Verify recommendation visible in first ½ page
- Verify all prices are specific (not vague)
- Verify all triggers are actionable
- Final formatting and polish

**NEW STEP 6D: Optional - Assemble Technical Appendix (On Request)**
- Organize TURN 1-5 outputs as Sections 5-9
- Add any historical context as Sections 10-14
- Total appendix: 10-20 pages
- Provide as download link or separate PDF

### Output Delivery

**STANDARD DELIVERY:** Primary report (3-4 pages, Sections 1-4 only)

**WITH REQUEST:** 
- Primary report + technical appendix (15-25 pages total)
- Appendix labeled "Technical Appendix - Optional Detailed Analysis"

---

## SECTION-BY-SECTION QUALITY GATES

### Gate for Section 1 (Executive Summary)

BEFORE SUBMITTING, VERIFY:

- ✅ Recommendation is first sentence (BUY/HOLD/SELL)
- ✅ Confidence % is stated (not "high confidence")
- ✅ Current price with date is shown
- ✅ Three price targets with probabilities (sum to 100%)
- ✅ Weighted expected return is calculated
- ✅ Immediate action is specific (not "consider buying")
- ✅ Three drivers are quantified (each with supporting facts)
- ✅ Three triggers are specific (user would recognize immediately)
- ✅ Total length ≤ 1 page
- ✅ User can read in 1-2 minutes and understand recommendation

### Gate for Section 2 (Technical Analysis)

BEFORE SUBMITTING, VERIFY:

- ✅ Regime is clearly stated (UPTREND/DOWNTREND/RANGE/VOLATILE)
- ✅ Support/resistance levels are specific prices (3 each)
- ✅ RSI value is shown (not "neutral")
- ✅ MACD status is clear (BULLISH/BEARISH/NEUTRAL)
- ✅ Moving averages are referenced with prices
- ✅ Volume trend is stated (UP/STABLE/DOWN)
- ✅ Backtest winner is named with performance metrics
- ✅ Entry trigger is specific (price + condition)
- ✅ Stop loss is specific dollar amount
- ✅ First and second targets have specific prices
- ✅ Risk/reward ratio is calculated
- ✅ Total length ≤ 1 page
- ✅ User knows exact entry, stop, and exit prices

### Gate for Section 3 (Fundamental Case)

BEFORE SUBMITTING, VERIFY:

- ✅ Three thesis drivers are specific (not generic)
- ✅ Each driver has quantified supporting facts
- ✅ Quality score is stated (XX/100)
- ✅ Profitability metrics are shown (margins, growth)
- ✅ Financial health metrics are shown (debt, liquidity)
- ✅ Catalysts are specific events with dates
- ✅ Each catalyst has potential impact range
- ✅ Risks are specific scenarios with probabilities
- ✅ Each risk has dollar/percentage impact
- ✅ Valuation ratios are shown (P/E, EV/EBITDA)
- ✅ Bull/base/bear cases modeled with multiples
- ✅ Fair value ranges calculated
- ✅ Total length ≤ 1 page
- ✅ User understands why business is attractive and risks

### Gate for Section 4 (Position Management)

BEFORE SUBMITTING, VERIFY:

- ✅ Current position stated
- ✅ Allocation recommendation is specific %
- ✅ Entry phases have specific price ranges
- ✅ Total allocation is stated
- ✅ Scale-out strategy has specific targets
- ✅ Hard stop loss is specific price (no exceptions)
- ✅ Decision triggers are specific and actionable
- ✅ Each trigger has specific action (HOLD/REDUCE/EXIT)
- ✅ Monitoring checklist is actionable
- ✅ Total length ≤ 1 page
- ✅ User can execute position immediately without clarification

---

## TESTING CRITERIA FOR ACTIONABLE REPORTING

### Speed Test (PASS/FAIL)

**Requirement:** User makes decision in <5 minutes

**Test:**
1. Give primary report to user
2. Time how long to read
3. Ask: "What's your decision?"
4. Evaluate: Did they understand recommendation? Can they execute?

**Pass Criteria:**
- Reading time ≤ 4 minutes ✅
- User correctly states recommendation ✅
- User can articulate action (entry price, stop loss, target) ✅

---

### Clarity Test (PASS/FAIL)

**Requirement:** 95%+ of users understand what to do without asking for clarification

**Test:**
1. Give report to user
2. Ask: "If you had $100,000, what would you do with this stock?"
3. Evaluate: Can they execute without asking "but what if...?" questions?

**Pass Criteria:**
- Entry price is specific (e.g., "$145-150" not "near support") ✅
- Stop loss is specific (e.g., "$130" not "if breaks support") ✅
- Allocation is specific (e.g., "3% of portfolio" not "moderate") ✅
- User can execute immediately ✅

---

### Depth Test (PASS/FAIL)

**Requirement:** Detailed methodology available for verification

**Test:**
1. User reads primary report and makes decision
2. User downloads technical appendix
3. User reviews TURN 1-5 detailed work
4. Evaluate: Can user verify all claims? Are calculations shown?

**Pass Criteria:**
- All calculations documented in appendix ✅
- All assumptions stated ✅
- All backtest results shown ✅
- User can trace from appendix back to primary report ✅

---

## IMPLEMENTATION REQUIREMENTS

### What Changes in Code

**TURN 6 Logic (Modified):**
```
OLD TURN 6:
  1. Compile all TURNs into sections
  2. Add synthesizing narratives
  3. Output 25-40 page document

NEW TURN 6:
  1. Extract key decision points from TURNs 1-5
  2. Format as 4 sections per templates
  3. Assemble as 3-4 page primary report
  4. Package TURNs 1-5 as optional appendix
  5. Output TWO versions: (A) Primary, (B) Primary + Appendix
```

### Templates Required

- ✅ PROMPT_EXECUTIVE_SUMMARY.md (created)
- ✅ PROMPT_ACTIONABLE_TECHNICALS.md (created)
- ✅ PROMPT_ACTIONABLE_FUNDAMENTALS.md (created)
- ✅ PROMPT_POSITION_MANAGEMENT.md (created)
- ✅ This file (PROMPT_REPORT_OUTPUT_ACTIONABLE.md)

---

## PRODUCTION ROLLOUT

### Timeline

**Week 1 (Nov 24-28):** All 5 template files completed ✅
**Week 2 (Dec 1-5):** Code TURN 6 logic, test with existing analyses
**Week 3 (Dec 8-12):** Refine templates, test with 5 new stocks live
**Week 4 (Dec 15+):** Deploy as Stage 1 v1.4.1-Release.1

### Success Metrics

**Primary Metric:** 80%+ of users decide in <5 minutes

**Secondary Metrics:**
- 95%+ users understand what to do
- 100% reports have specific entry prices
- 100% reports specify position sizing
- 100% reports include hard stop loss

**Tertiary Metrics:**
- 85%+ prefer new format
- 40-60% download appendix
- Higher user decision confidence

---

## APPROVAL & GOVERNANCE

**Issued By:** Master-Architect-Prompt-v1.0.md (Design Authority)
**Status:** ✅ APPROVED for immediate implementation
**Critical Path:** Blocks Stage 1 deployment (Dec 15)
**Target Deployment:** v1.4.1-Release.1 (December 15, 2025)

