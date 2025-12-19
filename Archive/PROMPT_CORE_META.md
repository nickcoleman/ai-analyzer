# PROMPT_CORE_META.md

**Version:** v1.4.1 (Core Metadata & Framework Overview)  
**Date:** November 25, 2025  
**Status:** Production Ready - v7 Base Framework  
**Purpose:** Framework workflow structure, TURN sequence, and core requirements  

---

## FRAMEWORK ARCHITECTURE - v1.4.1

### **Executive Summary**

The v1.4.1 framework executes institutional-grade stock analysis across 6 TURN phases, producing decision-focused reports (3-4 pages) instead of process-centric data dumps (25-40 pages).

**Key Governance:**
- ✅ Master-Architect Authority: Master-Architect-Prompt-v1.0.2.md
- ✅ Methodology & Compliance: GOVERNANCE-FRAMEWORK.md
- ✅ Quality Guardrails: QUALITY-GUARDRAILS.md (mandatory, non-negotiable)
- ✅ Execution Logic: TURN-PROMPTS-v1.4.1.md

---

## FRAMEWORK PHASES (TURN 1-6)

### **Phase 0: Pre-Analysis Verification (REQUIRED)**

Before analysis begins, verify:
- ✅ US market / USD currency
- ✅ Sufficient data available (SEC EDGAR, technicals, macro context)
- ✅ Market regime identified (Growth/Value/Defensive/Transition)

**Failure:** If Phase 0 fails, stop analysis. Do not proceed.

---

### **TURN 1: Synthesis - Multi-Agent Analysis (REQUIRED)**

**4 Independent Agents Synthesize:**
1. **Fundamental Analysis Agent** - DCF valuation, quality, growth
2. **Technical Analysis Agent** - Support/resistance, patterns, momentum
3. **Macro Analysis Agent** - Market regime, rates, sector rotation
4. **Sentiment Analysis Agent** - Positioning, flows, short interest

**Synthesis Process:**
- Each agent provides independent analysis
- Identify areas of agreement (high conviction) and conflict (risks)
- Preliminary recommendation with confidence score
- Escalate conflicts for resolution

**Output:** Unified synthesis with preliminary recommendation

**Reference:** TURN-PROMPTS-v1.4.1.md (TURN 1 section)

---

### **TURN 2: Quantitative Validation - Backtesting (REQUIRED)**

**Validate Technical Setup:**
- Backtest strategy using historical price data
- Calculate win rate, Sharpe ratio, profit factor
- Validate entry/stop/target prices
- Verify statistical significance

**Mandatory Guardrail:**
- ✅ BACKTESTING-CLAIMS-AUDIT.md must pass before delivery
- ✅ All backtesting claims must be traceable to PROMPT_QUANT_BACKTEST.md
- ✅ Unvalidated claims = analysis rejection

**Output:** Validated entry/stop/target prices with win rates

**Reference:** TURN-PROMPTS-v1.4.1.md (TURN 2 section)

---

### **TURN 3: Scenario Modeling - Conditional Analysis (REQUIRED)**

**Model 3 Market Scenarios:**
1. **Bull Case** - Market favorable, stock outperforms (probability %)
2. **Base Case** - Most likely scenario (typically 50-60% probability)
3. **Bear Case** - Market adverse, stock underperforms (probability %)

**Each Scenario Includes:**
- Market assumptions
- Stock impact
- Price target
- Conditional recommendation (what to do if scenario occurs)

**Output:** Bull/Base/Bear targets with scenario-conditional recommendations

**Reference:** TURN-PROMPTS-v1.4.1.md (TURN 3 section)

---

### **TURN 4: Risk Assessment - VaR/CVaR & Confidence Scoring (REQUIRED)**

**Quantify Risks:**
- VaR/CVaR analysis (Value at Risk worst case)
- Stress test key scenarios (recession, rate shock, sector rotation)
- Concentration risk in portfolio context
- Calculate overall recommendation confidence (0-100%)

**Confidence Scoring Formula:**
- +20% if fundamental & technical agree
- +20% if backtesting win rate >55%
- +15% if macro backdrop supportive
- +15% if sentiment not extreme
- +15% if risk/reward >1.5:1
- +15% if VaR acceptable

**Position Sizing by Confidence:**
- 80-100% confidence → 3-5% position size
- 60-79% confidence → 2-3% position size
- 40-59% confidence → 0-1% position size
- <40% confidence → HOLD or WAIT

**Output:** Confidence score, position sizing, risk-adjusted recommendation

**Reference:** TURN-PROMPTS-v1.4.1.md (TURN 4 section)

---

### **TURN 5: Iterative Refinement - Validation & Assumptions (REQUIRED)**

**Test Assumptions:**
- What if earnings growth is 10% instead of 15%?
- What if multiple compression occurs?
- What if macro condition changes?

**Validate Convergence:**
- Do all 4 TURN agents agree on recommendation?
- Are there unresolved conflicts?
- What's the highest conviction achievable?

**Final Recommendation:**
- Incorporate assumption sensitivities
- Verify convergence across methods
- Document key assumption and key risk
- Set conviction level for TURN 6 output

**Output:** Final recommendation with confidence, ready for reporting

**Reference:** TURN-PROMPTS-v1.4.1.md (TURN 5 section)

---

### **TURN 6: Output Generation - Decision-Focused Report (REQUIRED)**

**Primary Report (3-4 pages):**
1. Executive Summary (½-1 page) - Recommendation on page 1
2. Technical Analysis (1 page) - Entry, stop, target
3. Fundamental Case (1 page) - Thesis drivers, catalysts, risks
4. Position Management (½-1 page) - Sizing, entry, exit, triggers

**Optional Technical Appendix (10-20 pages):**
- All TURN 1-5 detailed analysis
- Calculations shown
- Gate results documented

**Quality Gates (All 6 must PASS):**
1. ✅ Recommendation clarity (BUY/HOLD/SELL, not hedged)
2. ✅ Technical precision (specific prices, not ranges)
3. ✅ Fundamental completeness (3 drivers, 3 catalysts, 3 risks)
4. ✅ Position clarity (% allocation, entry/exit prices, hard stop)
5. ✅ Decision triggers (3-5 observable events, specific actions)
6. ✅ User experience (<5 minutes to decide, execute immediately)

**Reference:** TURN-PROMPTS-v1.4.1.md (TURN 6 section) + all PROMPT_ACTIONABLE_* templates

---

## MANDATORY QUALITY GUARDRAILS (BEFORE ANY DELIVERY)

**All analysis must pass these 4 guardrails:**

### **Guardrail 1: File Management**
- Consult FRAMEWORK-DEPENDENCIES.md before ANY file operation
- Never recommend deletion of 🔴 CRITICAL files
- Complete DELETION-APPROVAL-GATE.md checklist if deletion considered

**Reference:** QUALITY-GUARDRAILS.md (Guardrail 1)

---

### **Guardrail 2: Backtesting Integrity**
- Before claiming ANY backtesting results, run BACKTESTING-CLAIMS-AUDIT.md
- Verify PROMPT_QUANT_BACKTEST.md exists
- All claims traceable and validated
- Zero exceptions for unvalidated claims

**Reference:** QUALITY-GUARDRAILS.md (Guardrail 3)

---

### **Guardrail 3: Self-Consistency**
- Complete SELF-CONSISTENCY-CHECK.md before finalizing recommendations
- Verify no contradictions (framework ready + deletion recommendations)
- Verify no feature claims contradicting file deletions
- All checks must pass

**Reference:** QUALITY-GUARDRAILS.md (Guardrail 4)

---

### **Guardrail 4: No Exceptions Policy**
- Guardrails are MANDATORY, not optional
- Failure to use guardrails = work rejection
- Repeated violations = escalation to Strategic Leadership

---

## COMPLIANCE REQUIREMENTS (v1.4.2)

**All analyses must maintain:**

1. **Plain English (95%+)**
   - All jargon explained on first use
   - No undefined acronyms
   - Definitions inline or in appendix

2. **Confidence Tagging (90%+)**
   - HIGH: Backed by multiple sources / quantitative validation
   - MEDIUM: Supported by data / logical argument
   - LOW: Estimate / assumption / single source

3. **Guardrail Compliance (100%)**
   - Not 95%, not 90% → 100%
   - All applicable guardrails must be used
   - No exceptions

---

## METADATA REFERENCES

**Core Framework Files:**
- ✅ TURN-PROMPTS-v1.4.1.md - All TURN 1-5 execution logic
- ✅ GOVERNANCE-FRAMEWORK.md - Authority, methodology, compliance
- ✅ QUALITY-GUARDRAILS.md - All mandatory guardrails
- ✅ Master-Architect-Prompt-v1.0.2.md - Design authority

**Output Templates:**
- ✅ PROMPT_EXECUTIVE_SUMMARY.md
- ✅ PROMPT_ACTIONABLE_TECHNICALS.md
- ✅ PROMPT_ACTIONABLE_FUNDAMENTALS.md
- ✅ PROMPT_POSITION_MANAGEMENT.md
- ✅ PROMPT_REPORT_OUTPUT_ACTIONABLE.md
- ✅ PROMPT_TURN6_ACTIONABLE.md

**Configuration Files:**
- ✅ ANALYZE.md - Analysis execution kickoff
- ✅ STARTUP-REQUIREMENTS.md - Framework configuration
- ✅ PROMPT_KICKOFF.md - Analysis initialization
- ✅ PORTFOLIO_CASCADE_MODEL.md - Portfolio risk cascade

---

## DEPLOYMENT STATUS

**Stage 1: v1.4.1 Production Ready**
- ✅ All TURN 1-5 logic finalized
- ✅ Actionable Reporting output redesigned (3-4 pages)
- ✅ 6 quality gates defined and testable
- ✅ 4 mandatory guardrails implemented
- ✅ Compliance framework (95% plain English, 90% confidence tagging)
- ✅ Ready for production deployment (Dec 15, 2025)

**v7.0: Market Framework (In Design)**
- ⏳ Market analysis TURNs (parallel to v1.4.1 TURNs)
- ⏳ Market integration bridge (market context → stock analysis)
- ⏳ Phase-based rollout (Jan-Feb 2026)

---

**END OF PROMPT_CORE_META.md**