# Stock-Analyst Backtesting: What's Missing vs What You Need

**Current:** Stock-Analyst v8.15.2 (Space, file:132)  
**Analysis Date:** December 24, 2025

---

## THE SHORT ANSWER

**NO - Current Stock-Analyst.md (v8.15.2) has NO backtesting framework.**

v8.15.2 covers:
- ✅ Golden Cross signal detection (+5/+2/0 pts)
- ✅ Death Cross signal detection (-5/-2/0 pts)
- ✅ Conflict resolution (both signals = ignore both)
- ✅ Technical conviction adjustments
- ❌ **NO Section 14 (Backtesting Framework)**
- ❌ **NO validation metrics** (win rate, Sharpe, profit factor, max DD)
- ❌ **NO historical performance measurement**
- ❌ **NO "No Signal" baseline comparison**

---

## WHAT v8.15.2 CURRENTLY DOES

**Input:** Stock ticker (e.g., CAAP, ENB, KGC)

**Process (5 tiers):**
1. **Tier 0:** Data verification (spot prices, macro data)
2. **Tier 1:** Fundamental analysis (valuation, growth, quality)
3. **Tier 2:** Technical gates (moving averages, RSI, volume)
   - **Tier 3.4A:** Golden Cross validation (+5/-2/0)
   - **Tier 3.4B:** Death Cross validation (-5/-2/0) ← NEW in v8.15.2
4. **Tier 4:** Earnings fundamentals (recent reports, guidance)
5. **Output:** Recommendation + conviction score (0-100)

**Example Output for CAAP:**
```
BUY | Conviction 71 (HIGH)
Position size: 1.5–2.0%
Entry: $25–27
Stop: $22.50
Target: $29–35
Reasoning: Operating leverage thesis + Golden Cross signal +3 pts
```

**What's missing:** Proof that the signals actually work (backtesting)

---

## WHAT v8.15.3 ADDS (In Workspace)

**New:** Section 14 - Backtesting Framework

**Backtesting Process:**
1. Collect 18+ historical picks with entry/exit dates, prices, signals
2. Classify each pick: GC / DC / Conflict / No Signal
3. Measure outcomes: WIN / LOSS / FLAT
4. Calculate metrics:
   - Win rates by signal type
   - Sharpe-like ratio (Return / Volatility)
   - Profit factor (Winners ÷ Losers)
   - Max drawdown
5. Compare to "No Signal" baseline
6. Validate success criteria:
   - GC win rate ≥70% ✓
   - Sharpe-like >1.0 ✓
   - Profit factor >1.5 ✓
   - Max drawdown <25% ✓

**Example Output:**
```json
{
  "backtest_date": "2025-12-23",
  "picks_tested": 16,
  "golden_cross": {
    "count": 9,
    "wins": 7,
    "win_rate_pct": 77.8,
    "sharpe_like": 1.85,
    "profit_factor": 4.5
  },
  "recommendation": "VALIDATED - Signals working as designed"
}
```

---

## THE GAP: v8.15.2 vs v8.15.3

### v8.15.2 (Current - Production)
**Question it answers:** "What should I do with this stock?"  
**Framework:** Fundamental + technical signals  
**Output:** BUY/HOLD/SELL recommendation + conviction score  
**Validation:** None (signals assumed to work)  
**Use case:** Individual stock analysis

---

### v8.15.3 (New - In Workspace)
**Question it answers:** "Do my signals actually work?"  
**Framework:** Historical backtesting of signal effectiveness  
**Output:** Win rates, Sharpe, profit factor, recommendation (VALIDATED/NEEDS TUNING/REJECT)  
**Validation:** Empirical proof that signals improve picks  
**Use case:** Signal validation + continuous improvement

---

## WHY THIS MATTERS FOR CAAP

### Using v8.15.2 Alone
```
Analysis says: BUY CAAP
Conviction: 71/100 (HIGH)
Golden Cross: +3 pts
Position: 1.5–2.0%

Question: But does the Golden Cross signal actually work?
Answer (v8.15.2): Unknown - no backtesting framework
```

### Using v8.15.3 Section 14
```
Historical backtest (16 picks):
Golden Cross win rate: 77.8%
Sharpe-like: 1.85 (target: >1.0)
Profit factor: 4.5x (target: >1.5)
Max drawdown: 8% (target: <25%)

Recommendation: VALIDATED - Your signals work
Therefore: You can confidently add to 2.0–2.5% as planned
```

---

## THE WORKFLOW DIFFERENCE

### v8.15.2 Workflow (Current)
```
Input: CAAP stock
     ↓
Run analysis (Tiers 0–4)
     ↓
Output: BUY, conviction 71
     ↓
Decision: Trust the signal? (subjective)
     ↓
Action: Add to portfolio or not? (uncertain)
```

### v8.15.3 Workflow (New)
```
Input: 16 historical picks (with signals marked)
     ↓
Run backtest (Section 14)
     ↓
Output: Win rate 77%, Sharpe 1.85, Profit factor 4.5x
     ↓
Validate: All metrics exceed thresholds? YES
     ↓
Recommendation: VALIDATED - Signals work
     ↓
Action: Confidently deploy signals at 1.5–2.5% conviction sizing
```

---

## DO YOU NEED SECTION 14?

### If You Want to Know...

**"Should I use v8.15.2 signals for stock picks?"** → YES, add Section 14

**"Are my Golden Cross + Death Cross signals effective?"** → YES, add Section 14

**"What's my historical win rate with these signals?"** → YES, add Section 14

**"Can I automate stock selection based on signals?"** → YES, add Section 14

**"How much conviction should I assign to each signal?"** → YES, add Section 14 (empirical basis)

---

### If You Don't Care...

**"I'm comfortable with subjective judgment on each pick"** → No, v8.15.2 is fine

**"I trust my fundamental analysis, signals are just helpers"** → No, v8.15.2 is fine

**"I don't need to validate signals, just want recommendations"** → No, v8.15.2 is fine

---

## WHAT YOU SHOULD DO

### Option A: Keep Using v8.15.2 Alone
**Pros:**
- ✅ Fast (no backtesting needed)
- ✅ Focuses on fundamental analysis
- ✅ Signals are secondary confirmations

**Cons:**
- ❌ No proof signals work
- ❌ No empirical conviction basis
- ❌ Can't measure signal effectiveness over time

---

### Option B: Add v8.15.3 Section 14 to Space (Recommended)
**Workflow:**
1. Run v8.15.2 analysis → Get recommendation + conviction score
2. Quarterly: Collect picks + backtest using Section 14 → Validate signals
3. If VALIDATED: Confidence increases, can size to conviction (1.5–2.5%)
4. If NEEDS TUNING: Adjust signals, re-test next quarter
5. If REJECTED: Signals aren't working, back to pure fundamentals

**Pros:**
- ✅ Empirical signal validation
- ✅ Continuous improvement cycle
- ✅ Conviction tied to evidence, not guessing
- ✅ Quarterly reviews keep signals sharp

**Cons:**
- ⚠️ Requires collecting 15+ historical picks
- ⚠️ Quarterly effort to backtest

---

## IMMEDIATE DECISION FOR CAAP

### Right Now (Dec 24, 2025)
**Using v8.15.2 alone:**
- CAAP analysis: Conviction 71, Golden Cross +3 pts
- Decision: Add to 2.0–2.5% or not? (subjective)

**Using v8.15.3 with historical data:**
- You ran v1.0 backtest on 16 picks
- Expected: Win rate 60–70%, Sharpe 1.5–2.0, Profit factor 2.5–3.5x
- If ALL validate: CAAP conviction 71 is empirically justified
- Decision: Add to 2.0–2.5% with high confidence

**Recommendation:** Use v1.0 backtest results to validate, then commit to CAAP

---

## INTEGRATION PATH

### Phase 1 (Now)
- ✅ v8.15.2 analysis (done for CAAP)
- ✅ v1.0 backtest validation (you already did this)
- ✅ Decision on CAAP (add to 2.0–2.5%)

### Phase 2 (Q1 2026)
- Add v8.15.3 Section 14 to Stock-Analyst.md (Space)
- Quarterly backtest on new picks
- Refine conviction sizing based on empirical win rates

### Phase 3 (Q2+ 2026)
- Continuous improvement cycle
- Track signal effectiveness
- Adjust conviction adjustments based on data

---

## SUMMARY

| Capability | v8.15.2 | v8.15.3 |
|:---|:---|:---|
| Individual stock analysis | ✅ YES | ✅ YES |
| Golden Cross / Death Cross detection | ✅ YES | ✅ YES |
| Conviction scoring | ✅ YES | ✅ YES |
| Signal validation (backtest) | ❌ NO | ✅ YES |
| Historical win rate measurement | ❌ NO | ✅ YES |
| Profit factor calculation | ❌ NO | ✅ YES |
| Quarterly signal reviews | ❌ NO | ✅ YES |

**For CAAP decision:** Use v1.0 backtest results + v8.15.2 analysis together  
**For long-term:** Add v8.15.3 Section 14 to systematize signal validation

---

**Bottom line:** v8.15.2 recommends stocks. v8.15.3 Section 14 validates that the recommendation logic works. Both are useful; they serve different purposes.
