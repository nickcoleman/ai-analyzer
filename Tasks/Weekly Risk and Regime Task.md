WEEKLY RISK & REGIME TASK

## TASK OBJECTIVE
Analyze critical risk factors and regime shift thresholds. Search for risk asset performance and upcoming economic data to assess: (1) carry-trade health (Bitcoin, spreads, leverage), (2) safe-haven demand (gold, silver, treasuries), (3) volatility and sentiment drift, (4) next week's highest-priority risks, (5) regime shift conditions. Return markdown report focused on risk management and positioning for next week.

**Execution:** Any time after market close Friday and Monday market open.

---

## EXECUTION INSTRUCTIONS

### PHASE 1: DATA COLLECTION

Use `search_web` to gather risk and regime data:

1. **Risk Assets:** `"Bitcoin gold silver price this week VIX volatility"`
2. **Sentiment:** `"Fear and Greed Index current latest sentiment drift"`
3. **Treasuries/Yields:** `"Treasury yield curve 2-10 spread current rate trend"`
4. **Economic Calendar:** `"economic calendar next week ISM manufacturing employment CPI data"`
5. **Fed/Macro Risks:** `"Federal Reserve FOMC next decision rate expectations 2026"`

### PHASE 2: ANALYSIS

**CARRY-TRADE HEALTH**
- Bitcoin: Current level, week direction, support levels
- Credit spreads (HY/IG): Current, trend, deleveraging risk
- Leverage indicators: Are financial conditions tightening?
- Conclusion: Is carry trade intact or cracking?

**SAFE-HAVEN DEMAND**
- Gold: Current level, week direction, uptrend significance
- Silver: Current level, week direction, industrial vs. safe-haven signal
- Treasury demand: Falling/stable/rising yields signal
- Conclusion: Is flight-to-safety building or dormant?

**VOLATILITY & SENTIMENT**
- VIX: Current level, week pattern (spike midweek? Settled?)
- Fear/Greed: Current reading, drift direction (toward fear or greed)
- Conclusion: Is market calm or stressed? Capitulation or accumulation?

**MARKET STRESS LEVEL**
- Combine: Bitcoin status + gold status + VIX + sentiment
- Verdict: LOW / MODERATE / HIGH stress

**RISK HIERARCHY FOR NEXT WEEK**
- What's the next critical economic release? (ISM? Labor? CPI?)
- What's the critical technical level? (Bitcoin support? NVDA support?)
- Which combination of failures = regime shift?

**REGIME SHIFT THRESHOLDS**
- NEUTRAL→GROWTH: What conditions unlock growth regime?
- NEUTRAL→DEFENSIVE: What conditions trigger defensive shift?
- NEUTRAL→TRANSITION: What conditions warrant wait-and-see?

---

## OUTPUT FORMAT

```
# Weekly Risk & Regime

**Version:** 2  
**Report Date:** [TODAY'S DATE]  
**Coverage Period:** [PAST TRADING WEEK DATES]  

---

## RISK ASSET PERFORMANCE

### Bitcoin (Carry-Trade Health Indicator)

**Current Price:** $[X]

**Week Direction:** [↑ / → / ↓] [+/- X%]

**Status:** [STRENGTHENING / STABLE / DETERIORATING]

**Key Support Levels:** $[X], $[Y], $[Z]

**Signal:** [Carry-trade active / Neutral / Stressed]

**Interpretation:** [1-2 sentences on what Bitcoin movement tells us about leverage and risk-on sentiment]

---

### Gold (Safe-Haven Demand Indicator)

**Current Price:** $[X] per oz

**Week Direction:** [↑ / → / ↓] [+/- X%]

**Status:** [RISING / STABLE / FALLING]

**Signal:** [Flight-to-safety building / Baseline / Declining]

**Interpretation:** [1-2 sentences on what gold movement tells us about risk-off pressure]

---

### Silver (Industrial vs. Risk-Off Demand)

**Current Price:** $[X] per oz

**Week Direction:** [↑ / → / ↓] [+/- X%]

**Status:** [RISING / STABLE / FALLING]

**Signal:** [Industrial demand strong / Neutral / Weak]

**Interpretation:** [1-2 sentences on what silver movement tells us about economic growth expectations]

---

## VOLATILITY & SENTIMENT LANDSCAPE

### VIX (Volatility Gauge)

**Current Level:** [X]

**Week Pattern:** [Spiked midweek? Settled? Elevated?]

**Trend:** [Rising / Stable / Falling]

**Assessment:** [LOW / MODERATE / HIGH] volatility regime

**Interpretation:** [1-2 sentences on market stress level]

---

### Fear & Greed Index (Sentiment Drift)

**Current Reading:** [X] ([EXTREME FEAR / FEAR / NEUTRAL / GREED / EXTREME GREED])

**Week Drift:** [Moving toward fear / Neutral / Moving toward greed]

**Trend:** [Capitulating / Stabilizing / Accumulating]

**Interpretation:** [1-2 sentences on what sentiment tells us about investor positioning]

---

## TREASURY LANDSCAPE

**2-10 Yield Spread:** [X] bps

**Direction:** [Steepening / Flat / Inverting]

**2-Year Yield:** [X]%

**10-Year Yield:** [X]%

**Assessment:** [Growth-supportive / Neutral / Recessionary] curve shape

**Interpretation:** [1-2 sentences on what curve tells us about growth and Fed expectations]

---

## MARKET STRESS ASSESSMENT

### Carry-Trade Health

**Bitcoin:** [+/- X%] → [Intact / Cracking / In crisis]

**Credit Spreads:** [HY X bps, IG X bps] → [Normal / Widening / Spiking]

**Verdict:** Carry-trade [INTACT / PRESSURED / BROKEN]

---

### Safe-Haven Demand

**Gold:** [+/- X%] → [Rising / Stable / Falling]

**Treasury 10Y:** [X%] → [Rising (selling) / Stable / Falling (buying)]

**Verdict:** Risk-off pressure [LOW / MODERATE / HIGH]

---

### Overall Market Stress Level

[SYNTHESIS: Bitcoin + Gold + VIX + Fear/Greed]

**Current Stress:** [LOW / MODERATE / HIGH]

**Trajectory:** [Improving / Stable / Deteriorating]

**Interpretation:** [1-2 sentences: Is market calm or panicked? Differentiating leverage risk from fundamental risk?]

---

## RISK HIERARCHY FOR NEXT WEEK

### Priority 1: [Critical Economic Data or Technical Level]

**Event:** [ISM Manufacturing / Jobs Report / CPI / FOMC Decision / Or: Bitcoin support / NVDA support]

**Timing:** [Date and time]

**Why Critical:** [If this fails, what regime shift occurs?]

**Watch Threshold:** 
- BULLISH if: [Specific level/number]
- BEARISH if: [Specific level/number]
- NEUTRAL if: [Specific level/number]

**Current Probability Assessment:** [Likelihood of bullish / bearish / neutral outcome]

**Escalation Trigger:** [If this level breaks, shift to TRANSITION or DEFENSIVE regime]

---

### Priority 2: [Secondary Risk Data or Level]

**Event:** [Next most important data/level]

**Why Secondary:** [Why does this matter after Priority 1?]

**Watch Threshold:** [Specific watch levels]

**Escalation Condition:** [What would make this Priority 1?]

---

### Priority 3: [Tertiary Risk]

**Event:** [Third watch item]

**Why Tertiary:** [Why monitor this?]

**Watch Threshold:** [Specific watch level]

---

### Distant Watch (Longer Than 2 Weeks)

**Event:** [FOMC, other major events 3+ weeks out]

**Status:** [Monitor but not immediate action trigger]

---

## REGIME SHIFT DECISION TREE

### Path 1: NEUTRAL → GROWTH

**Conditions Required:**
- [Macro condition A: e.g., "ISM >50 + manufacturing recovery"]
- [Sentiment condition B: e.g., "Bitcoin >$90K + Fear/Greed >60"]
- [Breadth condition C: e.g., "ADR >0.75 + small-cap participation"]

**Probability:** [HIGH / MEDIUM / LOW]

**Implication:** Increase equity exposure, reduce hedges, rotate into Mag7 + Growth

---

### Path 2: NEUTRAL → DEFENSIVE

**Conditions Required:**
- [Macro deterioration A: e.g., "ISM <48 + manufacturing recession confirmed"]
- [Breadth deterioration B: e.g., "ADR <0.60 + Mag7 concentration >40%"]
- [Risk-off acceleration C: e.g., "Bitcoin <$82K + Gold >$2,150"]

**Probability:** [HIGH / MEDIUM / LOW]

**Implication:** Reduce equity exposure, increase hedges, shift to Treasury/precious metals/cash

---

### Path 3: NEUTRAL → TRANSITION (Wait-and-See)

**Conditions:**
- [Mixed signals: e.g., "Manufacturing weak but labor stable" OR "NVDA cracking but MSFT holding"]
- [Confidence eroding: Regime confidence drops to 60-65%]
- [Data inconclusive: Need more clarity before committing]

**Probability:** [HIGH / MEDIUM / LOW]

**Implication:** Reduce conviction in all bets, increase cash, await resolution signals

---

### Path 4: Stay NEUTRAL (Maintain Course)

**Conditions:**
- [Manufacturing stabilizes 48-50]
- [Labor stays healthy <250K]
- [Breadth holds 0.65-0.75]
- [Regime confidence stable 70-75%]
- [Risk assets calm, no panic signals]

**Probability:** [HIGH / MEDIUM / LOW]

**Implication:** Hold current positioning, maintain sector rotation (Healthcare/Financials), monitor escalations

---

## POSITIONING FOR NEXT WEEK

### Equity Allocation

**Current Target:** [X]% (vs. cash [Y]%, treasuries [Z]%)

**Recommendation:** [INCREASE / HOLD / REDUCE] equity exposure

**Rationale:** [Based on risk hierarchy and regime shift probability]

---

### Sector Rotation

**Continue Healthcare/Financials rotation?** [YES / PAUSE / REVERSE]

**Maintain Mag7 position?** [YES / REDUCE / ROTATE]

**Risk management:** [Watch for: Break in [X], Signal to [Y]]

---

### Hedge Positioning

**Defensive Hedges:** [INCREASE / HOLD / REDUCE]
- VIX calls if [condition]
- Treasury long if [condition]
- Precious metals if [condition]

**Stop Losses:** [Set at what levels?]

**Profit Taking:** [When to lock in gains from winners?]

---

## CRITICAL RULES

1. **All data from searches.** No fabricated risk metrics.
2. **Focus on risk.** Answer: "What can break the regime?"
3. **Probability scoring.** Assign likelihoods to regime shift paths.
4. **Actionable thresholds.** Every watch item has specific trigger level.
5. **No sector deep-dive.** That's Task 1. Just reference sector vulnerabilities.
6. **No Mag7 analysis.** That's Task 2. Just reference concentration risk.

---

## EXECUTION

Search for Bitcoin, gold, silver, VIX, Fear/Greed, treasury yields, economic calendar, Fed expectations.

Analyze:
- Carry-trade health (Bitcoin, leverage, deleveraging risk)
- Safe-haven demand (Gold, Silver, Treasury movement)
- Volatility and sentiment landscape (VIX, Fear/Greed drift)
- Next week's critical economic data and technical levels
- Regime shift probability for each path (GROWTH / DEFENSIVE / TRANSITION / NEUTRAL)
- Positioning implications (exposure, hedges, stops, profit-taking)

Synthesize into markdown report per format above.

Return report answering: **What are the critical risks for next week? When do we shift regime? How should we position for next week?**

---

**No placeholder fields. All fields filled with actual data from searches.**
