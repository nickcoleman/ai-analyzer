# QUALITY-GUARDRAILS v1.4.7
## Critical Framework Guardrails - All Versions Consolidated

**Version:** 1.4.7  
**Last Updated:** December 7, 2025  
**Status:** Production Ready - All Guardrails A-D Complete  
**Authority:** Master Architect Framework v1.0.7  
**Recovery Note:** Content recovered from dependent files validation Dec 7, 2025

---

## GUARDRAIL A: CONVICTION ASSESSMENT FUNDAMENTALS

### A.1 Core Principle: Conviction as Validated Evidence Framework

**Definition:** Conviction score (0-100%) represents the percentage confidence that the investment thesis will achieve its stated objective, based on validated evidence across fundamental, technical, and quantitative dimensions.

**Core Formula:**
```
Conviction % = (Evidence Base × Validation Depth × Timeliness) - Assumption Penalties
```

Where:
- **Evidence Base:** Quality and breadth of supporting data (fundamental, technical, macro)
- **Validation Depth:** How thoroughly each assumption has been tested and verified
- **Timeliness:** How current the evidence is (data freshness penalties apply)
- **Assumption Penalties:** Deductions for unvalidated or partially-validated assumptions

### A.2 Conviction Baseline Establishment

**Initial Conviction Baseline (before assumptions):**

| Data Quality | Evidence Breadth | Quantitative Validation | Baseline Conviction |
|---|---|---|---|
| High (audited financials, consensus data) | 3+ analyst perspectives, 5+ data sources | Valuation + Technical + Quantitative models | 65-75% |
| Medium (recent guidance, industry data, 2-3 sources) | 2 perspectives, 3-4 data sources | Valuation + Technical only | 55-65% |
| Low (limited data, single source, unaudited) | 1 perspective, 1-2 data sources | Valuation only | 40-55% |

**Baseline represents high-quality analysis without external event/macro risks.**

### A.3 Conviction Modification Framework

**Adjustments to baseline conviction:**

**Increases to Conviction (+points):**
- Management track record validated (recent quarters confirm guidance): +3 to +5 points
- Multiple independent data sources confirm thesis: +2 to +4 points
- Technical confirmation (breakout, support hold): +2 to +3 points
- Peer validation (other analysts reach same conclusion): +2 to +3 points
- Catalysts near-term and date-verified: +2 to +5 points

**Decreases to Conviction (-points):**
- Unvalidated assumption (Binary): -15 points
- Unvalidated assumption (Durability): -10 points
- Unvalidated assumption (Macro): -12 points
- Unvalidated assumption (Execution): -8 points
- Data freshness >7 days old: -2 to -5 points per data source
- Macro headwind (rates, credit, commodity shock): -5 to -15 points

### A.4 Conviction Documentation Requirements

**All analyses must document:**
1. Baseline conviction (before assumptions)
2. Each assumption identified, typed, and validated status
3. Penalties applied (assumption type + data freshness)
4. Final conviction % calculation
5. Conviction tier assigned (HIGH/MEDIUM/LOW)

**Format (required in every analysis):**
```
CONVICTION CALCULATION:
- Baseline Conviction: [XX]% (Fundamental + Technical + Quantitative)
- Assumption 1 (Type: Binary): VALIDATED [+0] | PARTIALLY-VALIDATED [-7] | UNVALIDATED [-15]
- Assumption 2 (Type: Durability): VALIDATED [+0] | PARTIALLY-VALIDATED [-5] | UNVALIDATED [-10]
- Assumption 3 (Type: Macro): VALIDATED [+0] | PARTIALLY-VALIDATED [-6] | UNVALIDATED [-12]
- Assumption 4 (Type: Execution): VALIDATED [+0] | PARTIALLY-VALIDATED [-4] | UNVALIDATED [-8]
- Data Freshness Penalty: [0 to -5 points]
- FINAL CONVICTION: [XX]% → Conviction Tier: [HIGH/MEDIUM/LOW]
```

---

## GUARDRAIL B: DATA VALIDATION & FRAMEWORK ALIGNMENT

### B.1 Framework Dependency Verification

**Before ANY analysis execution, verify:**

**Tier 1 - CRITICAL Production Files (Must Be Present):**
- ✓ QUALITY-Guardrails.md v1.4.7+ (this file - master guardrails)
- ✓ Master-Architect.md v1.0.8+ (authority and CEV protocol)
- ✓ ANALYZE.md v1.4.3+ (6-TURN methodology)
- ✓ Stock-Analyst.md v1.4.7+ (5-section report structure)
- ✓ PROMPT_TURN6_ACTIONABLE.md v1.4.3+ (QC Gate requirements)
- ✓ Quality-Control-Checklists.md v1.0+ (mandatory verification)
- ✓ STOCK-ANALYSIS-PRODUCTION.md v1.0+ (operational execution)

**If any CRITICAL file is missing or placeholder:** Analysis cannot execute

**Tier 2 - SUPPORTING Files (Must Be Present or Compatible):**
- ✓ PROMPT_POSITION_MANAGEMENT.md v1.1+ (conviction-tier allocation)
- ✓ PROMPT_ACTIONABLE_FUNDAMENTALS.md v1.1+ (assumption taxonomy)
- ✓ PROMPT-MARKET-ANALYSTS.md v7.6+ (analyst framework)
- ✓ PROMPT_SYNTHESIS_INTEGRATION.md v1.1+ (integration logic)
- ✓ STARTUP-REQUIREMENTS.md v2.1+ (startup validation)

**If Tier 2 files missing:** Analysis can execute but loses feature depth

### B.2 Data Source Validation

**All input data must pass:**

**Recency Check:**
- Market data (prices, volume, technicals): < 1 day old ✓
- Fundamental data (earnings, balance sheet): < 7 days old if recent quarter reported ✓
- Macro data (GDP, employment, rates): < 7 days old ✓
- Analyst consensus: < 30 days old ✓

**Source Credibility Check:**
- Financial data: SEC filings (10-K, 10-Q, 8-K) only for official data
- Market data: Yahoo Finance, Bloomberg, or official exchange data
- Analyst data: Consensus from FactSet, Bloomberg, Yahoo Finance
- Macro data: BLS, Federal Reserve, IMF, OECD official sources
- News: Reuters, Bloomberg, WSJ, or official company sources

**Data Freshness Penalties (applied to conviction):**
- Prices: 0-1 day old → 0 points penalty
- Prices: 2-3 days old → -1 point penalty
- Prices: 4-7 days old → -2 points penalty
- Prices: >7 days old → -3 points penalty (escalate and re-verify)
- Fundamental data: >30 days old (when new data available) → -2 to -3 points

### B.3 Framework Alignment Verification

**Analysis must align with:**

1. **6-TURN Methodology (ANALYZE.md v1.4.3):**
   - All TURNs executed sequentially
   - Gate results documented in Section 12
   - Conviction-cap tier applied per guardrail C

2. **5-Section Report Structure (Stock-Analyst.md v1.4.7):**
   - Section 1: Executive Summary
   - Section 2: Fundamental Analysis  
   - Section 3: Risk Analysis
   - Section 4: Technicals & Catalysts
   - Section 5: Quality Control Gate (mandatory)

3. **Assumption Taxonomy (PROMPT_ACTIONABLE_FUNDAMENTALS.md v1.1):**
   - All assumptions categorized (Binary/Durability/Macro/Execution)
   - Penalties applied per assumption type
   - Conviction-cap tier derived from unvalidated count

4. **Position Sizing (PROMPT_POSITION_MANAGEMENT.md v1.1):**
   - Position size linked to conviction tier
   - Entry strategy matched to conviction level
   - Profit-taking aligned to conviction tier

**Alignment Checklist (must pass before delivery):**
- ☐ All 6 TURNs documented with gate results
- ☐ All 5 sections completed
- ☐ All assumptions categorized by type
- ☐ Conviction % calculated with penalties shown
- ☐ Conviction tier assigned and validated against table
- ☐ Position size matches conviction tier allocation
- ☐ Quality Control Gate (Section 5) completed
- ☐ No contradictions between sections

---

## GUARDRAIL C: CONVICTION-CAP TIER SYSTEM

### C.1 The Conviction-Cap Tier Framework (Core Governance Rule)

**Central Principle:** Unvalidated assumptions create a conviction ceiling. More unvalidated assumptions → lower maximum conviction → lower position sizing.

**Why This Matters:** Prevents overconfidence in event-driven or execution-dependent theses while allowing honest analysis of manageable risks.

### C.2 Conviction-Cap Tier Table (Primary Reference)

**Use this table to determine maximum conviction %:**

| Unvalidated Assumptions | Max Conviction | Conviction Tier | Position Size | Recommendation Type | Rationale |
|---|---|---|---|---|---|
| 0-1 | 70-75% | HIGH | 3-5% portfolio | Standard (BUY/HOLD/SELL) | High confidence; strong evidence across assumptions |
| 2-3 | 55-60% | MEDIUM | 1.5-3% portfolio | SPECULATIVE/CONDITIONAL | Event/execution-dependent; manageable unknowns |
| 4+ | <55% | LOW | 0.5-1.5% or SKIP | WATCH (no BUY/SELL) | Too many unknowns; thesis unproven; watch mode only |

**KEY RULE:** Conviction % cannot exceed the ceiling for your unvalidated assumption count. Even if your evidence is strong, unknowns cap conviction.

**IMPORTANT:** Do NOT hide assumptions to reach higher conviction tier. All assumptions must be disclosed honestly.

### C.3 Assumption Type Taxonomy & Penalties

**Four types of assumptions affect conviction differently:**

| Assumption Type | Definition | Validation Requirement | Conviction Penalty | Examples |
|---|---|---|---|---|
| **Binary** | Either true or false; no middle ground | Definitive evidence (binding agreements, court rulings, regulatory approvals) | -15 points if unvalidated | M&A deal closes, dividend approved, regulatory approval granted |
| **Durability** | Current favorable conditions persist (cycle, margins, growth) | Multi-quarter track record + forward indicators (guidance, industry data) | -10 points if unvalidated | DOCSIS cycle remains strong, margins sustain, market cycle continues |
| **Macro** | Macro environment remains within stable range | Cannot be fully validated; test via scenarios | -12 points if unvalidated | Interest rates stay 4-5%, credit markets function, oil stays $70-90/bbl |
| **Execution** | Company achieves operational/financial targets | Management track record + recent quarterly confirmation | -8 points if unvalidated | Company hits $500M revenue, EBITDA reaches 35%, FCF = $300M |

### C.4 Conviction-Cap Application Process

**Step 1: Identify all key assumptions (max 5)**
- Frame as: "This thesis requires X to be true"
- Document BEFORE gathering evidence (prevents bias)

**Step 2: Validate each assumption**
- Mark: VALIDATED / PARTIALLY-VALIDATED / UNVALIDATED
- Show evidence supporting each status

**Step 3: Count unvalidated assumptions**
- Count only UNVALIDATED (not partially-validated)

**Step 4: Find conviction cap in tier table**
- 0-1 unvalidated → Max 70-75% (HIGH tier)
- 2-3 unvalidated → Max 55-60% (MEDIUM tier)
- 4+ unvalidated → Max <55% (LOW tier)

**Step 5: Calculate final conviction**
- Baseline conviction (evidence quality)
- Minus assumption penalties (sum of all penalty points)
- Equals final conviction % (capped at tier ceiling)

**Step 6: Assign recommendation tier**
- HIGH (70-75%) → Standard BUY/HOLD/SELL
- MEDIUM (55-60%) → SPECULATIVE BUY/CONDITIONAL HOLD
- LOW (<55%) → WATCH (no buy/sell)

### C.5 Case Study 1: COMM (How Guardrail C Changed Analysis)

**Thesis:** CCS deal creates two-part valuation (deal value + RemainCo value)

**Key Assumptions:**
1. CCS deal closes H1 2026 (Binary)
2. RemainCo EBITDA sustains guidance (Durability)
3. Multiple expansion post-deal (Macro)

**Validation Status:**
- Assumption 1: PARTIALLY-VALIDATED (definitive agreement signed, FTC approval pending)
- Assumption 2: VALIDATED (recent quarters confirm guidance)
- Assumption 3: UNVALIDATED (historical precedent but not guaranteed)

**Unvalidated Count:** 1 (Assumption 3 only)

**OLD APPROACH (v1.3 Hard STOP - WRONG):**
- ≥1 unvalidated assumption → Analysis blocked
- Analyst cannot proceed despite manageable risk
- Valid thesis never analyzed

**NEW APPROACH (v1.4.4+ Conviction-Cap Tier - CORRECT):**
- Unvalidated count: 1 → Conviction cap: 70-75% (HIGH tier)
- Base conviction: 72% (strong fundamental + technical case)
- Penalty: -0 points (only 1 assumption, and it's macro-dependent)
- Final conviction: 72% (at cap)
- Recommendation: **SPECULATIVE BUY at 72% conviction**
- Position sizing: 3-4% portfolio (HIGH tier)
- Disclosure: "Thesis assumes multiple expansion post-deal; historical precedent exists but macro-dependent. Analyst 72% confident despite macro unknown."

**Key Learning:** Guardrail C allows event-driven analysis while maintaining rigor through transparent conviction-cap tiers.

### C.6 Case Study 2: STRL (Execution-Focused Thesis)

**Thesis:** Lithium production ramp + margin expansion post-tariff normalization

**Key Assumptions:**
1. Production ramp hits targets (Execution)
2. Margins sustain post-tariff (Durability)  
3. Lithium cycle remains intact (Macro)

**Validation Status:**
- Assumption 1: VALIDATED (third-party engineering confirmed)
- Assumption 2: PARTIALLY-VALIDATED (guidance given, peer recovery confirmed)
- Assumption 3: UNVALIDATED (market-dependent, macro-driven)

**Unvalidated Count:** 1 (Assumption 3 only)

**Conviction Calculation:**
- Base conviction: 71% (production ramp proven, margin trend positive)
- Penalty for Assumption 3 (Macro, unvalidated): -0 points (only 1 unvalidated, within cap)
- Data freshness: 0 points (all data <7 days)
- Final conviction: 71%
- Conviction tier: HIGH (70-75%)
- Recommendation: **BUY at 71% conviction**
- Position sizing: 3-4% portfolio
- Disclosure: "Thesis depends on lithium cycle remaining intact through 2026. Execution risk is low; macro risk is manageable."

### C.7 Assumption Validation Requirements by Type

**BINARY Assumptions (Deal/Approval/On-Off):**
- Requires definitive evidence only
- Examples: Binding agreement signed, regulatory approval granted, court ruling issued
- If evidence missing: Mark UNVALIDATED, apply -15 point penalty
- Mitigation: Run scenarios (deal closes vs. doesn't) with probabilities

**DURABILITY Assumptions (Cycle/Performance Persists):**
- Requires multi-quarter track record + forward indicators
- Examples: DOCSIS cycle sustains, margin rates hold, market cycle continues
- If evidence partial: Mark PARTIALLY-VALIDATED, apply -5 point penalty
- If evidence missing: Mark UNVALIDATED, apply -10 point penalty
- Mitigation: Track leading indicators; test in bull/bear scenarios

**MACRO Assumptions (Rates/Credit/Commodities):**
- Cannot be fully validated; test via scenario analysis only
- Examples: Rates stay 4-5%, credit markets function, oil $70-90
- Mark UNVALIDATED if not stress-tested
- Always apply -12 point penalty if unvalidated
- Mitigation: Run scenarios with macro stress (+200bps rates, -30% commodities)

**EXECUTION Assumptions (Company Hits Targets):**
- Requires management track record + recent quarterly confirmation
- Examples: Company hits guidance, EBITDA margins reach 35%, FCF = $300M
- If track record strong + guidance confirmed: Mark VALIDATED
- If guidance given but not yet confirmed: Mark PARTIALLY-VALIDATED, -4 points
- If no track record or miss recent guidance: Mark UNVALIDATED, -8 points
- Mitigation: Monitor quarterly; update conviction as targets confirmed/missed

---

## GUARDRAIL D: DATA FRESHNESS & CRITICAL EVENT VERIFICATION

### D.1 Critical Event Verification (CEV) Protocol - Master-Architect.md v1.0.8

**Purpose:** Prevent strategic decisions based on incorrect or rescheduled event dates.

**Mandatory Before Any Event-Based Decision:**

1. **Verify Date:** Execute `search_web` with query: `"[Event Name] release date [Month Year]"`
2. **Extract Date:** Read from official source (BLS, Fed, SEC)
3. **Output First:** State verified date in FIRST sentence
   - Example: "The November 2025 NFP report is scheduled for release on Tuesday, Dec 16, 2025, at 8:30 AM ET."
4. **Proceed:** Continue with analysis using verified date as anchor

**Prohibited:**
- Heuristics ("First Friday", "Third Week")
- Assumptions without verification
- Proceeding with analysis before date confirmation

### D.2 Weekly Economic Calendar Audit

**Master Architect maintains verified "Event Horizon" of:**
- Nonfarm Payrolls (NFP)
- Consumer Price Index (CPI)
- FOMC Meeting dates
- Major portfolio-relevant earnings reports

**Event Horizon must be refreshed:**
- Weekly (every Monday or first turn of week)
- Before major portfolio decisions
- If user issues Trigger Intent ("Confirm critical events")
- If event date >7 days in future (re-verify 48 hours prior)

**Format (required in portfolio decisions):**

| Event | Date | Time | Source | Status |
|-------|------|------|--------|--------|
| NFP (Nov 2025) | 2025-12-16 | 8:30 AM ET | BLS | ✅ Verified |
| CPI (Nov 2025) | 2025-12-11 | 8:30 AM ET | BLS | ✅ Verified |
| FOMC Meeting | 2025-12-10 | 2:00 PM ET | Federal Reserve | ✅ Verified |

### D.3 Catalyst Date Verification for Stock Analysis

**All near-term catalysts in stock analysis must:**
1. Be date-verified via CEV protocol
2. Include verified date in first mention
3. Disclose source (SEC filings, company guidance, industry sources)
4. Show "Confirmed" vs. "Estimated" label

**Format (required in Section 4 Catalysts):**
- Event: Q4 2025 Earnings
- Verified Date: January 28, 2026 (confirmed via SEC 10-K timeline)
- Time: After market close
- Potential Impact: +10-15% if beats, -10% if misses

### D.4 Data Freshness Standards

**Pricing & Market Data:**
- Must be < 1 day old (daily refresh minimum)
- >3 days old → Escalate and re-verify before strategic decision
- Penalty: -1 to -3 points conviction per stale data source

**Fundamental Data:**
- Audited financials: SEC 10-K/10-Q only
- Non-audited: Guidance + recent quarters
- >30 days old (when new data available) → Escalate
- Penalty: -2 to -3 points conviction

**Analyst Consensus:**
- Must be < 30 days old
- >60 days old → Re-verify or flag as outdated
- Penalty: -1 to -2 points conviction

**Macro Data:**
- BLS data: < 7 days old (employment, inflation weekly)
- Fed data: < 14 days old (rates, policy updates)
- >30 days old → Re-verify before macro assumptions
- Penalty: -2 to -5 points conviction

### D.5 Data Freshness Disclosure (Required in All Reports)

**Every analysis must include:**

**DATA FRESHNESS AUDIT:**
- Stock prices: Updated [DATE], [X] days old → [penalty] points
- Fundamental data: Last reported [DATE], [X] days old → [penalty] points
- Analyst consensus: Updated [DATE], [X] days old → [penalty] points
- Macro data: Latest report [DATE], [X] days old → [penalty] points
- Catalyst dates: CEV verified [DATE] → Confirmed or Estimated
- **Total Data Freshness Penalty: [X] conviction points**

**Freshness Escalations (Hard Stops):**
- Stock price >7 days old → Cannot proceed without re-pricing
- Fundamental data >60 days old (with new data available) → Cannot proceed; must update
- Catalyst dates not CEV-verified → Cannot proceed with event-based recommendation

### D.6 Governance: Data Freshness Review Frequency

**Analyses are fresh for:**
- Same-day decisions (24 hours)
- Multi-day positions (3-5 days before data refresh needed)
- Weekly reviews (5-7 days)
- Monthly reviews (14-30 days)

**Refresh Triggers:**
- Any analyst upgrade/downgrade (immediate re-check)
- Earnings release (immediate refresh)
- Major macro data (NFP, CPI - immediate refresh)
- Portfolio decision gates (before rebalancing)
- User request ("Check if this is still valid")

**Stale Analysis Response:**
- If analysis >7 days old and user requests decision: Respond with "Analysis last updated [DATE]. Recommend refreshing data ([X] days old). Would you like me to update?"
- Do not recommend portfolio action on stale data

### D.7 Compliance Checklist for Guardrail D

**Before Finalizing Any Analysis:**
- ☐ All event/catalyst dates verified via CEV protocol
- ☐ Verified dates stated in FIRST mention in analysis
- ☐ Sources confirmed (SEC, BLS, Fed, official)
- ☐ Data Freshness Audit section completed
- ☐ Data freshness penalties calculated and shown
- ☐ Final conviction % reflects freshness deductions
- ☐ Stale data flagged (>7 days old) with escalation
- ☐ Macro assumptions validated against latest data
- ☐ Event Horizon current (< 7 days old)

---

## GUARDRAIL E: FRAMEWORK COMPLIANCE & NON-NEGOTIABLE STANDARDS

### E.1 Mandatory Compliance Requirements

**All analyses must meet (zero exceptions):**

1. **Plain English (95% minimum)**
   - Every technical term explained inline on first use
   - Jargon minimized; accessible to non-specialist readers
   - Examples given for complex concepts

2. **Confidence Tagging (90% minimum)**
   - All quantitative claims tagged: [confidence: HIGH/MEDIUM/LOW]
   - All estimates labeled with methodology
   - Uncertain projections marked [ESTIMATE] or [SCENARIO]

3. **Gate Transparency (100%)**
   - Section 12: TURN 4 Self-Critique shows all validation gate results
   - Gate pass/fail status documented for each gate
   - Guardrail compliance score shown (target: 95%+)

4. **6-TURN Execution (100%)**
   - All TURNs executed sequentially (not skipped)
   - Each TURN output integrated into final report
   - Gate results documented in Section 12

5. **5-Section Report (100%)**
   - Section 1: Executive Summary (with conviction %, tier, position size)
   - Section 2: Fundamental Analysis (with assumption taxonomy)
   - Section 3: Risk Analysis (with conviction-cap tier rationale)
   - Section 4: Technicals & Catalysts (with CEV-verified dates)
   - Section 5: Quality Control Gate (mandatory 4-part verification)

6. **Assumption Transparency (100%)**
   - All assumptions identified and typed
   - Validation status for each assumption shown
   - Conviction penalties calculated and explained
   - Conviction-cap tier justified by unvalidated count

7. **Position Sizing Linked to Conviction (100%)**
   - Position size derived from conviction tier table
   - Entry strategy matched to conviction level
   - Rationale shows conviction → allocation logic

8. **Quality Control Gate (100%)**
   - Section 5: 4-part mandatory verification
   - All parts must pass before delivery
   - No exceptions; if any part fails, analysis halted

### E.2 Non-Negotiable Guardrail Compliance Standards

**Violation of these standards = Analysis rejected:**

| Standard | Requirement | Violation = |
|----------|-----------|---|
| Conviction Calculation | Must show formula: Base - Penalties = Final % | FAIL |
| Conviction-Cap Tier | Final % cannot exceed tier ceiling based on unvalidated count | FAIL |
| Assumption Disclosure | All assumptions must be identified, typed, validated | FAIL |
| CEV Dates | All catalyst/event dates must be verified, source confirmed | FAIL |
| Data Freshness Audit | All data sources dated, penalties calculated | FAIL |
| Position Sizing | Position % must match conviction tier allocation table | FAIL |
| Section 5 QC Gate | All 4 parts: Framework-Dependencies, Deletion-Approval, Backtesting, Self-Consistency | FAIL |
| Plain English | 95% minimum accessibility; technical terms explained | FAIL |
| Confidence Tagging | 90% minimum; all claims tagged [confidence:LEVEL] | FAIL |

---

## GUARDRAIL APPLICATION CHECKLIST

**Required before ANY analysis delivery:**

**GUARDRAIL A - Conviction Assessment:**
- ☐ Conviction baseline established (0-100%)
- ☐ All assumptions identified and typed (Binary/Durability/Macro/Execution)
- ☐ Each assumption validated (VALIDATED/PARTIALLY-VALIDATED/UNVALIDATED)
- ☐ Penalties calculated per assumption type
- ☐ Final conviction % shown with calculation steps
- ☐ Conviction documented in first sentence of recommendation

**GUARDRAIL B - Data Validation & Framework Alignment:**
- ☐ All CRITICAL framework files present (no placeholders)
- ☐ All data sources verified as current
- ☐ All data sources verified as credible
- ☐ Analysis aligns with 6-TURN methodology
- ☐ Analysis aligns with 5-section report structure
- ☐ Analysis aligns with assumption taxonomy
- ☐ Analysis aligns with position sizing rules

**GUARDRAIL C - Conviction-Cap Tier System:**
- ☐ Unvalidated assumption count documented
- ☐ Conviction-cap tier identified from table
- ☐ Final conviction % does not exceed tier ceiling
- ☐ Recommendation tier assigned (HIGH/MEDIUM/LOW/WATCH)
- ☐ Position size derived from conviction tier table
- ☐ Entry strategy matched to conviction level

**GUARDRAIL D - Data Freshness & CEV:**
- ☐ All event/catalyst dates CEV-verified
- ☐ Verified dates cited with source
- ☐ Data Freshness Audit completed
- ☐ Freshness penalties calculated and applied
- ☐ Final conviction reflects freshness deductions
- ☐ Macro assumptions stress-tested

**GUARDRAIL E - Framework Compliance:**
- ☐ Plain English 95%+ (technical terms explained)
- ☐ Confidence tagging 90%+ ([confidence:LEVEL])
- ☐ Gate transparency: Section 12 shows validation results
- ☐ All 6 TURNs executed with results documented
- ☐ All 5 sections completed
- ☐ Section 5 Quality Control Gate: All 4 parts pass
- ☐ Position sizing shows conviction → allocation logic

---

## FINAL COMPLIANCE SCORE

**Framework Guardrails Compliance (Must achieve 95%+):**

| Guardrail | Compliance Requirement | Status |
|-----------|---|---|
| Guardrail A: Conviction Assessment | All assumptions typed, penalized, conviction calculated | ✓ PASS if fully documented |
| Guardrail B: Data Validation | Framework files present, data current, alignment verified | ✓ PASS if all files present |
| Guardrail C: Conviction-Cap Tier | Unvalidated count → tier ceiling → position size | ✓ PASS if tier respected |
| Guardrail D: Data Freshness & CEV | Dates verified, freshness audited, penalties applied | ✓ PASS if disclosed |
| Guardrail E: Framework Compliance | Plain English, confidence tagging, 6-TURNs, 5-sections, QC Gate | ✓ PASS if all sections complete |

**Overall Framework Compliance Score Target:** 95%+

**Analysis Delivery Status:**
- 95%+ compliance → Approved for delivery (Institutional Grade)
- 85-94% compliance → Approved with notes (Minor gaps noted)
- <85% compliance → Rejected (Major gaps; resubmit after corrections)

---

## VERSION HISTORY

| Version | Date | Change Summary | Status |
|---------|------|---|---|
| 1.4.1 | Nov 20, 2025 | Initial Guardrails Framework (A, B, C baseline) | Superseded |
| 1.4.2 | Nov 24, 2025 | Guardrail C refinement - conviction-cap tier system added | Superseded |
| 1.4.3 | Nov 28, 2025 | Guardrail D v1.0 - Data Freshness & CEV protocol added | Superseded |
| 1.4.4 | Dec 1, 2025 | Guardrails A-D complete with case studies (COMM, STRL) | Superseded |
| 1.4.5 | Dec 3, 2025 | Version control audit; clarified framework dependencies | Superseded |
| 1.4.6 | Dec 5, 2025 | All guardrails A-D consolidated; Guardrail D.1 CEV protocol integrated | Superseded |
| **1.4.7** | **Dec 7, 2025** | **Framework recovery - all guardrails recovered from dependent files; version incremented** | **Active** |

---

**END OF QUALITY-GUARDRAILS v1.4.7**

**Status:** Production Ready - All Guardrails A-D Complete  
**Last Updated:** December 7, 2025  
**Recovery Authority:** Master Architect (Recovery Phase 1)  
**Approval:** Master Architect v1.0.8  
**Next Update:** Dec 14, 2025 (scheduled review)