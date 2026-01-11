# WEEKLY MARKET STRUCTURE TASK (UPDATED v3)

## TASK OBJECTIVE
Analyze current market structure and regime health. Search for macro and sector data to assess: (1) manufacturing, labor, credit, and breadth pillars of regime stability, (2) sector rotation health and leadership, (3) **explicit 1-11 sector ranking with numerical scores for regime synthesis**. Return markdown report focused on market foundation strength.

**Execution:** Any time after market close Friday and Monday market open.

---

## EXECUTION INSTRUCTIONS

### PHASE 1: DATA COLLECTION

Use `search_web` to gather macro and sector data:

1. **Manufacturing Trend:** `"ISM Manufacturing latest reading trend recent weeks"`
2. **Labor Market:** `"jobless claims unemployment rate latest current"`
3. **Credit Spreads:** `"high yield spreads investment grade spreads current"`
4. **Market Breadth:** `"S&P 500 advance decline ratio breadth current"`
5. **Sector Performance:** `"sector performance week XLV XLF XLK healthcare financials technology 2026"`

### PHASE 2: ANALYSIS

**MACRO REGIME HEALTH (4 Pillars)**
- Manufacturing: ISM level, trend direction (improving/stable/deteriorating)
- Labor: Claims level, unemployment trend, soft-landing thesis status
- Credit: HY/IG spreads (normal/widening/tight), systemic stress signal
- Breadth: ADR value, small-cap participation, concentration risk

**SECTOR ROTATION ASSESSMENT**
- Which sectors up this week? Which down?
- Count: How many sectors participating vs. lagging
- Is rotation real (Healthcare/Financials up, Tech down) or narrow?
- Healthcare + Financials momentum: Gaining or stalling?

**SECTOR RANKING SCORING (CRITICAL NEW COMPONENT)**

Score each of 11 GICS sectors from 0-10 based on:
- **Momentum (0-3 pts):** Week-over-week performance vs. S&P 500. Up >2% = 3 pts, up 1-2% = 2 pts, flat/down = 0-1 pts
- **Macro Fit (0-3 pts):** Alignment with current regime (growth/defensive/cyclical). Direct alignment = 3 pts, partial = 1-2 pts, misaligned = 0 pts
- **Fundamentals (0-2 pts):** Earnings growth outlook, margin trends, valuation. Strong = 2 pts, stable = 1 pt, weak = 0 pts
- **Technical (0-2 pts):** Position vs. 50-day MA, trend direction. Above MA, uptrend = 2 pts, mixed = 1 pt, below MA, downtrend = 0 pts

**Rank 1-11 from highest to lowest score.**

Assign tier:
- **ATTRACTIVE (7-10):** Overweight candidates
- **NEUTRAL (4-6):** Hold/monitor current weights
- **UNATTRACTIVE (0-3):** Reduce/underweight

**POSITIONING IMPLICATIONS**
- Sector calls: Buy/Hold/Reduce for each sector
- Market exposure: Increase/Hold/Reduce equity allocation
- Hedge positioning: Increase/Hold/Reduce defensive hedges

---

## OUTPUT FORMAT

```
# Weekly Market Structure

**Version:** 3  
**Report Date:** [TODAY'S DATE]  
**Coverage Period:** [PAST TRADING WEEK DATES]  

---

## MACRO REGIME HEALTH: FOUR PILLARS

### Pillar 1: Manufacturing

**ISM Latest:** [Reading] (Trend: improving/stable/deteriorating)

**Data Point:** [New Orders subindex, factory employment, production details]

**Assessment:** [CLEAR / CAUTION / HOLD]

**Implication:** [1-2 sentences on growth outlook]

---

### Pillar 2: Labor Market

**Jobless Claims:** [Latest number] (Trend: improving/stable/deteriorating)

**Unemployment:** [Current %] (Trend)

**Assessment:** [CLEAR / CAUTION / HOLD]

**Implication:** [1-2 sentences on soft-landing thesis]

---

### Pillar 3: Credit Markets

**HY Spreads:** [Current bps] (Trend: tightening/stable/widening)

**IG Spreads:** [Current bps] (Trend: tightening/stable/widening)

**Assessment:** [CLEAR / CAUTION / HOLD]

**Implication:** [1-2 sentences on systemic stress level]

---

### Pillar 4: Market Breadth

**ADR (Advance-Decline Ratio):** [Current value] (Trend: improving/stable/deteriorating)

**Small-Cap Participation:** [Russell 2000 % change] (Broad or narrow leadership)

**Assessment:** [CLEAR / CAUTION / HOLD]

**Implication:** [1-2 sentences on concentration risk]

---

## MACRO REGIME VERDICT

[2-3 sentences synthesizing all four pillars and regime health assessment]

**Regime Classification:** [GROWTH / NEUTRAL / TRANSITION / DEFENSIVE]

**Regime Confidence:** [X]% (improving / stable / eroding from last week)

---

## SECTOR RANKING: 1-11 GICS WITH NUMERICAL SCORES

### Ranking Methodology

**Scoring Criteria (0-10 scale per sector):**
- **Momentum (0-3 pts):** Week-over-week % performance vs. S&P 500 baseline
- **Macro Fit (0-3 pts):** How well sector benefits from current regime classification
- **Fundamentals (0-2 pts):** Earnings growth outlook, margin trends, relative valuation
- **Technical (0-2 pts):** Position vs. 50-day MA, trend strength, momentum direction

**Interpretation:**
- 7-10: ATTRACTIVE (overweight/buy candidates)
- 4-6: NEUTRAL (hold current weights)
- 0-3: UNATTRACTIVE (reduce/underweight)

---

### Sector Rankings 1-11

| Rank | Sector | Score | Tier | Momentum | Macro Fit | Fundamentals | Technical | Key Driver |
|------|--------|-------|------|----------|-----------|--------------|-----------|-----------|
| 1 | [SECTOR] | [X.X]/10 | ATTRACTIVE | [+/- X%] | [HIGH/MED/LOW] | [STRONG/STABLE/WEAK] | [BULLISH/NEUTRAL/BEARISH] | [Brief driver] |
| 2 | [SECTOR] | [X.X]/10 | ATTRACTIVE | [+/- X%] | [HIGH/MED/LOW] | [STRONG/STABLE/WEAK] | [BULLISH/NEUTRAL/BEARISH] | [Brief driver] |
| 3 | [SECTOR] | [X.X]/10 | ATTRACTIVE | [+/- X%] | [HIGH/MED/LOW] | [STRONG/STABLE/WEAK] | [BULLISH/NEUTRAL/BEARISH] | [Brief driver] |
| 4 | [SECTOR] | [X.X]/10 | NEUTRAL | [+/- X%] | [HIGH/MED/LOW] | [STRONG/STABLE/WEAK] | [BULLISH/NEUTRAL/BEARISH] | [Brief driver] |
| 5 | [SECTOR] | [X.X]/10 | NEUTRAL | [+/- X%] | [HIGH/MED/LOW] | [STRONG/STABLE/WEAK] | [BULLISH/NEUTRAL/BEARISH] | [Brief driver] |
| 6 | [SECTOR] | [X.X]/10 | NEUTRAL | [+/- X%] | [HIGH/MED/LOW] | [STRONG/STABLE/WEAK] | [BULLISH/NEUTRAL/BEARISH] | [Brief driver] |
| 7 | [SECTOR] | [X.X]/10 | NEUTRAL | [+/- X%] | [HIGH/MED/LOW] | [STRONG/STABLE/WEAK] | [BULLISH/NEUTRAL/BEARISH] | [Brief driver] |
| 8 | [SECTOR] | [X.X]/10 | UNATTRACTIVE | [+/- X%] | [HIGH/MED/LOW] | [STRONG/STABLE/WEAK] | [BULLISH/NEUTRAL/BEARISH] | [Brief driver] |
| 9 | [SECTOR] | [X.X]/10 | UNATTRACTIVE | [+/- X%] | [HIGH/MED/LOW] | [STRONG/STABLE/WEAK] | [BULLISH/NEUTRAL/BEARISH] | [Brief driver] |
| 10 | [SECTOR] | [X.X]/10 | UNATTRACTIVE | [+/- X%] | [HIGH/MED/LOW] | [STRONG/STABLE/WEAK] | [BULLISH/NEUTRAL/BEARISH] | [Brief driver] |
| 11 | [SECTOR] | [X.X]/10 | UNATTRACTIVE | [+/- X%] | [HIGH/MED/LOW] | [STRONG/STABLE/WEAK] | [BULLISH/NEUTRAL/BEARISH] | [Brief driver] |

**Score Distribution:**
- Top 3 (Rank 1-3): ATTRACTIVE combined 7-10 points → **OVERWEIGHT CANDIDATES**
- Middle 4 (Rank 4-7): NEUTRAL combined 4-6 points → **HOLD / MONITOR**
- Bottom 4 (Rank 8-11): UNATTRACTIVE combined 0-3 points → **REDUCE / UNDERWEIGHT**

---

## SECTOR ROTATION ANALYSIS

### 11-Sector Performance Scorecard (This Week)

| Sector | This Week | Direction | Momentum | Status |
|--------|-----------|-----------|----------|--------|
| Healthcare | [+/- X%] | [↑/→/↓] | [Strong / Stable / Weak] | [Leading / Participating / Lagging] |
| Financials | [+/- X%] | [↑/→/↓] | [Strong / Stable / Weak] | [Leading / Participating / Lagging] |
| Technology | [+/- X%] | [↑/→/↓] | [Strong / Stable / Weak] | [Leading / Participating / Lagging] |
| Consumer Disc. | [+/- X%] | [↑/→/↓] | [Strong / Stable / Weak] | [Leading / Participating / Lagging] |
| Energy | [+/- X%] | [↑/→/↓] | [Strong / Stable / Weak] | [Leading / Participating / Lagging] |
| Materials | [+/- X%] | [↑/→/↓] | [Strong / Stable / Weak] | [Leading / Participating / Lagging] |
| Industrials | [+/- X%] | [↑/→/↓] | [Strong / Stable / Weak] | [Leading / Participating / Lagging] |
| Utilities | [+/- X%] | [↑/→/↓] | [Strong / Stable / Weak] | [Leading / Participating / Lagging] |
| Real Estate | [+/- X%] | [↑/→/↓] | [Strong / Stable / Weak] | [Leading / Participating / Lagging] |
| Consumer Staples | [+/- X%] | [↑/→/↓] | [Strong / Stable / Weak] | [Leading / Participating / Lagging] |
| Communications | [+/- X%] | [↑/→/↓] | [Strong / Stable / Weak] | [Leading / Participating / Lagging] |

**Sector Count:** [X]/11 up, [Y]/11 down/flat this week

### Rotation Assessment

**Is Rotation Real?**
[Assess based on sector performance: Are top-ranked sectors actually up? Are bottom-ranked sectors actually down? Or is ranking contradicted by price action?]

**Rotation Health:** [WORKING / INCOMPLETE / STALLED / NOT HAPPENING]

**Key Insight:** [2-3 sentences on what sector action tells us about market structure and regime]

---

## WHAT'S WORKING | WHAT'S NOT

### Strengths
- ✅ [Sector/metric that validates macro theme]
- ✅ [Breadth or momentum improvement]
- ✅ [Specific strength supporting current regime]

### Weaknesses
- ❌ [Lagging sector or deteriorating metric]
- ❌ [Breadth or momentum concern]
- ❌ [Specific weakness challenging current regime]

### Structure Verdict
[2-3 sentences synthesizing strengths/weaknesses: Is market structure healthy or showing stress? Are foundations stable or eroding?]

---

## SECTOR POSITIONING RECOMMENDATIONS

| Rank | Sector | Recommendation | Score Rationale |
|------|--------|---|---|
| 1 | [TOP RANKED] | [BUY / OVERWEIGHT] | [Score 8.5/10: Strong momentum, macro tailwinds, bullish technicals] |
| 2 | [2ND RANKED] | [BUY / OVERWEIGHT] | [Score 8.0/10: ...] |
| 3 | [3RD RANKED] | [HOLD / ACCUMULATE] | [Score 7.2/10: ...] |
| 4-7 | [NEUTRAL RANKED] | [HOLD] | [Scores 4-6/10: Maintain current weights, monitor for rotation] |
| 8-11 | [UNATTRACTIVE RANKED] | [REDUCE / UNDERWEIGHT] | [Scores 0-3/10: Limited upside, reduce exposure] |

---

## MARKET EXPOSURE GUIDANCE

**Equity Allocation:** [INCREASE / HOLD / REDUCE]
- Rationale: [Based on macro regime and breadth assessment]

**Hedge Positioning:** [INCREASE / HOLD / REDUCE]
- Rationale: [Based on concentration risk and breadth deterioration]

**Cash Buffer:** [INCREASE / HOLD / REDUCE]
- Target: [X%] of portfolio for opportunities or safety

---

## CRITICAL RULES

1. **All data from searches.** No fabricated numbers.
2. **Focus on structure.** Answer: "Is the market foundation healthy?"
3. **Four-pillar assessment.** Manufacturing, labor, credit, breadth = regime health.
4. **Sector ranking is explicit and scored.** 1-11 with 0-10 scale, not subjective.
5. **Momentum must match ranking.** If Tech ranked #1 but down 2%, note the contradiction.
6. **Actionable output.** End with clear positioning recommendations tied to ranking scores.

---

## EXECUTION

Search for current macro and sector data.

Analyze:
- Manufacturing health (ISM level + trend)
- Labor stability (claims, unemployment)
- Credit stress (HY/IG spreads)
- Breadth participation (ADR, small-cap)
- Sector leadership (actual prices + technical + fundamentals)
- **Sector ranking (score each 1-11)**
- Rotation quality (is it real or contradicted by prices?)

Synthesize into markdown report per format above.

Return report answering: 
- **Is market structure healthy?** 
- **Which sectors should lead portfolio allocation (1-11 ranking)?**
- **Do actual prices match ranking? (Confirmation or contradiction?)**

---

**No placeholder fields. All fields filled with actual data from searches.**

---

## KEY OUTPUTS FOR MARKET-ANALYST SYNTHESIS

This report's **PRIMARY OUTPUTS** that feed into Regime.md synthesis:

1. **Sector Rankings 1-11 with Scores (0-10)** — Direct input to Regime.md section "Sector Rankings (1-11 GICS)"
2. **Macro Regime Health Verdict** (GROWTH / NEUTRAL / TRANSITION / DEFENSIVE) — Inputs to Regime.md regime classification
3. **Regime Confidence %** — Inputs to Regime.md confidence calculation
4. **Four-Pillar Assessment** (ISM, labor, credit, breadth status) — Feeds into Regime.md signal evaluation

**Market-Analyst v8.2.0 consumes:**
- This report's sector rankings table
- This report's regime classification and confidence
- Combine with Risk-and-Regime, Mag7, and Daily-Analysis reports
- Synthesize into unified Regime.md output
```

---

## CHANGES FROM PREVIOUS VERSION

**What's New:**
- **SECTOR RANKING: 1-11 GICS WITH NUMERICAL SCORES** section (explicit ranking with 0-10 scoring)
- **Ranking Methodology** section (defines scoring criteria: Momentum, Macro Fit, Fundamentals, Technical)
- **Sector Rankings 1-11 table** (required output: Rank, Sector, Score, Tier, breakdown of each scoring component)
- **Score Distribution** explanation (7-10 = ATTRACTIVE, 4-6 = NEUTRAL, 0-3 = UNATTRACTIVE)
- **KEY OUTPUTS FOR MARKET-ANALYST SYNTHESIS** section (clarifies what feeds to regime synthesis)

**What's the Same:**
- 4-pillar macro analysis (Manufacturing, Labor, Credit, Breadth)
- Sector rotation assessment (is it real or narrow)
- Positioning recommendations
- All other structure and format
