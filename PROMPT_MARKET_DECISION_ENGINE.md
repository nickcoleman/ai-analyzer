# PROMPT_MARKET_DECISION_ENGINE.md

**Version:** 1.0 (Phase 2 - Operational Algorithms)  
**Date:** November 25, 2025  
**Status:** Operational - Market decision execution engine  
**Scope:** Regime classification algorithms, position sizing formulas, rebalancing decision trees

---

## SECTION 1: REGIME CLASSIFICATION ALGORITHM

### **Purpose**
Convert raw analyst outputs into definitive market regime classification (Growth/Value/Defensive/Transition).

### **Input Data (From PROMPT_MARKET_ANALYSTS.md)**

```json
{
  "qma_technical_trend": "Strong up | Up | Neutral | Down | Strong down",
  "qma_breadth": 45,
  "qma_confidence": "HIGH | MEDIUM | LOW",
  
  "fma_earnings_growth": 5,
  "fma_macro_regime": "Expansion | Peak | Contraction | Trough",
  "fma_bubble_stage": 1,
  
  "irm_risk_regime": "Normal | Elevated | High | Crisis",
  "irm_correlation": 0.55,
  
  "mspa_greed_intensity": 8,
  "mspa_fear_intensity": 3,
  "mspa_sentiment_stage": 1
}
```

### **Decision Tree: Regime Classification**

```
START: Classify Market Regime

STEP 1: Check Macro Foundation (FMA)
├─ If macro = "Expansion" + earnings growth >5%
│  └─ Foundation: GROWTH potential
├─ If macro = "Peak" + earnings growth slowing
│  └─ Foundation: TRANSITION risk
├─ If macro = "Contraction" + unemployment rising
│  └─ Foundation: DEFENSIVE priority
└─ If macro = "Trough"
   └─ Foundation: CRISIS (all defensive)

STEP 2: Check Technical Confirmation (QMA)
├─ If breadth >55% + trend "Strong up" + confidence HIGH
│  └─ Technical: CONFIRMS growth foundation
├─ If breadth 45-55% + trend "Up but weakening" + confidence MEDIUM
│  └─ Technical: CAUTIONS growth (transition building)
├─ If breadth <45% + trend "Down" + confidence HIGH
│  └─ Technical: CONTRADICTS growth (shift to defensive)
└─ If breadth <40% + trend "Strong down" + confidence HIGH
   └─ Technical: OVERRIDES to defensive/crisis

STEP 3: Check Risk Confirmation (IRM)
├─ If correlation <0.60 + VaR -12% or better
│  └─ Risk: SUPPORTS growth positioning
├─ If correlation 0.60-0.75 + VaR -15%
│  └─ Risk: NEUTRAL (normal range)
├─ If correlation 0.75-0.90 + VaR -20%
│  └─ Risk: CAUTIONS (diversification failing)
└─ If correlation >0.90 + VaR >-25%
   └─ Risk: CRISIS (all correlations breaking down)

STEP 4: Check Sentiment Alignment (MSPA)
├─ If greed 8-10 + fear 2-3 + insider buying
│  └─ Sentiment: EUPHORICALLY optimistic (bubble peak risk)
├─ If greed 6-7 + fear 4-5 + mixed insider
│  └─ Sentiment: BALANCED
├─ If greed 4-5 + fear 6-7 + insider selling
│  └─ Sentiment: CAUTIOUSLY pessimistic
└─ If greed 2-3 + fear 8-10 + insider buying spikes
   └─ Sentiment: PANIC (potential bottom)

STEP 5: Consensus Scoring
├─ Count analyst agreement on regime:
│  ├─ 4-of-4 agree = Confidence VERY HIGH (95%+) → Execute
│  ├─ 3-of-4 agree = Confidence HIGH (80-90%) → Execute, monitor
│  ├─ 2-of-4 split = Confidence MEDIUM (60-75%) → Caution
│  └─ 1 or 0 agree = Confidence LOW (<50%) → Escalate to review

STEP 6: Final Regime Classification
└─ IF Foundation (FMA) + Technical (QMA) + Risk (IRM) + Sentiment (MSPA) align:
   ├─ GROWTH: All 4 say yes OR 3-of-4 say yes + no technical breakdown
   ├─ VALUE: FMA says "Peak/Contraction" + IRM says neutral + 2-3 analysts aligned
   ├─ DEFENSIVE: Macro contracting + correlation high + sentiment fearful
   ├─ TRANSITION: Foundation/Technical conflict OR Risk/Sentiment misalignment
   └─ CRISIS: >50% correlation + VaR worse than -25% + greed collapsed

END: Output regime + confidence + rationale
```

### **Regime Classification Output**

```json
{
  "regime_classification": "Growth | Value | Defensive | Transition | Crisis",
  "confidence": "VERY HIGH (95%+) | HIGH (80-90%) | MEDIUM (60-75%) | LOW (<50%)",
  "analyst_agreement": "4-of-4 | 3-of-4 | 2-of-4 | 1-of-4",
  
  "rationale": {
    "macro_foundation": "Expansion, earnings +5%, supports growth",
    "technical_confirmation": "Breadth 45%, declining, suggests transition risk",
    "risk_assessment": "Correlation 0.55, VaR -12%, risk normal but rising",
    "sentiment_alignment": "Greed 8/10 extreme, insider selling, bubble risk",
    "consensus": "3-of-4 say growth foundation, but MSPA dissents on sentiment"
  },
  
  "regime_confidence": "HIGH (80%)",
  "transition_risk": "MODERATE (40-60% within 4-8 weeks)"
}
```

---

## SECTION 2: SECTOR LEADERSHIP ALGORITHM

### **Purpose**
Determine which sectors are outperforming (safe havens) vs. underperforming within current regime.

### **Input Data**

```json
{
  "market_regime": "Growth",
  "market_performance_ytd": 15,
  
  "sectors": [
    {"name": "Technology", "performance": 35, "equal_weight": 8},
    {"name": "Communications", "performance": 12, "equal_weight": 10},
    {"name": "Financials", "performance": 18, "equal_weight": 16},
    {"name": "Industrials", "performance": 8, "equal_weight": 7},
    {"name": "Healthcare", "performance": 5, "equal_weight": 4},
    {"name": "Consumer Discretionary", "performance": 12, "equal_weight": 11},
    {"name": "Consumer Staples", "performance": -2, "equal_weight": -1},
    {"name": "Utilities", "performance": -5, "equal_weight": -4},
    {"name": "Real Estate", "performance": 3, "equal_weight": 2},
    {"name": "Materials", "performance": 10, "equal_weight": 9},
    {"name": "Energy", "performance": 6, "equal_weight": 5}
  ]
}
```

### **Sector Ranking Algorithm**

**Step 1: Calculate Performance vs. Market Average**

```
Market average performance: 15% YTD
Tech: 35% - 15% = +20% outperformance (RANK 1)
Financials: 18% - 15% = +3% outperformance (RANK 3)
Communications: 12% - 15% = -3% underperformance (RANK 5)
Utilities: -5% - 15% = -20% underperformance (RANK 11)
```

**Step 2: Check Divergence (Cap-Weight vs. Equal-Weight)**

```
Technology:
  Cap-weighted: +35%
  Equal-weighted: +8%
  Divergence: 27% (EXTREME CONCENTRATION - Weak Area 5 fix)
  Assessment: Distorted (Mag7 driving, not sector health)
  
Communications:
  Cap-weighted: +12%
  Equal-weighted: +10%
  Divergence: 2% (Normal, healthy)
  Assessment: Genuine sector strength
```

**Step 3: Apply Divergence Penalty (Weak Area 4 Fix)**

```
IF divergence >20%:
  Penalty = divergence × 0.5 (partial penalty, not full)
  Tech adjusted rank = 35% - (27% × 0.5) = 21.5% → Still RANK 1 but flagged
  
IF divergence 10-20%:
  Penalty = 5%
  
IF divergence <10%:
  Penalty = 0%
```

**Step 4: Regime Fit Multiplier**

```
Growth regime:
  Tech (1.2× multiplier) → Boost rank
  Communications (1.1× multiplier) → Boost rank
  Utilities (0.7× multiplier) → Reduce rank
  Energy (0.8× multiplier) → Reduce rank

Value regime:
  Financials (1.2× multiplier) → Boost
  Industrials (1.2× multiplier) → Boost
  Tech (0.7× multiplier) → Reduce
  Communications (0.8× multiplier) → Reduce

Defensive regime:
  Healthcare (1.2× multiplier) → Boost
  Utilities (1.2× multiplier) → Boost
  Consumer Staples (1.1× multiplier) → Boost
  Tech (0.5× multiplier) → Reduce
```

**Step 5: Final Sector Ranking**

```
Final Score = (Performance vs Market) × (Regime Multiplier) - Divergence Penalty

Growth Regime:

Tech: (20 × 1.2) - 13.5 = 10.5 → RANK 1 ★
  Flagged: Concentration extreme, monitor
  
Communications: (-3 × 1.1) - 0 = -3.3 → RANK 5
  Assessment: Underperforming growth regime

Financials: (3 × 1.0) - 0 = 3 → RANK 3
  Assessment: Neutral in growth regime

Utilities: (-20 × 0.7) - 0 = -14 → RANK 11
  Assessment: Worst fit for growth regime
```

### **Sector Leadership Output**

```json
{
  "regime": "Growth",
  "sector_rankings": [
    {
      "rank": 1,
      "sector": "Technology",
      "performance": 35,
      "regime_multiplier": 1.2,
      "divergence": 27,
      "divergence_flag": "EXTREME (Mag7 concentration)",
      "final_score": 10.5,
      "allocation_cap": 35,
      "recommendation": "Top sector, but concentration risk HIGH"
    },
    {
      "rank": 2,
      "sector": "Financials",
      "performance": 18,
      "regime_multiplier": 1.0,
      "divergence": 2,
      "divergence_flag": "Normal",
      "final_score": 3,
      "allocation_cap": 20,
      "recommendation": "Secondary sector, genuine strength"
    },
    {
      "rank": 11,
      "sector": "Utilities",
      "performance": -5,
      "regime_multiplier": 0.7,
      "divergence": 0,
      "divergence_flag": "Normal",
      "final_score": -14,
      "allocation_cap": 5,
      "recommendation": "Avoid in growth regime"
    }
  ],
  "safe_haven_assessment": "None (growth regime, all sectors cyclical)"
}
```

---

## SECTION 3: POSITION SIZING FORMULA

### **Purpose**
Calculate exact position sizing for each stock given conviction, regime, sector, and bubble stage.

### **Formula Components**

**Base Formula:**

```
Position Size = (Conviction Score) × (Regime Multiplier) × (Sector Multiplier) × (Stage Modulator) × (Portfolio Total %)

Where:
  Conviction Score = 0.50 to 1.00 (50% to 100% conviction)
  Regime Multiplier = 0.60 to 1.20 (defensive to growth)
  Sector Multiplier = 0.70 to 1.20 (weak to strong sector)
  Stage Modulator = 0.60 to 1.00 (bubble peak to foundation)
  Portfolio Total % = 90% (stocks in portfolio, not cash)
```

### **Example Calculation**

**Scenario: Microsoft [finance:Microsoft Corporation] in Growth Regime, Stage 1**

```
INPUT DATA:
  Conviction: 88% (0.88)
  Stock type: Growth (regime multiplier in Growth = 1.20)
  Sector: Technology (ranked #1, sector multiplier = 1.20)
  Bubble Stage: 1 (stage modulator = 0.92)
  Portfolio allocation: 90% stocks
  
CALCULATION:
  Position Size = 0.88 × 1.20 × 1.20 × 0.92 × 0.90
  Position Size = 0.88 × 1.20 × 1.20 × 0.92 × 0.90
  Position Size = 0.874 = 8.74% of portfolio
  
BUT: Single stock cap = 6% max
  Final Position = min(8.74%, 6%) = 6%
  
RATIONALE:
  - Microsoft [finance:Microsoft Corporation] 88% conviction justifies strong sizing
  - Growth regime supports tech allocation
  - Tech #1 sector adds boost
  - Stage 1 is accumulation phase (0.92 slight caution)
  - Position capped at 6% max single stock
```

**Scenario: Utilities Stock in Growth Regime, Stage 1**

```
INPUT DATA:
  Conviction: 72% (0.72)
  Stock type: Defensive (regime multiplier in Growth = 0.60)
  Sector: Utilities (ranked #11, sector multiplier = 0.70)
  Bubble Stage: 1 (stage modulator = 0.92)
  Portfolio allocation: 90% stocks
  
CALCULATION:
  Position Size = 0.72 × 0.60 × 0.70 × 0.92 × 0.90
  Position Size = 0.219 = 2.19% of portfolio
  
INTERPRETATION:
  - Even 72% conviction severely reduced by regime
  - Growth regime penalizes defensive stocks
  - Utilities ranked worst in sector (0.70×)
  - Result: Small position (2.2%) only, not core holding
  - Framework message: "Conviction OK but regime misaligned"
```

### **Stage Modulator Factors**

```
Stage 1 (Accumulation): 0.92 (slight caution, good opportunity)
Stage 2 (Peak): 0.85 (significant caution, reduce concentration)
Stage 3 (Burst): 0.60 (severe caution, sell into strength)
Stage 4 (Recovery): 0.80 (return to normal, opportunities emerging)
```

### **Position Sizing Output Format**

```json
{
  "stock": "Microsoft [finance:Microsoft Corporation]",
  "conviction": 88,
  "regime": "Growth",
  "regime_multiplier": 1.20,
  "sector": "Technology",
  "sector_multiplier": 1.20,
  "bubble_stage": 1,
  "stage_modulator": 0.92,
  "portfolio_allocation": 90,
  
  "calculated_size": 8.74,
  "guardrail_cap": 6.0,
  "final_position_size": 6.0,
  
  "sizing_rationale": "Strong conviction + growth regime + top sector supports 6% (max cap)",
  "conviction_threshold": "85% (6-month holding) - Position justified",
  "regime_alignment": "Excellent (growth stock in growth regime)",
  "sector_alignment": "Excellent (tech #1 sector in growth regime)"
}
```

---

## SECTION 4: REBALANCING DECISION TREE

### **Purpose**
Determine specific rebalancing actions (trim/hold/add) based on portfolio analysis output.

### **Input: Portfolio Analysis Data**

```json
{
  "stock_analysis": [
    {
      "stock": "Stock A",
      "current_position": 4.5,
      "conviction": 72,
      "conviction_threshold_month_6": 85,
      "sector": "Utilities",
      "regime_fit": "Poor",
      "performance_vs_sector": "-8%"
    },
    {
      "stock": "Stock B",
      "current_position": 5.0,
      "conviction": 88,
      "conviction_threshold_month_6": 85,
      "sector": "Technology",
      "regime_fit": "Excellent",
      "performance_vs_sector": "+15%"
    }
  ],
  
  "portfolio_total": 70,
  "target_allocation": 75,
  "regime": "Growth"
}
```

### **Decision Tree: Trim/Hold/Add**

```
FOR EACH STOCK IN PORTFOLIO:

DECISION 1: Is conviction above threshold?
├─ YES (conviction > threshold)
│  └─ Proceed to Decision 2
└─ NO (conviction < threshold)
   └─ ACTION: TRIM
      - Calculate trim amount: Position × (1 - Conviction/Threshold)
      - Example: 4% position, 72% conviction, 85% threshold
        Trim = 4% × (1 - 72/85) = 4% × 0.153 = 0.6% → Reduce to 3.4%

DECISION 2: Is sector fit aligned with regime?
├─ YES (stock type matches regime)
│  └─ Proceed to Decision 3
└─ NO (stock type conflicts with regime)
   └─ ACTION: TRIM or SELL
      - Defensive stock in Growth regime = Trim 25%
      - Growth stock in Defensive regime = Sell entirely
      - Example: Utilities in Growth regime = Trim 25%
                 Current 4.5% → Trim to 3.4%

DECISION 3: Is stock outperforming sector?
├─ YES (stock up 15%, sector up 8%)
│  └─ Proceed to Decision 4
├─ NO but outperforming market (stock up 5%, market up 3%)
│  └─ Proceed to Decision 4
└─ NO - underperforming (stock down 2%, sector up 8%)
   └─ ACTION: TRIM
      - Underperformance = quality concern
      - Trim 15% of position
      - Example: Stock A -8% vs sector → Trim 15%

DECISION 4: Is position sized correctly per formula?
├─ Position > Formula Result + 1%
│  └─ ACTION: TRIM to formula result
│     (Position drifted up, trim excess)
├─ Position < Formula Result - 1%
│  └─ ACTION: ADD to formula result
│     (Position drifted down, add back)
└─ Position within ±1% of formula
   └─ ACTION: HOLD
      (Position correctly sized, no action)

FINAL ACTIONS:
├─ If multiple trims → Consolidate into single trim order
├─ If add opportunity + trimmed proceeds → Execute swap
└─ If only holds → Report "Portfolio balanced, no action"
```

### **Rebalancing Decision Output**

```json
{
  "rebalancing_date": "2025-11-25",
  "current_portfolio_total": 70,
  "target_portfolio_total": 75,
  
  "actions": [
    {
      "stock": "Stock A (Utilities)",
      "current_position": 4.5,
      "decision": "TRIM",
      "reasoning": [
        "Conviction 72% below 85% threshold (month 6)",
        "Utilities poorly aligned with Growth regime",
        "Underperforming sector (-8%)"
      ],
      "trim_amount": 1.1,
      "target_position": 3.4,
      "proceeds": 1.1
    },
    {
      "stock": "Stock B (Technology)",
      "current_position": 5.0,
      "decision": "HOLD",
      "reasoning": [
        "Conviction 88% above 85% threshold",
        "Tech excellent alignment with Growth regime",
        "Outperforming sector (+15%)",
        "Position correctly sized per formula"
      ],
      "action": "No change"
    },
    {
      "stock": "Stock D (New Candidate from Screening)",
      "current_position": 0,
      "decision": "ADD",
      "reasoning": [
        "Top screening candidate (score 5.76)",
        "Tech sector, Growth regime aligned",
        "Seeking Alpha rating: Buy",
        "Replaces trimmed allocation from Stock A"
      ],
      "add_amount": 1.1,
      "target_position": 1.1
    }
  ],
  
  "portfolio_summary": {
    "cash_proceeds_from_trims": 1.1,
    "cash_to_deploy_to_adds": 1.1,
    "net_change": 0,
    "new_portfolio_total": 70,
    "status": "Rebalanced, ready for execution"
  }
}
```

---

## SECTION 5: REAL-TIME TRIGGER EXECUTION

### **Purpose**
When real-time triggers fire (regime shift, correlation spike, sentiment extreme), execute immediate rebalancing outside weekly cycle.

### **Trigger Execution Priorities**

**Priority 1: Regime Transition - Execute Same Day**

```
TRIGGER: QMA flags breadth <50% for 2+ days
STATUS: Regime transition from Growth → Transition detected

IMMEDIATE ACTIONS (within 1 hour):
  1. Recalculate sector caps for Transition regime:
     - Tech: 35% → 25% (reduce, prepare for defensive)
     - Financials: 20% → 25% (increase, stable value)
     - Defensive (Healthcare/Utilities): 10% → 20% (increase safe havens)
  
  2. Identify positions to trim:
     - Any Tech position >25% sector cap → trim excess
     - Any growth position >formula result → trim
     - Lock in profits into strength
  
  3. Identify safe haven additions:
     - Screening filter: Switch to defensive sector candidates
     - Healthcare/Utilities with high conviction
  
  4. Execute rebalancing by close of business
     Timeline: Sell into any strength, raise cash for safe havens

OUTPUT:
{
  "trigger": "Regime transition Growth → Transition",
  "detection_time": "2025-11-25 15:45",
  "execution_deadline": "2025-11-25 16:00 (market close)",
  "actions": "Sell Tech 35% → 25%, add Healthcare/Utilities, raise cash"
}
```

**Priority 2: Correlation Spike - Execute Within 1 Day**

```
TRIGGER: IRM flags correlation >0.75 on Day 2 confirmation
STATUS: Diversification breaking down, crisis approaching

IMMEDIATE ACTIONS (by end of Day 2):
  1. Verify correlation confirmed (not noise):
     Day 1: Correlation 0.75
     Day 2: Correlation 0.78 (confirmed)
  
  2. Reduce equity exposure:
     Current: 70% stocks, 30% cash
     Target: 60% stocks, 40% cash (reduce stocks 10%)
  
  3. Trim highest concentration first:
     - Trim Mag7 positions by 25%
     - Trim highest single-stock positions
     - Raise cash for optionality
  
  4. Add defensive hedges:
     - Consider VIX calls or put spreads
     - Increase stop-loss discipline

OUTPUT:
{
  "trigger": "Correlation spike >0.75",
  "detection": "Day 2 confirmation",
  "execution_deadline": "End of Day 2",
  "portfolio_change": "70% → 60% stocks, 30% → 40% cash",
  "trim_priority": "Mag7 first (25%), then highest single stocks"
}
```

**Priority 3: Sentiment Extreme - Execute Within 2 Days**

```
TRIGGER: MSPA flags greed drops from 8 to 5 in single day
STATUS: Sentiment exhaustion, potential reversal

IMMEDIATE ACTIONS (Day 2 if greed holds below 7):
  1. Confirm sentiment drop held:
     Day 1: Greed 8 → 5 (major shift)
     Day 2: Greed 6 (held low, not reversal)
  
  2. Take profits on concentration:
     - Trim Mag7 50% (lock in gains)
     - Lock in profits from best performers
     - Raise cash for rebounds
  
  3. Monitor insider activity:
     - If insiders buying spikes = bottom signal
     - If insiders selling accelerates = further downside
  
  4. Position for capitulation:
     - Add quality growth on weakness
     - Wait for greed 2-3 range for additions

OUTPUT:
{
  "trigger": "Sentiment extreme - greed collapse",
  "detection": "Day 2 confirmation",
  "execution_deadline": "During Day 2 strength",
  "action": "Trim Mag7 50%, lock profits, raise cash, wait for capitulation"
}
```

---

## SECTION 6: DECLINING CONVICTION ENFORCEMENT

### **Purpose**
Automatically enforce conviction thresholds that increase over time (Month 1→12).

### **Algorithm: Conviction Check**

**For each position, check each Tuesday:**

```
STEP 1: Determine holding period
  Holding months = (Today - Entry Date) / 30 days
  Example: Held 6 months = Conviction threshold 85%

STEP 2: Determine current conviction
  From v1.4.1 history: Conviction 82%

STEP 3: Compare vs. threshold
  IF Conviction > Threshold:
    ✓ Position justified, hold
  
  IF Conviction = Threshold:
    ✓ Position at threshold, watch closely (one bad quarter = exit)
  
  IF Conviction < Threshold:
    ✗ Position unjustified, execute trim
    Trim amount = Position × (1 - Conviction/Threshold)
    Example: 4% position, 82% conviction, 85% threshold
             Trim = 4% × (1 - 82/85) = 4% × 3.5% = 0.14%
             Result: Cut to 3.86%

STEP 4: Framework message to you
  IF position trimmed:
    "Microsoft [finance:Microsoft Corporation] conviction declined to 82%, 
     below 85% threshold (6-month holding). Position trimmed 4% → 3.86%. 
     Conviction must improve by Month 9 or position exits."
```

### **Conviction Enforcement Example**

```
MICROSOFT [FINANCE:MICROSOFT CORPORATION] (Hypothetical)

Nov 2024 (Entry):
  Conviction: 85%
  Threshold: 70% (Month 1)
  Status: JUSTIFIED ✓ Position 5% → Taken

Feb 2025 (Month 3):
  Conviction: 85% (unchanged)
  Threshold: 80% (raised)
  Status: JUSTIFIED ✓ Position 5% → Held

May 2025 (Month 6):
  Conviction: 82% (declined)
  Threshold: 85% (raised)
  Status: UNJUSTIFIED ✗ Position 5% → Trim to 3.87%
  Framework message: "Conviction below threshold, position reduced"

Aug 2025 (Month 9):
  Conviction: 80% (continued decline)
  Threshold: 90% (raised further)
  Status: UNJUSTIFIED ✗ Position 3.87% → Trim to 1.94%
  Framework message: "Conviction well below threshold (80% vs 90% needed). 
                      Position near exit. Conviction must improve by Month 12 or exit."

Nov 2025 (Month 12):
  Conviction: 78% (continued decline)
  Threshold: 90% (at maximum)
  Status: UNJUSTIFIED ✗ EXIT entire position
  Framework message: "Conviction threshold breached. Position 1.94% → EXIT"
```

---

## SECTION 7: WEEKLY EXECUTION CYCLE

### **Purpose**
Operationalize the complete analysis-to-execution workflow on weekly basis.

### **Weekly Schedule**

**Tuesday 6:00 AM (Pre-Market):**
```
1. Run all 4 analysts (QMA, FMA, IRM, MSPA)
   Inputs: Friday close data through Monday evening
   Outputs: 4 JSON analyst reports
   
2. Synthesis runs (Section 1 of PROMPT_SYNTHESIS_INTEGRATION.md)
   Inputs: 4 analyst outputs
   Outputs: Market regime + confidence + transition risk
   
3. Sector ranking updates (this section, Section 2)
   Inputs: Latest sector performance data
   Outputs: Sector leadership rankings
   
4. All calculations complete by 6:30 AM
```

**Tuesday 9:30 AM (Market Open):**
```
5. Run portfolio analysis (Section 7 of PROMPT_SYNTHESIS_INTEGRATION.md)
   Inputs: Your current portfolio screenshot
   Outputs: Weak/strong stocks, rebalancing recommendations
   
6. Run candidate screening (Section 8 of PROMPT_SYNTHESIS_INTEGRATION.md)
   Inputs: Seeking Alpha screenshot (sector ratings, Alpha Picks)
   Outputs: Top 5-10 candidates ranked by fit
   
7. Translate to v1.4.1 (Section 4 of PROMPT_SYNTHESIS_INTEGRATION.md)
   Inputs: Market regime + portfolio actions + candidates
   Outputs: Market context ready for stock analysis
   
8. All outputs ready for presentation by 10:00 AM
```

**Tuesday 10:00 AM (Your Review):**
```
9. Framework presents synthesis to you:
   - Market regime + confidence
   - Portfolio rebalancing recommendations
   - Top stock candidates
   - Conflicts (if any: macro vs. micro)
   
10. YOUR DECISION REQUIRED:
    - Approve market regime classification?
    - Approve portfolio rebalancing actions?
    - Which candidates to analyze in v1.4.1?
    - Any overrides or adjustments?
```

**Tuesday 10:30 AM (Execution):**
```
11. You execute trades:
    - Trim/sell weak positions
    - Hold strong positions
    - Add new candidates after v1.4.1 analysis
    
12. Framework updates portfolio with actual fills
    - Triggers applied
    - Positions updated
    - Conviction thresholds checked (declining conviction governance)
```

**Tuesday-Friday (Monitoring):**
```
13. Real-time trigger monitoring:
    - Watch for regime shift (breadth <50%)
    - Watch for correlation spike (>0.75)
    - Watch for sentiment extreme (greed drops >3)
    
14. IF trigger fires:
    - Execute same-day/1-day rebalancing (see Section 5)
    - Framework updates portfolio
    - Notification to you
```

**Following Monday (Weekly Review):**
```
15. Review of past week:
    - Did regime transition occur? When?
    - Did any triggers execute? Results?
    - Portfolio performance vs. market
    - Conviction updates (any declines below threshold?)
    
16. Prepare for Tuesday synthesis cycle
```

---

## END: PROMPT_MARKET_DECISION_ENGINE.md

---

**Complete operational algorithms for:**
✅ Regime classification (decision tree, confidence scoring)
✅ Sector leadership (ranking algorithm, divergence penalties)
✅ Position sizing (formula with multipliers, guardrails)
✅ Rebalancing decisions (trim/hold/add logic)
✅ Real-time trigger execution (priorities 1-3)
✅ Declining conviction enforcement (automatic trimming)
✅ Weekly execution cycle (6 AM to following Monday)