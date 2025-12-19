# TURN-PROMPTS-v1.4.1.md

**Purpose:** Consolidated execution prompts for all TURN 1-5 analysis phases  
**Version:** v1.4.1 (All TURNs integrated)  
**Date:** November 24, 2025  
**Status:** Production Ready  
**Note:** Consolidates PROMPT_SYNTHESIS.md, PROMPT_FUNDAMENTAL.md, PROMPT_TECHNICAL.md, PROMPT_COT.md, PROMPT_QUANT_BACKTEST.md, PROMPT_RISK.md, PROMPT_ITERATIVE_REFINEMENT.md into single reference document

---

## TURN 1: SYNTHESIS AGENT - Multi-Agent Analysis Orchestration

### TURN 1 Purpose
Synthesize outputs from four specialized analysis agents (Fundamental, Technical, Macro, Sentiment) into unified market thesis with conflict resolution.

### Agent 1: Fundamental Analysis Agent
**Role:** Deep fundamental valuation analysis

**Responsibilities:**
- DCF valuation (10-year model, discount rate selection, terminal growth)
- Comparable multiples (peer group selection, trading multiples assessment)
- Quality assessment (ROE, ROIC, debt levels, management quality)
- Growth analysis (historical growth, forward guidance, competitive positioning)
- Catalysts (earnings surprises, strategic initiatives, macro tailwinds)

**Output Format:**
```
Fundamental Thesis:
├─ Valuation: Fair value $X.XX (upside/downside %)
├─ Quality: X/100 score (based on ROE, ROIC, debt)
├─ Growth: X% expected growth (vs. Y% consensus)
├─ Key Drivers: [3 specific drivers]
└─ Conviction: X% (HIGH/MEDIUM/LOW)
```

---

### Agent 2: Technical Analysis Agent
**Role:** Price action, patterns, momentum analysis

**Responsibilities:**
- Support/Resistance identification (3 key levels, breakout probabilities)
- Chart patterns (current pattern, breakout direction, target)
- Momentum indicators (RSI, MACD, Volume trends)
- Trend identification (up/down/sideways with phase)
- Entry/Exit signals (specific trigger prices)

**Output Format:**
```
Technical Setup:
├─ Regime: Uptrend / Downtrend / Range
├─ Support: $X (strong), $Y (moderate)
├─ Resistance: $A (strong), $B (moderate)
├─ Key Setup: [Current pattern + breakout direction]
└─ Conviction: X% (HIGH/MEDIUM/LOW)
```

---

### Agent 3: Macro Analysis Agent
**Role:** Market context, macro environment, sector rotation

**Responsibilities:**
- Market regime (Growth/Value/Defensive/Transition)
- Interest rate environment (Fed policy, rate expectations)
- Economic backdrop (GDP, unemployment, inflation, credit)
- Sector rotation (sector trends, flows, relative strength)
- Competitive dynamics (industry structure, disruption risks)

**Output Format:**
```
Macro Context:
├─ Market Regime: Growth / Value / Defensive
├─ Rate Environment: Rising / Stable / Falling
├─ Sector Trend: [Sector name] - Bullish / Bearish / Neutral
├─ Competitive: [Position vs. peers]
└─ Conviction: X% (HIGH/MEDIUM/LOW)
```

---

### Agent 4: Sentiment Analysis Agent
**Role:** Institutional positioning, flows, sentiment

**Responsibilities:**
- Institutional positioning (long/short concentration)
- Retail sentiment (social media, retail flows)
- Options market signals (put/call ratios, skew)
- Short interest (shorts as % of float, covering risk)
- Analyst sentiment (consensus, recent changes)

**Output Format:**
```
Sentiment Assessment:
├─ Institutions: Accumulating / Distributing / Neutral
├─ Retail: Bullish / Bearish / Mixed
├─ Options: [Put/Call skew, current IV level]
├─ Short Interest: X% of float (high/low)
└─ Conviction: X% (HIGH/MEDIUM/LOW)
```

---

### TURN 1 Synthesis Process

**Step 1: Collect Outputs**
- Receive outputs from Fundamental Agent
- Receive outputs from Technical Agent
- Receive outputs from Macro Agent
- Receive outputs from Sentiment Agent

**Step 2: Identify Areas of Agreement**
```
Framework Alignment Check:
├─ ALL agents bullish → HIGH conviction BUY
├─ 3/4 agents bullish → MEDIUM conviction BUY
├─ 2/4 agents bullish → MIXED signals
├─ <2/4 agents bullish → Lean toward HOLD/SELL
```

**Step 3: Flag Conflicts**
```
Conflict Detection:
├─ Fundamental bullish but Technical bearish? → Flag risk
├─ Macro positive but Sentiment negative? → Flag uncertainty
├─ All conflicts documented for escalation
```

**Step 4: Preliminary Recommendation**
```
TURN 1 Output:
├─ Synthesis Recommendation: BUY / HOLD / SELL
├─ Conviction: X% (based on agent agreement)
├─ Key Drivers: [Top 3 from agents]
├─ Key Risks: [Top 3 from agents]
├─ Conflicts Flagged: [None / List if any]
└─ Confidence Score: X% (0-100)
```

---

## TURN 2: QUANTITATIVE VALIDATION - Backtesting & Price Validation

### TURN 2 Purpose
Validate entry/stop/target prices using quantitative backtesting. Ensure technical setup has statistically significant win rate.

### Backtesting Framework

**Strategy Definition:**
```
Strategy Name: [e.g., "Mean Reversion at Support"]
Entry Condition: [Specific trigger, e.g., "RSI < 30 + price touches 200-day MA"]
Exit Condition (Profit): [Specific target, e.g., "Price hits 50-day MA"]
Exit Condition (Loss): [Specific stop, e.g., "Price breaks below 200-day MA"]
```

**Backtesting Specification:**
- Historical period: [Start date to end date]
- Data source: [Daily/intraday, adjusted/unadjusted]
- Position size: [Standard (e.g., 1 contract per signal)]
- Slippage: [Assumed slippage $0.XX per trade]

**Backtesting Results:**
```
Performance Metrics:
├─ Total Trades: X
├─ Winning Trades: X (%)
├─ Losing Trades: X (%)
├─ Win Rate: X%
├─ Profit Factor: X.XX (gross profit / gross loss)
├─ Largest Win: $X.XX
├─ Largest Loss: $X.XX
├─ Drawdown (Max): X%
├─ Sharpe Ratio: X.XX
└─ Return (Annualized): X%
```

**Validation Output:**
```
TURN 2 Result:
├─ Entry Price: $X.XX (VALIDATED by backtest)
├─ Stop Loss: $Y.YY (Backtest drawdown: X%)
├─ Target: $Z.ZZ (Backtest avg win: X%)
├─ Win Rate: X% (statistically significant if >50%)
├─ Profit Factor: X.XX (>1.5 preferred)
├─ Confidence: HIGH (if win rate >60%) / MEDIUM / LOW
└─ Status: APPROVED / NEEDS REFINEMENT / REJECTED
```

---

## TURN 3: SCENARIO MODELING - Conditional Analysis

### TURN 3 Purpose
Model three scenarios (Bull/Base/Bear) to generate conditional recommendations.

### Scenario Definitions

**Bull Case Scenario:**
- Market Regime: Growth (or favorable)
- Key Assumption: [Main upside driver, e.g., "Fed cuts rates 100bps by end of year"]
- Stock Impact: [How this benefits the stock]
- Price Target (Bull): $X.XX
- Probability: X%

**Base Case Scenario:**
- Market Regime: Balanced
- Key Assumption: [Status quo assumption, e.g., "Rates stable, moderate growth continues"]
- Stock Impact: [How this affects the stock]
- Price Target (Base): $Y.YY
- Probability: X% (typically 50-60%)

**Bear Case Scenario:**
- Market Regime: Defensive/Recession
- Key Assumption: [Main downside driver, e.g., "Recession, earnings decline 30%"]
- Stock Impact: [How this hurts the stock]
- Price Target (Bear): $Z.ZZ
- Probability: X%

### Scenario Analysis Output

```
TURN 3 Result:
├─ Bull Case: $X.XX (Probability X%)
│  └─ Recommendation if Bull: BUY (target $X)
├─ Base Case: $Y.YY (Probability X%)
│  └─ Recommendation if Base: HOLD (target $Y)
├─ Bear Case: $Z.ZZ (Probability X%)
│  └─ Recommendation if Bear: SELL (target $Z or lower)
├─ Probability-Weighted Target: $W.WW
└─ Scenario-Conditional Recommendations: [List all 3 scenarios]
```

---

## TURN 4: RISK ASSESSMENT - VaR, Stress Testing, Confidence Scoring

### TURN 4 Purpose
Quantify portfolio impact, stress test scenarios, and score recommendation confidence.

### Risk Metrics

**Value at Risk (VaR):**
- 95% VaR: Worst case loss in 20 bad days (e.g., "-$X from entry")
- 99% CVaR: Worst case loss if you're in worst 1% of days (e.g., "-$Y from entry")

**Stress Testing:**
```
Stress Scenario: [e.g., "S&P 500 down 10%"]
├─ Stock Impact: [Expected move, e.g., "-15% (1.5x market beta)"]
├─ Portfolio Impact: [Dollar impact if position is X% of portfolio]
└─ Recovery Time: [How long to recover based on historical]
```

**Concentration Risk:**
- Position size: X% of portfolio
- Correlation to portfolio: [0-1.0, lower is better]
- Portfolio stress if position goes to stop: $X loss

### Confidence Scoring Framework

**Gate-Based Confidence:**
```
Confidence Score Calculation:
├─ Gate 1: Fundamental & Technical Agree? (+20% if yes)
├─ Gate 2: Backtesting Win Rate >55%? (+20% if yes)
├─ Gate 3: Macro Backdrop Supportive? (+15% if yes)
├─ Gate 4: Sentiment Not Extreme? (+15% if yes)
├─ Gate 5: Risk/Reward >1.5:1? (+15% if yes)
├─ Gate 6: VaR Acceptable? (+15% if yes)
└─ Final Score: X% (0-100)
```

**Confidence Levels:**
- 80-100%: HIGH conviction → BUY sizing 3-5%
- 60-79%: MEDIUM conviction → BUY sizing 2-3%
- 40-59%: LOW conviction → HOLD or small ADD sizing 0-1%
- <40%: VERY LOW conviction → HOLD or WAIT

### Risk Assessment Output

```
TURN 4 Result:
├─ Confidence Score: X%
├─ VaR (95%): -$X per $1000 invested
├─ CVaR (99%): -$Y per $1000 invested
├─ Stress Test: Stock down X% if market down 10%
├─ Position Size: Recommended X% of portfolio
├─ Risk/Reward: 1:X ratio (>1.5 preferred)
└─ Recommendation: BUY at X% sizing / HOLD / WAIT
```

---

## TURN 5: ITERATIVE REFINEMENT - Validation & Final Recommendation

### TURN 5 Purpose
Test assumptions, validate convergence, refine recommendation with confidence score.

### Assumption Sensitivity Analysis

**Key Assumptions to Test:**
```
Assumption 1: [e.g., "Earnings growth 15%"]
├─ If actual is 10%: Price target becomes $X.XX (down X%)
├─ If actual is 20%: Price target becomes $Y.YY (up Y%)

Assumption 2: [e.g., "Multiple expansion from 12x to 14x"]
├─ If multiple stays 12x: Price target becomes $A.AA
├─ If multiple expands to 16x: Price target becomes $B.BB
```

**Sensitivity Results:**
- Most likely scenario: $X.XX (current recommendation)
- Downside sensitivity: Stock prices would break down at $Y.YY
- Upside sensitivity: Stock could reach $Z.ZZ if best case

### Convergence Validation

**Question:** Does framework converge on same recommendation across all approaches?

```
Convergence Check:
├─ Fundamental Analysis says: BUY ✓
├─ Technical Analysis says: BUY ✓
├─ Scenario Analysis says: BUY in base case ✓
├─ Risk Assessment says: Acceptable risk for BUY ✓
└─ Convergence: YES - HIGH CONVICTION RECOMMENDATION
```

If any approach disagrees, document the conflict and address it.

### TURN 5 Final Output

```
TURN 5 Result (FINAL):
├─ Recommendation: BUY / HOLD / SELL
├─ Confidence: X% (HIGH/MEDIUM/LOW)
├─ Price Target: $X.XX (base case)
├─ Entry Price: $Y.YY (VALIDATED)
├─ Stop Loss: $Z.ZZ (hard stop)
├─ Risk/Reward: 1:X.X (favorable if >1.5)
├─ Time Horizon: X months
├─ Key Assumption: [Most critical driver]
├─ Key Risk: [Biggest risk to thesis]
└─ Conviction: X% ready for TURN 6 output generation
```

---

## SUMMARY: TURN 1-5 WORKFLOW

**TURN 1 Input:** Market analysis (macro context)
**TURN 1 Output:** Multi-agent synthesis with preliminary recommendation

↓

**TURN 2 Input:** TURN 1 technical setup
**TURN 2 Output:** Validated entry/stop/target prices with win rates

↓

**TURN 3 Input:** TURN 1 & 2 outputs
**TURN 3 Output:** Bull/Base/Bear scenario recommendations

↓

**TURN 4 Input:** TURN 1-3 outputs
**TURN 4 Output:** Risk-adjusted confidence score and position sizing

↓

**TURN 5 Input:** TURN 1-4 outputs
**TURN 5 Output:** FINAL recommendation with confidence, ready for TURN 6 formatting

---

## REFERENCE: All TURN 1-5 Use These Core Files

**All TURNs reference these specialized components:**

1. **Fundamental Analysis Component** - DCF, multiples, quality scoring
2. **Technical Analysis Component** - Support/resistance, patterns, momentum
3. **Macro Analysis Component** - Market regime, rates, sector rotation
4. **Sentiment Analysis Component** - Positioning, flows, short interest
5. **Backtesting Component** - Strategy validation, win rates, Sharpe
6. **Risk Component** - VaR/CVaR, stress testing, confidence scoring
7. **Refinement Component** - Sensitivity analysis, convergence checking

---

**END OF TURN-PROMPTS-v1.4.1.md**