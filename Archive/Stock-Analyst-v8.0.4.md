# Stock-Analyst.md v8.0.4 - UPDATED WITH ACTIONABLE REPORTING FRAMEWORK

**Version:** 8.0.4  
**Updated:** December 8, 2025, 7:32 PM MST  
**Status:** COMPLETE - Actionable Reporting Integrated into TURN 6  
**Change Summary:** Stock-Analyst TURN 6 now outputs 4-section actionable report (1-page format primary + optional appendix)

---

## TURN 6 – OUTPUT GENERATION (UPDATED - Actionable Reporting Framework Integrated)

**Objective:** Generate 4-section actionable report ready for immediate portfolio decision.

**Format:** 3-4 page primary report (Sections 1-4) + Optional appendix (TURN 1-5 detailed work)

---

### SECTION 1: EXECUTIVE SUMMARY (1 page)

**Template per PROMPT_EXECUTIVE_SUMMARY.md v1.0**

**Required Elements:**

| Element | Format | Example |
|---------|--------|---------|
| **Recommendation** | BUY \| HOLD \| SELL | BUY |
| **Confidence Score** | XX% (0-100%) | 85% |
| **Current Price** | $X as of [DATE] | $15.96 as of Dec 8 |
| **Current Position** | Y% of portfolio or N/A | 0% (new opportunity) |
| **Position Status** | ↑ (up) / → (flat) / ↓ (down) / - (none) | - (new) |
| **Base Case Price Target** | $X (+YY%) [ZZ% probability] | $21.67 (+35.8%) [55%] |
| **Bull Case Target** | $X (+YY%) [ZZ% probability] | $26.00 (+62.8%) [25%] |
| **Bear Case Target** | $X (-YY%) [ZZ% probability] | $14.47 (-9.4%) [20%] |
| **Weighted Expected Return** | +YY% | +33.1% |
| **Immediate Action** | [Specific verb + timing] | BUY 2-4% at $15.96-16.20 |
| **Entry Price Range** | $X-$Y (specific, not "near support") | $15.96-$16.20 |
| **Position Size** | Z% maximum | 4% max |
| **Time Horizon** | X months/years | 12 months |
| **Thesis Driver 1** | [Specific fact with number] | 7th year supply deficit, Q3 FCF +447% YoY |
| **Thesis Driver 2** | [Specific fact with number] | Rochester +81% production coming online |
| **Thesis Driver 3** | [Specific fact with number] | -14.8% from peak, RSI 31.2 (bottom signal) |
| **Decision Trigger 1** | 🚨 [Event that invalidates thesis] | Silver breaks $47/oz sustained |
| **Decision Trigger 2** | 🚨 [Event that changes outlook] | Q4 earnings miss 2026 guidance |
| **Decision Trigger 3** | 🚨 [Event that requires exit] | Hard support breaks $14.47 |

**Quality Gates (must pass before proceeding to Section 2):**
- ☐ Recommendation is FIRST (not buried)
- ☐ Recommendation is one word: BUY/HOLD/SELL
- ☐ Confidence % is explicit (0-100, not qualitative)
- ☐ Price targets are SPECIFIC (exact dollars, not ranges)
- ☐ All three scenarios have explicit probabilities (sum to 100%)
- ☐ Expected return is CALCULATED (not vague)
- ☐ Action verb is CLEAR (BUY/ADD/HOLD/SELL + specific price)
- ☐ Position size is stated as % of portfolio
- ☐ Three thesis drivers have SPECIFIC NUMBERS
- ☐ Three decision triggers are IMMEDIATELY RECOGNIZABLE

---

### SECTION 2: TECHNICAL SETUP (0.5-1 page, condensed)

**Extracted from TURN 3 (QMA Momentum Analysis)**

**Required Elements:**

| Element | Content | Example |
|---------|---------|---------|
| **Chart Setup** | Current trend + key levels | Above 200-day MA, RSI 58 (rising) |
| **Support Level** | Nearest major support | $14.47 (fibonacci + consolidation) |
| **Resistance Level** | Nearest major resistance | $17.50 (previous range high) |
| **Momentum Signal** | Positive / Neutral / Negative | POSITIVE (price above MA, RSI >50) |
| **Entry Zone** | Specific technical level | $15.96-16.20 (breakout confirmation + support) |
| **Stop Loss** | Risk management | $14.30 (below support consolidation) |
| **Risk/Reward Ratio** | Entry to target / stop | 2.8:1 (basis point target $21.67) |
| **Technical Divergence** | Any bearish signals? | None (price making new highs, volume increasing) |

**Quality Gates:**
- ☐ Technical setup supports recommendation (not contradictory)
- ☐ Entry zone aligns with Section 1 entry price
- ☐ Stop loss level explicitly stated
- ☐ Risk/reward ratio calculated
- ☐ No conflicting signals

---

### SECTION 3: FUNDAMENTAL CASE (0.75-1 page, condensed)

**Extracted from TURN 2 (FMA Fundamental Analysis)**

**Required Elements:**

| Element | Content | Example |
|---------|---------|---------|
| **Earnings Story** | Revenue + EPS growth thesis | Q3 FCF +447% YoY; Net income $266.8M |
| **Valuation Assessment** | P/E, EV/EBITDA, relative to peers | P/E 18.2x vs sector 22.1x (15% discount) |
| **Competitive Position** | Market share + competitive advantages | #2 silver producer globally; lower all-in costs |
| **Growth Catalysts** | Near-term drivers (next 12 months) | Rochester mine ramp (+81% Au), Las Chispas FID |
| **Financial Health** | Balance sheet strength | Net cash position; 3.59x current ratio (fortress) |
| **Valuation vs Consensus** | vs Wall Street target | Trading $15.96 vs consensus $19.50 target |
| **Key Risks** | What could derail thesis? | Silver price <$45/oz, mine permitting delays |

**Quality Gates:**
- ☐ Earnings story supported by actual numbers
- ☐ Valuation justified vs peers (not just absolute level)
- ☐ Catalysts dated and specific (not vague)
- ☐ Balance sheet supports thesis (not weak)
- ☐ Risks are specific and material (not hand-wavy)

---

### SECTION 4: POSITION MANAGEMENT (0.5 page)

**Extracted from PROMPT_POSITION_MANAGEMENT.md v1.x**

**Required Elements:**

| Element | Content | Example |
|---------|---------|---------|
| **Entry Strategy** | Phased or immediate? | Phased: 2% at $15.96, +1% at $15.00, +1% at $14.50 |
| **Conviction Tier** | HIGH / MEDIUM / LOW | HIGH (85% confidence) |
| **Entry Price** | Specific buy price(s) | $15.96-16.20 (market order) or $15.00 (scale) |
| **Initial Position Size** | Starting % of portfolio | 2% initial, scale to 4% max |
| **Profit-Taking Levels** | When to trim/sell | Take 25% profits at $21.67 (base case) |
| **Profit-Taking Price 2** | Second level | Take another 25% at $24.00 |
| **Stop Loss Level** | Hard stop below this price | $14.30 (below support) |
| **Stop Loss Reason** | Why exit at this level? | Invalidates technical setup (200-day MA break) |
| **Max Loss per Position** | Portfolio impact if stopped | Max 2% portfolio loss if stopped at $14.30 |
| **Time Stop** | Exit if no movement by when? | Reassess on Q4 earnings (Jan 2026) if no move |
| **Conviction Required for Hold** | Minimum conviction to stay | 70% conviction minimum to hold |

**Quality Gates:**
- ☐ Entry strategy is SPECIFIC (prices, amounts, timing)
- ☐ Profit-taking levels are DEFINED (not vague)
- ☐ Stop loss is HARD number (not judgment call)
- ☐ Position sizing makes sense given conviction
- ☐ Exit rules are clear (user knows exactly when to sell)

---

### SECTION 5: QUALITY CONTROL GATE (Mandatory - Per QUALITY-Guardrails.md v1.4.7)

**4-Part Verification (all must PASS before delivery):**

#### Part 1: Framework Dependencies Verified
- ☐ All CRITICAL files present (Stock-Analyst.md, ANALYZE.md, Quality-Control-Checklists.md)
- ☐ No placeholder files used
- ☐ All data sources verified as current (pricing ≤1 day, fundamentals ≤7 days)
- ☐ Analyst consensus data ≤30 days old
- ☐ All external links functional

#### Part 2: Assumption Disclosure Complete
- ☐ All key assumptions identified (minimum 3, max 5)
- ☐ Each assumption typed: Binary / Durability / Macro / Execution
- ☐ Validation status documented: VALIDATED / PARTIALLY / UNVALIDATED
- ☐ Conviction penalties calculated per assumption type
- ☐ Conviction-cap tier justified by unvalidated count
- ☐ Final conviction % shown with calculation steps

#### Part 3: Backtesting & Conviction Validation
- ☐ Thesis assumptions backtested against historical scenarios
- ☐ Conviction score validated against historical precedent (if applicable)
- ☐ Return targets aligned with historical outcomes in similar scenarios
- ☐ Risk levels realistic (not optimistic)
- ☐ Entry points backtested for reliability (not cherry-picked)

#### Part 4: Self-Consistency Check
- ☐ All 5 sections aligned (no contradictions)
- ☐ Recommendation consistent with conviction %
- ☐ Position size matches conviction tier table
- ☐ Entry price supports recommendation
- ☐ Risk/reward ratio supports recommendation
- ☐ All trigger levels internally consistent

**If ANY part FAILS:**
- STOP delivery
- Fix failures
- Re-verify all 4 parts before proceeding
- Escalate if unfixable issues detected

**If ALL parts PASS:**
- ✅ Report APPROVED
- ✅ Ready for external QA Engineer
- ✅ Ready for portfolio action

---

## OPTIONAL APPENDIX: DETAILED TURN 1-5 ANALYSIS

**On Request:** Full TURN 1-5 work can be provided as appendix (10-20 pages)

**Contents:**
- TURN 1: Expert pool responses (FMA, IRM, MSPA, QMA, MMA, MQAA)
- TURN 2: Complete fundamental analysis with all metrics
- TURN 3: Technical + sentiment deep dive with charts
- TURN 4: De-conflicting matrix and weighting calculation
- TURN 5: Position sizing formula with all multipliers

**Standard Deliverable:** Sections 1-4 only (4-page report)

---

## TURN 6 WORKFLOW (Updated)

**Step 6A – Generate Section 1-4 Report**
1. Extract Executive Summary from TURN 1 conviction + expert consensus
2. Extract Technical Setup from TURN 3 QMA output
3. Extract Fundamental Case from TURN 2 FMA output
4. Extract Position Management from TURN 5 sizing + conviction tier
5. Verify all sections internally consistent

**Step 6B – Execute Quality Control Gate (4 parts)**
1. Verify framework dependencies
2. Verify assumption disclosure complete
3. Verify backtesting + conviction validation
4. Verify self-consistency across all 5 sections

**Step 6C – If ALL gates PASS:**
- Generate final report PDF (Sections 1-4)
- Prepare MQAA audit trail
- Handoff to external QA Engineer

**Step 6D – If ANY gate FAILS:**
- Document failures
- Fix root causes
- Re-verify gates
- If unfixable, escalate to Master Architect

---

## OUTPUT FILES

**Primary Deliverable:**
- `Stock-Analysis-Report-[TICKER]-[DATE].md` (Sections 1-4, 4 pages)

**Supporting Deliverables:**
- `SA-ExecutiveSummary-[TICKER].md` (Section 1 only, 1 page, can be used standalone)
- `SA-Output-v8.0.4.json` (machine-readable, includes MQAA audit trail)

**Optional:**
- `SA-DetailedAnalysis-[TICKER]-APPENDIX.md` (TURN 1-5 detailed work, 10-20 pages on request)

---

## QUALITY CHECKLIST FOR TURN 6 OUTPUT

**All Sections:**
- ☐ Written in plain English (95%+ accessibility)
- ☐ All technical terms explained on first use
- ☐ All quantitative claims tagged [confidence: HIGH/MEDIUM/LOW]
- ☐ No hedging language ("could be", "may be", "might be")
- ☐ Action-oriented (what user should DO, not what might happen)

**Section 1 (Executive Summary):**
- ☐ Recommendation is FIRST (not buried)
- ☐ Recommendation is one word: BUY/HOLD/SELL
- ☐ Three price scenarios with explicit probabilities
- ☐ Specific entry price(s) stated
- ☐ Three thesis drivers have numbers
- ☐ Three decision triggers are actionable

**Section 2 (Technical):**
- ☐ Chart setup described with current levels
- ☐ Support/resistance levels explicit (not approximate)
- ☐ Entry zone aligned with Section 1
- ☐ Risk/reward ratio calculated

**Section 3 (Fundamental):**
- ☐ Earnings story supported by actual numbers
- ☐ Valuation justified vs peers
- ☐ Catalysts dated and specific
- ☐ Balance sheet discussed (debt/cash position)

**Section 4 (Position Management):**
- ☐ Entry strategy is specific (prices and amounts)
- ☐ Profit-taking levels defined
- ☐ Stop loss is hard number (not judgment)
- ☐ Exit rules are clear

**Section 5 (QC Gate):**
- ☐ All 4 parts documented with PASS/FAIL status
- ☐ Any failures fixed and re-verified
- ☐ Final approval signature present

---

## INTEGRATION NOTES

**v8.0.4 Changes from v8.0.3:**
- TURN 6 output restructured into 4-section actionable format
- Executive Summary now leads (Section 1, 1 page)
- Detailed sections 2-4 condensed for readability
- Quality Control Gate formalized (Section 5, mandatory)
- Optional detailed appendix available on request

**Backward Compatibility:**
- All TURN 1-5 workflows unchanged
- TURN 6 output now more actionable (4-page format vs longer traditional report)
- MQAA audit trail integrated into final output JSON

**Next Framework Version:**
- v8.0.5 will explore automated Section generation from TURN outputs
- v8.0.5 will add real-time sentiment weighting

---

**Stock-Analyst.md v8.0.4 - COMPLETE**

Ready for external QA Engineer validation and portfolio decision-making.

