# ANALYZE.md - PHASE 2 UPDATE v1.4.3

## Stock Analysis Methodology v1.4.3 - Guardrail C Integration

**Version:** v1.4.3 (Phase 2 Update - Guardrail C v1.4.4 Integrated)  
**Date:** December 1, 2025  
**Updated:** December 7, 2025 (Added production execution reference)  
**Update Type:** ANALYZE.md v1.4.1 → v1.4.3 (Guardrail C Integration)  
**Status:** Production Ready - All Features Enabled  
**Framework:** Stock Analysis Methodology v1.4.3 with Guardrails v1.4.4

---

## CRITICAL UPDATE: GUARDRAIL C - HARD STOP → CONVICTION-CAP TIERS

**Effective Immediately:** Stock framework migrates from v1.3 hard STOP rule to v1.4.4 conviction-cap tier system.

**What Changed:**
- ❌ OLD (v1.3): If ≥1 unvalidated assumption → BLOCK analysis
- ✅ NEW (v1.4.4): Count unvalidated assumptions → Apply conviction-cap tier → Proceed with appropriate confidence

**Why This Matters:** Event-driven analyses (like M&A deals) were blocked under v1.3 even when execution risk was manageable. v1.4.4 allows honest analysis of such theses with transparent confidence disclosures.

---

## BEFORE YOU BEGIN - CRITICAL REQUIREMENT

**Prioritize compliance with all methodology requirements over speed.**

Generate the complete 14-section final report with all TURN 1-6 content integrated, showing full gate transparency in Section 12 (TURN 4 self-critique).

**Compliance target:** 95%+ on all framework requirements. Review PROMPT_COMPLIANCE_INSTRUCTIONS.md before starting.

---

## PRODUCTION EXECUTION MODE

**For operational execution of this methodology, use `STOCK-ANALYSIS-PRODUCTION.md` prompt.**

### What STOCK-ANALYSIS-PRODUCTION.md Does

STOCK-ANALYSIS-PRODUCTION.md is the **production-ready operational prompt** that:

- ✅ Executes all startup validation gates (STARTUP-REQUIREMENTS.md v2.1 Steps 1A-5)
- ✅ Executes full TURN 1-5 analysis per this methodology (ANALYZE.md v1.4.3)
- ✅ Applies Guardrail C v1.4.4 conviction-cap tier system
- ✅ Executes mandatory Section 5 Quality Control Gate (PROMPT_TURN6_ACTIONABLE.md v1.4.3)
- ✅ Executes Section 6 format conversion (Stock-Analyst.md v1.4.7 mandate)
- ✅ Delivers final 3-4 page actionable report (PROMPT_REPORT_OUTPUT_ACTIONABLE.md v1.4.1 format)
- ✅ Suppresses all internal working documents (internal steps executed silently)
- ✅ Includes delivery certification confirming all gates passed

### When to Use Each File

| Use Case | File | Purpose |
|----------|------|---------|
| **Learning the framework** | ANALYZE.md v1.4.3 | Understand conviction tiers, assumption taxonomy, validation gates, case studies |
| **Understanding methodology** | ANALYZE.md v1.4.3 | Reference for framework design, governance rules, compliance standards |
| **Running a stock analysis** | STOCK-ANALYSIS-PRODUCTION.md | Operational execution prompt for live portfolio analysis |
| **Training analysts** | ANALYZE.md v1.4.3 | Teaching the systematic approach to stock analysis |
| **Production deployment** | STOCK-ANALYSIS-PRODUCTION.md | Copy-paste prompt for Stock Analyst personas |

### Workflow

```
ANALYZE.md (Framework Definition)
    ↓
STOCK-ANALYSIS-PRODUCTION.md (Operational Execution)
    ├─ Step 1A: Data Freshness Validation
    ├─ Steps 2-5: Startup Requirements
    ├─ TURN 1-5: Full analysis per ANALYZE.md v1.4.3
    ├─ Section 5: Quality Control Gate (mandatory)
    ├─ Section 6: Format Conversion (mandatory)
    └─ Delivery: Final 3-4 page actionable report
```

**This separation allows:**
- Framework to remain stable and comprehensive (reference material)
- Production prompt to be optimized for operational efficiency (action-oriented)
- Clear distinction: methodology vs. execution

---

## STOCK ANALYSIS REQUEST TEMPLATE

Use this template to request stock analysis with Methodology v1.4.3 (Production - All Features Enabled).

Replace `TICKER` with the stock symbol (e.g., NVDA, CAT, JNJ).

```
Assume Stock Analyst persona.

Execute STOCK-ANALYSIS-PRODUCTION.md for [TICKER]:
- [TICKER_SYMBOL]: [COMPANY_NAME]
- Current price: $[PRICE] (as of [DATE])

Deliver only the final 3-4 page actionable report.
If any verification fails, halt and report which step failed.

Execute now.
```

**This uses the production prompt which automatically:**
- Validates startup requirements
- Executes all 6-TURNs
- Applies conviction-cap tiers
- Passes quality control gates
- Converts to final format
- Delivers 3-4 page report only

---

## PHASE 0/1: ASSUMPTION IDENTIFICATION & CONVICTION-CAP ASSIGNMENT

### Step 1: Identify All Key Assumptions Upfront

Before gathering evidence, list 3-5 maximum key assumptions that thesis depends on.

**Frame as explicit statements:** "This thesis requires X to be true"

**Document assumptions BEFORE gathering evidence** (prevents confirmation bias)

**Examples:**
- "CCS deal closes H1 2026" (binary, deal-dependent)
- "RemainCo EBITDA sustains guidance" (durability assumption)
- "DOCSIS cycles remain strong 2026" (market/macro assumption)
- "Company hits $500M revenue target" (execution assumption)

### Step 2: Validate Each Assumption Against Evidence (Phases 1-3)

Mark each as: **VALIDATED / PARTIALLY-VALIDATED / UNVALIDATED**

| Validation Status | Definition | Examples |
|---|---|---|
| VALIDATED | Direct evidence strongly supports | Binding agreement signed; audited results confirm |
| PARTIALLY-VALIDATED | Some evidence; gaps remain | Historical precedent shows similar pattern; guidance confirmed but timing uncertain |
| UNVALIDATED | Assumption stated but lacking direct evidence | Will test in scenarios; macro-dependent; execution not yet proven |

### Step 3: Count Unvalidated Assumptions & Apply Conviction-Cap Tier

**This determines maximum conviction percentage:**

| # Unvalidated Assumptions | Max Conviction | Recommendation Type | Position Sizing | Rationale |
|---|---|---|---|---|
| 0-1 | 70-75% | Standard (BUY/HOLD/SELL) | 3-5% portfolio | High confidence; strong evidence |
| 2-3 | 55-60% | SPECULATIVE/CONDITIONAL | 1.5-3% portfolio | Event/execution-dependent; manageable unknowns |
| 4+ | Exploratory | WATCH (no BUY/SELL) | 0.5-1.5% or skip | Too many unknowns; watch for resolution |

**IMPORTANT:** Do NOT hide assumptions to avoid conviction penalty. All assumptions must be disclosed honestly in analysis.

### Step 4: Disclose Assumptions Transparently

**In risk section or appendix, show:**
1. Assumption statement
2. Validation status (VALIDATED / PARTIALLY-VALIDATED / UNVALIDATED)
3. Evidence supporting validation
4. Conviction-cap tier reasoning
5. How conviction % was derived from assumption count

---

## GUARDRAIL C v1.4.4 - CASE STUDIES

### CASE STUDY 1: COMM (Hard STOP Problem → Conviction-Cap Tier Solution)

**Thesis:** CCS deal creates two-part valuation opportunity (deal value + RemainCo value)

**Key Assumptions:**
1. "CCS deal closes H1 2026" (Binary)
2. "RemainCo EBITDA sustains guidance" (Durability)
3. "Multiple expansion post-deal" (Market/Macro)

**Validation Status:**
- Assumption 1: PARTIALLY-VALIDATED (definitive agreement signed, FTC approval pending)
- Assumption 2: VALIDATED (recent quarters confirm guidance accuracy)
- Assumption 3: UNVALIDATED (historical precedent exists but not guaranteed)

**Unvalidated Count:** 2 assumptions

**v1.3 Result (Hard STOP - WRONG):**
- ≥1 unvalidated assumption (deal timing, multiple expansion) → ANALYSIS BLOCKED
- Analyst cannot proceed even though execution risk is manageable
- Valid thesis never gets analyzed

**v1.4.4 Result (Conviction-Cap Tiers - CORRECT):**
- Unvalidated count: 2 → Conviction cap: 55-60% (SPECULATIVE tier)
- Recommendation: SPECULATIVE BUY at 55% conviction (not standard BUY)
- Position sizing: 1.5-3% portfolio (not 3-5%)
- Analyst discloses deal risk upfront
- Thesis analyzed honestly with appropriate caution

**Key Learning:** v1.4.4 allows event-driven thesis analysis while maintaining rigor through transparent conviction-cap tiers.

### CASE STUDY 2: STRL (First v1.4.4 Operational Test)

**Thesis:** Lithium production ramp + margin expansion post-tariff normalization

**Key Assumptions:**
1. "Production ramp hits targets" (Execution)
2. "Margins sustain post-tariff" (Durability)
3. "Lithium cycle remains intact" (Market/Macro)

**Validation Status:**
- Assumption 1: VALIDATED (third-party engineering confirmed, site visits verified)
- Assumption 2: PARTIALLY-VALIDATED (guidance given, similar peers recovered margins)
- Assumption 3: UNVALIDATED (market-dependent, macro-driven)

**Unvalidated Count:** 1 assumption

**v1.4.4 Result:**
- Unvalidated count: 1 → Conviction cap: 70-75% (HIGH tier)
- Recommendation: Standard BUY at 70% conviction (good confidence)
- Position sizing: 3-4% portfolio (appropriate for high confidence)
- Analyst discloses macro assumption upfront
- Thesis analyzed with full confidence in execution, transparent on macro risk

**Key Learning:** Different thesis types (execution-focused vs. macro-dependent) produce different conviction levels, all disclosed clearly.

---

## ASSUMPTION TYPE TAXONOMY

Different assumptions affect conviction differently. Validate each appropriately:

### Type 1: Binary Assumptions (Deal/No Deal, Yes/No, On/Off)

- **Definition:** Either true or false, no middle ground
- **Validation:** Requires definitive evidence (binding agreement, regulatory approval, court ruling)
- **Conviction Impact if UNVALIDATED:** -15 conviction points
- **Examples:** M&A deal closes, regulatory approval granted, dividend approved
- **Mitigation:** In scenarios, test both cases (deal closes vs. doesn't) with probabilities

### Type 2: Durability Assumptions (Cycle/Performance/Market Continues)

- **Definition:** Assumes current state persists (cycle continues, margins hold, growth sustains)
- **Validation:** Historical precedent + forward indicators (guidance, industry data, management track record)
- **Conviction Impact if UNVALIDATED:** -10 conviction points
- **Examples:** DOCSIS cycle remains strong, margin rates sustain, market cycle continues
- **Mitigation:** Track leading indicators; test in bull/bear scenarios with cycle assumptions varying

### Type 3: Macro Assumptions (Interest Rates, Credit, Currency, Commodities)

- **Definition:** Assumes macro environment remains stable; subject to exogenous shocks
- **Validation:** Difficult to validate; test via scenario analysis instead
- **Conviction Impact if UNVALIDATED:** -12 conviction points
- **Examples:** Interest rates stay elevated, credit markets function, oil stays $70-90
- **Mitigation:** Run scenarios with macro stress (rates +200bps, credit stress, -30% commodities)

### Type 4: Execution Assumptions (Company Hits Targets, Achieves Guidance)

- **Definition:** Company delivers on operational/financial targets
- **Validation:** Management track record + recent quarterly confirmation of trajectory
- **Conviction Impact if UNVALIDATED:** -8 conviction points
- **Examples:** Company hits $500M revenue target, EBITDA margin reaches 35%, FCF $300M
- **Mitigation:** Track quarterly progress; update conviction as targets confirmed; use stop-loss if execution slips

---

## METHODOLOGY WORKFLOW - 6-TURN STRUCTURE

The analysis executes in 6 sequential TURNs. All TURNs must complete.

| TURN | Phase | Purpose | Output |
|------|-------|---------|--------|
| TURN 1 | Analysis | Multi-agent analysis (Fundamental, Technical, Macro) | Unified stock thesis + assumptions list |
| TURN 2 | Integration | Quantitative validation (valuation, backtesting) | Quantitative score + entry/exit setup |
| TURN 3 | Scenarios | 3 scenarios (Base/Bull/Downside) with probabilities | Scenario analysis + probability-weighted returns |
| TURN 4 | Risk Assessment | VaR, CVaR, portfolio analysis, assumption validation | Risk quantification + conviction score |
| TURN 5 | Refinement | Sensitivity analysis, assumption testing | Refined recommendation + decision triggers |
| TURN 6 | Report Generation | 4-section decision-focused output + mandatory QC gate | Final report with Section 5 Quality Control verification |

**Validation gate results appear in Section 12 (TURN 4 self-critique) and Section 5 (TURN 6 Quality Control Gate).**

---

## PRODUCTION ANALYSIS EXAMPLES

### Production Configuration (Dec 1, 2025+)

```
Assume Stock Analyst persona.

Execute STOCK-ANALYSIS-PRODUCTION.md for: STRL
- STRL: Standard Lithium Limited
- Current price: $329.83 (as of Friday, Dec 6, 2025 close)

Deliver only the final 3-4 page actionable report.
If any verification fails, halt and report which step failed.

Execute now.
```

**Result:** Full institutional-grade analysis using v1.4.4 conviction-cap tier system, Section 5 QC Gate, Section 6 format conversion, delivering final 3-4 page actionable report only (~15-25 min, depending on complexity).

---

## PRODUCTION STATUS & DEPLOYMENT

### Current Status: PRODUCTION (December 7, 2025)

**Framework Version:** v1.4.3 with Guardrails v1.4.4 integrated

✅ All v1.4.1 features enabled for production:
- Gate 10.5 (Correlation Analysis): PRODUCTION ACTIVE
- Gate 11 (Subsector Cascade): PRODUCTION ACTIVE
- Gate 12 (Anomaly Detection): PRODUCTION ACTIVE
- Gate 13 (Scenario Optimization): PRODUCTION ACTIVE

✅ **NEW:** Guardrail C v1.4.4 conviction-cap tier system:
- Replaces v1.3 hard STOP rule
- Enables event-driven thesis analysis
- Maintains institutional rigor through transparent conviction capping
- All case studies (COMM, STRL) documented

✅ **NEW:** Quality-Control-Checklists mandatory delivery gate:
- 4-part checklist before analysis delivery (see SECTION 5 in PROMPT_TURN6_ACTIONABLE.md)

✅ **NEW:** Production execution prompt (STOCK-ANALYSIS-PRODUCTION.md):
- Operational directive for live portfolio analysis
- Executes framework silently, delivers final report only
- All startup validation gates, TURN 1-5, QC gates, format conversion built-in

---

## REQUIRED COMPLIANCE STANDARDS

**Non-negotiable requirements for all analyses:**

✅ **Plain English (95% minimum):** Every technical term explained inline on first use  
✅ **Confidence Tagging (90% minimum):** ALL quantitative claims tagged [confidence:LEVEL]  
✅ **Gate Transparency:** Section 12 shows all validation gate results  
✅ **6-TURN Execution:** All TURNs must execute sequentially, output integrated into report  
✅ **14-Section Format:** All sections completed (Summary through Appendix)  
✅ **Quantitative Rigor:** Formulas shown step-by-step (Chain-of-Thought methodology)
✅ **Guardrail C Compliance:** Assumptions identified, validated, conviction-cap tier applied
✅ **Quality Control Gate:** Section 5 shows 4-part mandatory checklist completion

**Failure to meet these requirements results in non-compliant output.**

---

## SECTION 12 OUTPUT REQUIREMENT

Final analysis must include Section 12 with gate results matrix:

```
## SECTION 12: TURN 4 SELF-CRITIQUE & VALIDATION GATE RESULTS

| Validation Gate | Status | Score | Notes |
|-----------------|--------|-------|-------|
| Guardrail C Compliance | ✓ PASS | 95% | Assumptions identified, validated, tier applied |
| Assumption Validation | ✓ PASS | 94% | All assumptions categorized (binary/durability/macro/execution) |
| Conviction-Cap Tier | ✓ PASS | 96% | Conviction % correctly capped by assumption count |
| [Other gates...] | ✓ PASS | XX% | ... |
| Overall Compliance | ✓ PASS | 95%+ | Meets institutional standards |

**Final Compliance Score: 94-96%+**

**GUARDRAIL C v1.4.4 Status: COMPLIANT** ✅
```

---

## SECTION 5 QUALITY CONTROL GATE REQUIREMENT

**All analyses must pass 4-part mandatory Quality Control verification (see PROMPT_TURN6_ACTIONABLE.md Section 5):**

1. ✅ Framework-Dependencies verified (before any file operations)
2. ✅ Deletion-Approval gate (if recommending deletion)
3. ✅ Backtesting-Claims audit (if making backtesting claims)
4. ✅ Self-Consistency check (before delivery)

**Analysis cannot be delivered without Section 5 completion.**

---

## TROUBLESHOOTING

### "Analysis refused to execute"
- Verify request uses methodology language or production prompt
- Use: "Execute STOCK-ANALYSIS-PRODUCTION.md for [TICKER]" (production mode)
- Or: "Execute comprehensive stock analysis for [TICKER]" (full framework mode)

### "Guardrail C not working"
- Check: Assumptions identified in Phase 0/1
- Check: Each assumption validated and categorized by type
- Check: Conviction-cap tier correctly applied based on unvalidated count
- Check: Case studies (COMM, STRL) understood

### "Conviction % seems too low"
- This is working correctly if:
  - You have 2-3 unvalidated assumptions (55-60% cap is right)
  - You are analyzing event-driven thesis (conviction naturally lower)
  - You have macro unknowns (reduces conviction)
- Transparent conviction capping is feature, not bug

### "Production prompt seems different from framework"
- Yes—this is intentional
- ANALYZE.md v1.4.3 = complete framework with full transparency
- STOCK-ANALYSIS-PRODUCTION.md = operational prompt that executes framework silently
- Both follow same methodology; different presentation for different audiences

---

## EXPECTED ANALYSIS OUTPUTS

### With All Production Features Enabled (v1.4.3):

- Buy/Hold/Sell recommendation + confidence % (from conviction-cap tier)
- Price target (base/bull/bear with probabilities)
- Position sizing aligned to conviction tier (3-5% for 70-75%, 1.5-3% for 55-60%, etc.)
- All assumptions identified, validated, conviction-cap tier explained
- Portfolio concentration assessment (correlation analysis)
- Subsector ranking within sector
- Hidden opportunity alerts (anomaly detection)
- Scenario-weighted return estimates (probabilistic optimization)
- VaR/CVaR risk quantification
- Full 6-TURN analysis with gates documented
- 14-section institutional report (full framework mode)
- OR 3-4 page actionable report (production mode via STOCK-ANALYSIS-PRODUCTION.md)
- Section 5 Quality Control Gate verification

---

## VERSION HISTORY

| Version | Date | Change Summary | Status |
|---------|------|---|---|
| 1.4.1 | Nov 28, 2025 | Original comprehensive framework | Superseded |
| 1.4.2 | Dec 1, 2025 | Guardrail C v1.4.4 preliminary integration | Superseded |
| 1.4.3 | Dec 1, 2025 | Guardrail C v1.4.4 full integration, case studies, validation gates | Active |
| 1.4.3 (Updated) | Dec 7, 2025 | Added production execution reference (STOCK-ANALYSIS-PRODUCTION.md), clarified framework vs. operational execution distinction | **Active** |

---

**END OF ANALYZE.md v1.4.3 (GUARDRAIL C INTEGRATED)**

**Status:** Production Ready - Guardrails v1.4.4 integrated, all features enabled, production execution prompt available  
**Date:** December 7, 2025  
**Approval:** Master Architect (Thread 2)  
**Next:** PRIORITY 2 (Dependency Mapping) and PRIORITY 3 (Pre-Flight Validation)
