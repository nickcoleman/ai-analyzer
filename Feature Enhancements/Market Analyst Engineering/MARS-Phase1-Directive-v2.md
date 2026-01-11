# MARKET-ANALYST v8.1.0 PHASE 1 DIRECTIVE — REVISED
## Specification Development for Human-Readable Market Intelligence

**FROM:** Design Authority (Master-Architect v8.1.0)  
**TO:** MARS Thread (NEW)  
**DATE:** December 29, 2025, 11:20 AM MST  
**VERSION:** 2.0 (Revised for architectural clarity)  
**FRAMEWORK:** v8.1.0 (v8 only, no v9)  
**STATUS:** ACTIVE - APPROVED FOR EXECUTION

---

## SECTION 1: PURPOSE STATEMENT (CRITICAL)

### What Is Market-Analyst?

**Market-Analyst produces ONE deliverable: human-readable Markdown market intelligence reports.**

That is the ONLY output that matters. No JSON. No data structures. No "optional outputs for other systems."

```
MARKET DATA → [Market-Analyst] → MARKDOWN REPORT (human reads this)
                (Standalone)              ↓
                                   Human makes decisions
```

**Definition**: MA analyzes macroeconomic conditions and sector dynamics, classifies market regimes, ranks sectors, identifies themes, and produces a professional Markdown report that answers:

> "What is the current market regime? How confident are we? Which sectors are attractive? What should we watch?"

**Humans read this report and decide what to do.** That's it. Everything else is implementation detail.

---

## SECTION 2: CORE FUNCTIONS

Market-Analyst executes FIVE steps to produce ONE report:

### **STEP 1: Macro Regime Assessment**
- Collect 8 specific economic indicators (specified below)
- Classify regime: BULLISH, NEUTRAL, BEARISH, or CRISIS
- Calculate confidence (0-100%) in regime call
- Identify top 3 drivers (explain why this regime)
- Identify top 3 risks (what could break this regime)

### **STEP 2: Sector Attractiveness Ranking**
- Score all 11 GICS sectors on market attractiveness
- Rank 1-11 (most attractive to least)
- Identify rotation signals

### **STEP 3: Market Context Synthesis**
- Identify 3-4 key market themes (strategic patterns)
- Connect macro regime to sector opportunities

### **STEP 4: Risk Assessment**
- Summarize key downside scenarios
- Estimate probabilities and timelines

### **STEP 5: Markdown Report Generation**
- Format Sections 1-4 into professional Markdown document
- Readable by human decision-makers
- Actionable and data-driven

---

## SECTION 3: CRITICAL ARCHITECTURAL PRINCIPLES

### Principle 1: Complete Independence

**Market-Analyst operates with ZERO external dependencies.**

- Does not depend on Stock-Analyst
- Does not depend on Portfolio-Analyst
- Does not depend on Alpha-Picks-Analyst
- Does not depend on Portfolio-Orchestrator
- Does not read constraint files
- Does not coordinate with other systems

**Test of Independence**: Remove all other analyst systems. MA produces identical output.

If output changes: FAILURE — MA has hidden dependency.

### Principle 2: Portfolio-Agnostic Analysis

**Portfolio state NEVER influences market analysis.**

Portfolio.md may be read for THESE fields ONLY:
- `user_profile.risk_tolerance` (for display context: "analysis for conservative investors")
- `sector_list` (which GICS sectors to report on — typically all 11)

Portfolio.md fields MA MUST NEVER read:
- ❌ Current holdings or positions
- ❌ Sector allocation percentages or weights
- ❌ Cash available or constraints
- ❌ Rebalancing thresholds
- ❌ Portfolio P&L or performance data
- ❌ Any field representing portfolio STATE

**Test of Portfolio-Agnostic**: 
- Run MA at Time T1 with portfolio state A (MSFT 10%, TLT 5%, SLV 3%)
- Run MA at Time T2 with portfolio state B (MSFT 5%, TLT 10%, SLV 1%), same market data
- Expected: **MA outputs identical** (market conditions unchanged, portfolio changed)
- If outputs differ: FAILURE — MA is portfolio-dependent

---

## SECTION 4: THE 11 KEY QUESTIONS FOR MARS

**MARS must answer these 11 questions to produce the specification. (Note: Question 13 from v1.0 removed — MA does not produce JSON.)**

### **Q1: What Defines Each Regime Type?**

Provide clear, unambiguous definitions for:
- **BULLISH** — What specific economic conditions? (GDP growth rate, unemployment level, Fed stance, etc.)
- **NEUTRAL** — What equilibrium state?
- **BEARISH** — What stress conditions?
- **CRISIS** — What extreme conditions?

### **Q2: Which 8 Macro Indicators Determine Regime?**

Select exactly 8 indicators from this list (or propose alternatives with justification):
- Federal Funds Rate (Fed policy, yield curve)
- Inflation (CPI YoY, PCE)
- GDP Growth (real growth rate)
- Unemployment (rate, trend)
- Credit Spreads (HY OAS, investment grade)
- Yield Curve (10Y-2Y slope)
- ISM Manufacturing PMI
- Dollar Index (DXY)

For each indicator selected:
- Specify data source
- Define bullish/neutral/bearish thresholds
- Specify maximum acceptable data age (see Section 7: Data Freshness Requirements)

### **Q3: How Do You Resolve Conflicting Macro Signals?**

When indicators point different directions (e.g., rates rising = bearish, but earnings accelerating = bullish):
- How do you weight conflicting signals?
- What consensus threshold triggers a regime call?
- How do you handle 4-4 splits (half bullish, half bearish)?
- How do you handle crisis indicators?

Provide pseudocode algorithm.

### **Q4: How Is Regime Confidence Calculated (0-100%)?**

Define the formula for confidence:
- Base: How many indicators align with regime?
- Adjustments: Signal strength, data freshness penalties, conflicting signals?
- Floor: Minimum publishable confidence (see Section 8: Confidence Rules)
- Ceiling: Maximum confidence (never 100%)

Provide pseudocode. Include examples (e.g., "6-of-8 indicators aligned, all fresh data = confidence 82%").

### **Q5: What Drivers Explain the Regime?**

For each regime call, identify top 3 drivers (factors):

Example:
```
Regime: BULLISH (Confidence: 82%)
Drivers:
  1. Fed Rate Decline (Rate now 4.25%, down from 4.33% — bullish for equities)
  2. Earnings Acceleration (S&P 500 EPS +12% YoY vs +6% last quarter)
  3. Inflation Moderating (CPI 3.1% vs 3.5% prior month — supports Fed cuts)
```

How do you identify, rank, and score key drivers? Provide algorithm.

### **Q6: What 4-6 Dimensions Define Sector Attractiveness?**

Define market-based dimensions (NOT portfolio-based):
- Valuation (e.g., P/E relative to market)
- Growth (e.g., earnings growth rate)
- Momentum (e.g., relative strength vs market)
- Cyclicality (e.g., alignment with macro regime)
- Quality (e.g., profitability, margins)
- Yield (e.g., dividend + buyback yield)

Select 4-6 dimensions. Justify why each matters.

### **Q7: How Do You Score Each Sector's Attractiveness?**

For each of 11 GICS sectors:
- Calculate score for each dimension (e.g., 1-10 scale)
- Combine into single attractiveness score
- Define weighting (which dimensions matter most?)

Provide algorithm. Include worked example (e.g., Technology sector scoring 8.33/10).

### **Q8: How Are All 11 Sectors Ranked (1-11)?**

Define the ranking logic:
- Sort by attractiveness score (descending)
- Handle ties
- Apply any macro regime multipliers (e.g., BULLISH favors Tech, BEARISH favors Healthcare)
- Consider concentration risk (if Mag7 dominates, flag it)

Provide algorithm. Include example ranking for BULLISH regime.

### **Q9: What Sector Rotation Signals Matter?**

Define signals that indicate sectors are rotating:
- Rate changes → growth vs value rotation?
- Earnings divergence → growth leaders change?
- Yield curve flattening → defensive demand?
- Credit stress → flight to quality?

List 3-4 key rotation signals you monitor.

### **Q10: How Do You Identify Market Themes Programmatically?**

Define patterns that count as "market themes" (strategic insights):

Example themes:
- "AI-driven Technology outperformance" (Mag7 earnings +18%, leading market)
- "Financial sector beneficiary of higher rates" (NIM expansion, rates stable)
- "Defensive rotation pressure" (Utilities underperforming, valuations attractive)

How do you algorithmically identify these from macro + sector data? Provide logic.

### **Q11: What Is the Markdown Report Format?**

Define the professional Markdown report structure:

**Suggested sections:**
1. **Executive Summary** (1 paragraph) — Regime, confidence, key insight
2. **Macro Assessment** (1-2 pages) — Current regime, top 3 drivers, top 3 risks, macro data snapshot
3. **Sector Attractiveness** (1 page) — Ranked table (1-11), brief commentary on top 3 and bottom 1
4. **Market Themes** (1 page) — 3-4 key themes, each with thesis, opportunity, risk
5. **Key Risks** (0.5 page) — Downside scenarios, probabilities, timelines
6. **Forward Outlook** (0.5 page) — What to watch in 1-month, 2-3 month horizons

Should this structure change? Define better structure if needed.

---

## SECTION 5: ACCEPTANCE CRITERIA

Phase 1 is COMPLETE when ALL criteria are met:

### **Specification Completeness**
✓ All 11 Key Questions answered with specificity (no hand-waving)
✓ Regime definitions clear and implementable (concrete conditions, not vague)
✓ 8 macro indicators specified with thresholds for each
✓ Conflict resolution algorithm in pseudocode
✓ Confidence scoring algorithm in pseudocode with examples
✓ Sector attractiveness algorithm in pseudocode
✓ Sector ranking algorithm in pseudocode with 11-sector example
✓ Report format and content clearly defined
✓ Data requirements documented (sources, freshness, error handling)

### **Independence Verified**
✓ Architecture shows MA as completely standalone
✓ No dependencies on other systems (no arrows to/from other analysts)
✓ Portfolio.md field access documented (explicit list of allowed/forbidden fields)
✓ Independence test plan included (procedure to verify portfolio-agnostic)

### **Portfolio-Agnostic Verified**
✓ Test procedure: Run MA with portfolio state A and state B (same market data), verify outputs identical
✓ Field access list (what MA reads from Portfolio.md): [explicit list]
✓ Forbidden fields documented (what MA must NOT read): [explicit list]

### **Data Integration Realistic**
✓ All 8 macro indicators sourced and documented
✓ Data freshness limits specified (1-3-7 days per indicator, see Section 7)
✓ Confidence penalty for stale data defined
✓ Do-not-publish threshold defined (confidence <50% = no regime call)
✓ Error handling specified (what happens if data unavailable?)
✓ Fallback logic specified (default regime if data missing?)

### **Example Outputs Provided**
✓ 2-3 sample Markdown reports (realistic data, realistic scenarios)
✓ Demonstrated output quality is professional and readable

### **Framework Compliance**
✓ v8.1.0 scope maintained (no v9 forward-thinking)
✓ No constraint calculations included
✓ No analyst validation included
✓ No portfolio coordination required
✓ Focus on market intelligence, not portfolio impact

---

## SECTION 6: WHAT v2.0 CHANGED FROM v1.0

**Key Improvements:**

1. **Purpose First** — Section 1 now explicitly states: "MA produces Markdown reports for humans. That is the only deliverable."

2. **JSON Trap Removed** — Question 13 (MAOutput schema) deleted entirely. No JSON output defined.

3. **Portfolio Independence Tightened** — Replaced vague "context understanding" with explicit field access list (allowed: risk_tolerance, sector_list; forbidden: holdings, allocations, constraints).

4. **Data Freshness Tightened** — Maximum acceptable age now 1-7 days (not 17-35 days). CPI data not acceptable if >7 days old.

5. **Confidence Rules Clarified** — Floor at 20% (not 40%), ceiling 95%, do-not-publish threshold at <50%.

6. **Crisis Override Fixed** — Requires 2+ independent crisis indicators (not just 1).

7. **Questions Reduced** — 11 questions instead of 13 (removed JSON).

---

## SECTION 7: DATA FRESHNESS REQUIREMENTS

**Maximum acceptable data age by indicator:**

| Indicator | Max Age | Rationale | Source |
|-----------|---------|-----------|--------|
| Fed Funds Rate | 1 day | Daily, market-critical | Federal Reserve |
| Inflation (CPI) | 7 days | Monthly release, 2-week lag; 7 days gives flexibility | BLS |
| GDP Growth | 7 days | Use Atlanta Fed Nowcast for interim freshness | BEA |
| Unemployment | 3 days | Released weekly by BLS | BLS |
| Credit Spreads | 1 day | Daily real-time data | Bloomberg |
| Yield Curve (10Y-2Y) | 1 day | Daily Treasury data | US Treasury |
| ISM Manufacturing PMI | 1 day | Monthly release, must be current month | ISM |
| Dollar Index | 1 day | Daily real-time | ICE |

**Confidence Penalty for Stale Data:**
- If indicator is 1-2 days beyond max age: -5 confidence points
- If indicator is 3+ days beyond max age: -15 confidence points

**Do-Not-Publish Rule:**
- If confidence score drops below 50%, do NOT generate regime call
- Instead, return: "NEUTRAL (insufficient data)" + explanation of stale indicators

---

## SECTION 8: CONFIDENCE CALCULATION RULES

### Confidence Floor: 20%
- Minimum publishable confidence is 20% (allows "weak signal" calls, but flags uncertainty)
- Example: 4-of-8 indicators aligned, mixed signal strength = 45% confidence → PUBLISH with "low confidence" caveat

### Confidence Ceiling: 95%
- Maximum confidence is 95% (acknowledges epistemic limits; no certainty in markets)
- Never publish 100% confidence

### Do-Not-Publish: <50% Confidence
- If calculated confidence < 50%, do not generate regime call
- Return: "INSUFFICIENT CONFIDENCE — regime unclear, awaiting data"
- Report: Which indicators are stale, when data expected

### Crisis Override Rule
- Crisis regime requires **2 or more independent crisis indicators**, not just 1
- Examples of independent: GDP negative AND unemployment spiking (independent signals of recession)
- Non-independent: Oil price spike + VIX spike (both symptoms of same event)

---

## SECTION 9: DELIVERABLES EXPECTED

MARS submits:

**Deliverable 1: Market-Analyst v8.1.0 Complete Specification**

Document containing:
- All 11 Key Questions answered (with specificity, examples, pseudocode)
- Regime definitions (clear, unambiguous)
- 8 macro indicators selected (with thresholds for each)
- Conflict resolution algorithm (pseudocode)
- Confidence scoring algorithm (pseudocode + examples)
- Sector attractiveness algorithm (pseudocode)
- Sector ranking algorithm (pseudocode + 11-sector example)
- Portfolio independence test plan (explicit, verifiable)
- Portfolio field access list (explicit allowed/forbidden)
- Report format and sections (Markdown structure)
- Data freshness requirements (per Section 7)
- Error handling and fallback logic
- No placeholders. No "Phase 2 will define this." Complete.

**Deliverable 2: Design Authority Memo**

Brief summary (1-2 pages):
- Design philosophy (why these choices?)
- Key trade-offs (what did you prioritize?)
- Identified risks or limitations
- Recommendations for Phase 2

**Deliverable 3: Example Outputs**

- 2-3 sample Markdown reports (realistic scenarios, realistic data)
- Demonstrates expected output quality and professionalism

---

## SECTION 10: PHASE 1 GATE REVIEW

### When MARS Completes Phase 1

1. Submit all 3 deliverables
2. Include status checklist indicating which acceptance criteria are satisfied
3. Flag any ambiguities or open design questions

### Design Authority Reviews Against

1. Completeness (all questions answered, no placeholders)
2. Unambiguity (clear enough for Phase 2 implementation)
3. Independence (verified portfolio-agnostic, no hidden coupling)
4. Real-world feasibility (data freshness realistic, algorithms implementable)
5. Framework alignment (v8.1.0 principles maintained)

### Gate Outcomes

**PASS**: All criteria met → Proceed immediately to Phase 2

**REVISIONS REQUESTED**: Some criteria not met → MARS revises and resubmits (1 iteration max)

**FAIL**: Major architectural issues → MARS redesigns and resubmits

---

## SECTION 11: CRITICAL PRINCIPLES FOR MARS

### Principle 1: Architecture > Implementation
- Don't optimize for answering the questions
- Optimize for **defining a system that solves the real problem**
- The real problem: "Humans need to understand market regime and sector dynamics to make portfolio decisions"
- Everything flows from that purpose

### Principle 2: Independence is Non-Negotiable
- It's not about good intentions ("I won't read portfolio state")
- It's about structural isolation (explicit field access list prevents mistakes)
- Future code will be written by people who didn't write the original
- Make mistakes impossible, not just unlikely

### Principle 3: Real-World Constraints Matter
- Don't design for idealized data availability
- Design for actual data (CPI published monthly with lag, unemployment released weekly, etc.)
- Account for data downtime gracefully
- Build confidence penalty into algorithm

### Principle 4: Humans Are the Consumers
- Markdown is readable by humans
- Reports should answer human questions
- Don't produce data structures "for other systems"
- Other systems may parse Markdown if they want, but that's their job, not MA's

---

## SECTION 12: SUPPORT & ESCALATION

### If MARS Needs Clarification

Document the ambiguity clearly. Submit via facilitator. Design Authority will clarify within 4 hours.

### Communication Protocol

- MARS produces → Facilitator passes to DA
- DA reviews → Facilitator passes back to MARS
- Manual handoff only (no direct thread communication)

---

## CONCLUSION

**Phase 1 Objective**: Produce a complete, unambiguous specification for a standalone market intelligence system that produces human-readable Markdown reports.

**Quality Standard**: Complete and implementable. Ready for Phase 2 development without rework.

**Key Improvement Over v1.0**: Focuses on PURPOSE (human-readable reports) instead of TASKS (answer 13 questions). Removes JSON trap. Tightens portfolio independence constraints. Specifies realistic data freshness.

**Authority**: This directive is active and approved for execution.

---

**DIRECTIVE STATUS:** ACTIVE  
**VERSION:** 2.0 (Revised)  
**ISSUED BY:** Design Authority (Master-Architect v8.1.0)  
**ISSUED TO:** MARS Thread (NEW)  
**DATE:** December 29, 2025, 11:20 AM MST  

**Ready for MARS execution.**

---

## APPENDIX: Quick Reference — 11 Key Questions

1. What defines each regime type? (BULLISH, NEUTRAL, BEARISH, CRISIS)
2. Which 8 macro indicators determine regime?
3. How do you resolve conflicting macro signals?
4. How is regime confidence calculated (0-100%)?
5. What drivers explain the regime (top 3 factors)?
6. What 4-6 dimensions make sectors attractive?
7. How do you score each sector's attractiveness?
8. How are all 11 sectors ranked (1-11)?
9. What sector rotation signals matter?
10. How do you identify market themes programmatically?
11. What is the Markdown report format and content?

**REMOVED (from v1.0): Question 13 (MAOutput JSON) — Not applicable. MA produces Markdown reports for humans, not data structures.**

---

**End of Phase 1 Directive v2.0**
