# PROMPT_ACTIONABLE_TECHNICALS.md

**VERSION:** 1.0  
**STATUS:** Decision-Focused Technical Analysis  
**PURPOSE:** Condensed 1-page technical setup (vs. traditional 3-5 pages)  
**ROLE IN FRAMEWORK:** Section 2 of Actionable Report - shows setup without methodology

---

## TEMPLATE: TECHNICAL ANALYSIS (1 Page)

```
═══════════════════════════════════════════════════════════════════════════════
CURRENT REGIME & PRICE STRUCTURE
═══════════════════════════════════════════════════════════════════════════════

Market Regime: [UPTREND / DOWNTREND / RANGE / VOLATILE]
Primary Trend Direction: [STRONG UP / UP / NEUTRAL / DOWN / STRONG DOWN]
Volatility Status: [ELEVATED / NORMAL / COMPRESSED]

Support Levels:
  • STRONG SUPPORT: $[X] (Fib/moving avg/previous breakout) ← Most important
  • MEDIUM SUPPORT: $[Y] (Psychological/technical)
  • WEAK SUPPORT: $[Z] (Trailing support)

Resistance Levels:
  • WEAK RESISTANCE: $[A] (Psychological/recent high)
  • MEDIUM RESISTANCE: $[B] (Technical/moving avg)
  • STRONG RESISTANCE: $[C] (Fib/psychological round number) ← Most important

═══════════════════════════════════════════════════════════════════════════════
KEY INDICATOR STATUS (Real-Time Summary)
═══════════════════════════════════════════════════════════════════════════════

MOMENTUM INDICATORS:
  • RSI (14): [XX] ([OVERBOUGHT/NEUTRAL/OVERSOLD])
  • MACD: [BULLISH/BEARISH/NEUTRAL] (signal [ABOVE/BELOW/AT] zero line)
  • Rate of Change: [ACCELERATING/STEADY/DECELERATING]

TREND CONFIRMATION:
  • 50-day MA: [ABOVE/BELOW] current price (confirmation: YES/NO)
  • 200-day MA: [ABOVE/BELOW] current price (long-term trend: YES/NO)
  • Volume Trend: [UP / STABLE / DOWN] (conviction: [STRONG/MODERATE/WEAK])

VOLATILITY MEASURE:
  • ATR (Average True Range): $[X] / [Y]% of price (daily movement range)
  • BB Squeeze: [YES/NO] (bands [WIDE/NARROW] - mean reversion vs breakout)

═══════════════════════════════════════════════════════════════════════════════
BACKTEST WINNER: Best Strategy for Current Regime
═══════════════════════════════════════════════════════════════════════════════

STRATEGY NAME: [e.g., \"Momentum Breakout\", \"Mean Reversion\", \"Trend Following\"]
LOOKBACK PERIOD: [e.g., \"1-Year\", \"3-Month\"]
HISTORICAL RETURN: [+XX]% (vs. [+YY]% buy-and-hold)
RISK METRIC (Sharpe Ratio): [X.XX] ([EXCELLENT / GOOD / ACCEPTABLE / POOR])
WIN RATE: [XX]% (profitable trades / total trades)
PROFIT FACTOR: [X.XX] (gross profit / gross loss)
VALIDATION: ✓ PASS [out-of-sample return [+XX]%, walk-forward verified]

**INTERPRETATION:** This strategy performed best historically in [REGIME]. Recommended entry/exit below follows this logic.

═══════════════════════════════════════════════════════════════════════════════
ENTRY & EXIT SETUP (Actionable Price Points)
═══════════════════════════════════════════════════════════════════════════════

ENTRY TRIGGER: Price at $[X] with [specific condition met]
  • Condition: [e.g., \"RSI >70 + MACD above zero\", \"break above $145\", \"close above 50-day MA\"]
  • Entry Price: $[X]-$[Y] (specific range, not vague)

INITIAL STOP LOSS: $[Z] (hard stop, [X]% below entry)
  • If hit: Exit entire position, thesis broken

FIRST PROFIT TARGET: $[A] (scale out [Y]% of position here)
  • Condition: [e.g., \"first Fib resistance\", \"RSI >80\"]

SECOND PROFIT TARGET: $[B] (scale out another [Y]% here)
  • Condition: [e.g., \"daily close above $150\"]

HOLD REMAINDER: Until [condition met]
  • Final exit: [When to sell remaining position, e.g., \"RSI divergence\", \"break below support\"]

═══════════════════════════════════════════════════════════════════════════════
RISK/REWARD SETUP & BREAKEVEN
═══════════════════════════════════════════════════════════════════════════════

Entry Price: $[X]
Stop Loss: $[Y] ([Z]% risk)
First Target: $[A] ([Z]% gain, ratio [X]:[1])
Probability of Success: [XX]% (based on backtest)

RISK/REWARD RATIO: [X]:[1] ([EXCEPTIONAL / FAVORABLE / ACCEPTABLE / POOR])
  • For every $1 risked, you can make $[X] (if targets hit)

PROBABILITY-WEIGHTED EXPECTED VALUE: [+X]% 
  • (Probability of win × target gain) - (Probability of loss × stop loss)

```

---

## WRITING GUIDELINES FOR TECHNICAL ANALYSIS

### What to INCLUDE (Decision-Relevant Information)

1. **Regime Classification** - Is this trending up, down, or sideways? Determines entry strategy.
2. **Key Support/Resistance** - Where will price likely reverse? These are entry/exit points.
3. **Indicator Status** - What are RSI, MACD, volume saying RIGHT NOW? Confirm entry condition.
4. **Backtest Winner** - Which strategy worked historically? Validates our entry logic.
5. **Entry/Exit Setup** - Specific price points user can execute on (not vague "when conditions align")
6. **Risk/Reward Ratio** - Is this worth the risk? Decision filter.

### What to EXCLUDE (Non-Decision-Relevant Information)

- ❌ ALL indicator formulas (e.g., "RSI = 100 - (100 / (1 + RS))" - not needed to use RSI)
- ❌ Historical context or "why we use this indicator" (methodological background)
- ❌ Comparison to other indicators (e.g., "MACD is better than Stochastic")
- ❌ Detailed calculations (e.g., "200-day moving average = sum of last 200 closes / 200")
- ❌ Multi-page technical explanation (save for appendix if user wants depth)
- ❌ Alternative entry signals (give ONE best setup, not "you could also...")

### Specificity Requirements

**DO:**
- "Entry at $145-150 if RSI >70 AND MACD crosses above zero line"
- "Stop loss at $130 (hard stop, 3% risk)"
- "Scale out 50% at $165 (first target), remainder at $180"

**DON'T:**
- "Entry near key resistance" (where is key resistance? $144? $150? $160?)
- "Stop below support" (which support level? What is it?)
- "Take profits on strength" (when exactly? Price? Indicator?)

### Regime-Specific Language

**UPTREND:**
- "RSI should stay above 40 on dips"
- "Break above [level] confirms continuation"
- "Support holds = add to position"
- "Targets: [level 1], [level 2], [level 3]"

**DOWNTREND:**
- "RSI should stay below 60 on bounces"
- "Break below [level] confirms continuation"
- "Resistance holds = reduce position"
- "Targets: [level 1], [level 2] on the downside"

**RANGE:**
- "Buy support at $[X], sell resistance at $[Y]"
- "Mean reversion strategy: oversold = buy, overbought = sell"
- "Stop loss: break outside range"

---

## EXAMPLE 1: ACTIONABLE TECHNICAL (UPTREND REGIME)

```
═══════════════════════════════════════════════════════════════════════════════
CURRENT REGIME & PRICE STRUCTURE
═══════════════════════════════════════════════════════════════════════════════

Market Regime: UPTREND (with pullback)
Primary Trend Direction: STRONG UP (higher highs/lows intact)
Volatility Status: NORMAL

Support Levels:
  • STRONG SUPPORT: $142.15 (50-day MA + psychological) ← Most important
  • MEDIUM SUPPORT: $138.50 (Fibonacci 38.2% retracement)
  • WEAK SUPPORT: $135.00 (psychological round number)

Resistance Levels:
  • WEAK RESISTANCE: $155.00 (recent intraday high)
  • MEDIUM RESISTANCE: $158.75 (Fibonacci 61.8% extension)
  • STRONG RESISTANCE: $165.00 (52-week high target) ← Most important

═══════════════════════════════════════════════════════════════════════════════
KEY INDICATOR STATUS (Real-Time Summary)
═══════════════════════════════════════════════════════════════════════════════

MOMENTUM INDICATORS:
  • RSI (14): 58 (NEUTRAL - room to run, not yet overbought)
  • MACD: BULLISH (signal line above zero, histogram positive)
  • Rate of Change: ACCELERATING (momentum strengthening)

TREND CONFIRMATION:
  • 50-day MA: $142.15, current $147.30 (ABOVE - confirmation: YES)
  • 200-day MA: $138.00, current $147.30 (ABOVE - long-term trend: YES)
  • Volume Trend: UP (volume on up days > down days, conviction: STRONG)

VOLATILITY MEASURE:
  • ATR (14): $2.85 / 1.9% of price (typical daily movement)
  • BB Squeeze: NO (bands wide, breakout mode not squeeze mode)

═══════════════════════════════════════════════════════════════════════════════
BACKTEST WINNER: Best Strategy for Current Regime
═══════════════════════════════════════════════════════════════════════════════

STRATEGY NAME: Momentum Breakout
LOOKBACK PERIOD: 1-Year (current conditions)
HISTORICAL RETURN: +48.4% (vs. +14.2% buy-and-hold)
RISK METRIC (Sharpe Ratio): 7.04 (EXCELLENT - best risk-adjusted returns)
WIN RATE: 50% (50 profitable trades out of 100)
PROFIT FACTOR: 5.10 (for every $1 lost, made $5.10)
VALIDATION: ✓ PASS (out-of-sample return +43.2%, walk-forward verified Oct-Nov 2024)

**INTERPRETATION:** In strong uptrends with momentum, breakout buys at resistance have historically worked best. Entry on momentum confirmation at $150 should work.

═══════════════════════════════════════════════════════════════════════════════
ENTRY & EXIT SETUP (Actionable Price Points)
═══════════════════════════════════════════════════════════════════════════════

ENTRY TRIGGER: Price rallies to $150-152 with confirmed MACD above zero
  • Condition: Close above $150 on volume (>1M shares), RSI >60
  • Entry Price: $150-152 (buy on confirmation, not breakout front-run)

INITIAL STOP LOSS: $142.00 (hard stop at -5.4% below entry)
  • If hit: Exit entire position, downtrend reversal probable

FIRST PROFIT TARGET: $158.75 (Fib 61.8% extension - scale out 50%)
  • Condition: RSI approaches 75 (take profits into strength)

SECOND PROFIT TARGET: $165.00 (52-week high breakout - scale out another 25%)
  • Condition: Daily close above $160 confirms strength

HOLD REMAINDER: Until MACD divergence or close below $155
  • Final exit: Break and close below 50-day MA ($142.15) = full exit

═══════════════════════════════════════════════════════════════════════════════
RISK/REWARD SETUP & BREAKEVEN
═══════════════════════════════════════════════════════════════════════════════

Entry Price: $151
Stop Loss: $142 (9 points / 6% risk)
First Target: $158.75 (7.75 points / 5.1% gain, ratio 1:0.86)
Second Target: $165 (14 points / 9.3% gain)

RISK/REWARD RATIO: 1:1.6 (FAVORABLE - more upside than downside risk)
  • For every $1 risked, you can make $1.60 on base case

PROBABILITY-WEIGHTED EXPECTED VALUE: +6.8%
  • (50% probability × 5.1% gain) + (40% probability × 9.3% gain) - (10% probability × 6% loss)
```

---

## EXAMPLE 2: ACTIONABLE TECHNICAL (RANGE REGIME WITH OVERSOLD SIGNAL)

```
═══════════════════════════════════════════════════════════════════════════════
CURRENT REGIME & PRICE STRUCTURE
═══════════════════════════════════════════════════════════════════════════════

Market Regime: RANGE (trading $14.50-$17.50 for 8 weeks)
Primary Trend Direction: NEUTRAL (no sustained up or down)
Volatility Status: COMPRESSED (setting up for breakout)

Support Levels:
  • STRONG SUPPORT: $14.50 (psychological round number + Fib support) ← Most important
  • MEDIUM SUPPORT: $15.00 (previous range low)
  • WEAK SUPPORT: $15.50 (moving average support)

Resistance Levels:
  • WEAK RESISTANCE: $16.50 (intraday high)
  • MEDIUM RESISTANCE: $17.00 (Fibonacci extension)
  • STRONG RESISTANCE: $17.50 (range high, previous breakout fail) ← Most important

═══════════════════════════════════════════════════════════════════════════════
KEY INDICATOR STATUS (Real-Time Summary)
═══════════════════════════════════════════════════════════════════════════════

MOMENTUM INDICATORS:
  • RSI (14): 31.2 (OVERSOLD - exhaustion, reversal likely)
  • MACD: BEARISH but FLATTENING (histogram bars shrinking, about to cross)
  • Rate of Change: DECELERATING (momentum weakening on downside, recovery setting up)

TREND CONFIRMATION:
  • 50-day MA: $16.00, current $15.96 (AT - no clear trend)
  • 200-day MA: $16.50, current $15.96 (BELOW - still in range)
  • Volume Trend: DOWN (volume on down days < up days, exhaustion = reversal)

VOLATILITY MEASURE:
  • ATR (14): $0.42 / 2.6% of price (very low - bands squeezed, breakout imminent)
  • BB Squeeze: YES (bands very narrow - mean reversion setup strong)

═══════════════════════════════════════════════════════════════════════════════
BACKTEST WINNER: Best Strategy for Current Regime
═══════════════════════════════════════════════════════════════════════════════

STRATEGY NAME: Mean Reversion + RSI Divergence
LOOKBACK PERIOD: 3-Month (range formation)
HISTORICAL RETURN: +38.5% (vs. +22.1% buy-and-hold)
RISK METRIC (Sharpe Ratio): 3.87 (GOOD - strong risk-adjusted performance)
WIN RATE: 62% (buy support = 62 profitable out of 100 trades)
PROFIT FACTOR: 4.15 (for every $1 lost, made $4.15)
VALIDATION: ✓ PASS (out-of-sample return +34.2%, walk-forward verified Aug-Nov 2024)

**INTERPRETATION:** In tight ranges with oversold RSI, bounces from support work 62% of the time. Current setup is textbook mean reversion.

═══════════════════════════════════════════════════════════════════════════════
ENTRY & EXIT SETUP (Actionable Price Points)
═══════════════════════════════════════════════════════════════════════════════

ENTRY TRIGGER: RSI bounces from 31 back above 40 (reversal confirmation)
  • Condition: Close above $15.80 with volume >500K shares, MACD crosses zero
  • Entry Price: $15.96 (now) OR wait for pullback to $15.00

INITIAL STOP LOSS: $14.04 (hard stop at support below, -12% risk)
  • If hit: Break of support invalidates entire setup

FIRST PROFIT TARGET: $16.80 (midpoint of range - scale out 50%)
  • Condition: RSI >60 (overbought after oversold recovery)

SECOND PROFIT TARGET: $17.50 (range high - scale out another 25%)
  • Condition: Daily close above $17.25 confirms breakout above range

HOLD REMAINDER: Until close below $16.00 (back into range = exit)
  • Final exit: Break and close below $14.50 (hard support breaks)

═══════════════════════════════════════════════════════════════════════════════
RISK/REWARD SETUP & BREAKEVEN
═══════════════════════════════════════════════════════════════════════════════

Entry Price: $15.96
Stop Loss: $14.04 (1.92 points / 12% risk)
First Target: $16.80 (0.84 points / 5.3% gain, ratio 1:0.44)
Second Target: $17.50 (1.54 points / 9.6% gain)

RISK/REWARD RATIO: 1:0.8 on first target, but 62% win rate makes it +EV
  • Lower reward per target, but high win rate and follow-on targets

PROBABILITY-WEIGHTED EXPECTED VALUE: +4.2%
  • (62% probability × 5.3% gain) + (48% probability × 9.6% follow-on) - (38% probability × 12% loss)
```

---

## QUALITY CHECKLIST FOR TECHNICAL ANALYSIS

### Must Pass (Hard Requirements)

- ✅ Regime clearly stated (UPTREND/DOWNTREND/RANGE/VOLATILE)
- ✅ Three support levels with specific prices (not "support area")
- ✅ Three resistance levels with specific prices (not "resistance zone")
- ✅ RSI value shown (e.g., "58" not "neutral")
- ✅ MACD status clear (BULLISH/BEARISH/NEUTRAL with reference level)
- ✅ Moving averages referenced (50-day, 200-day with specific prices)
- ✅ Volume trend stated (UP/STABLE/DOWN)
- ✅ Backtest winner named with specific performance metrics
- ✅ Backtest Sharpe ratio shown (qualitative assessment: EXCELLENT/GOOD/ACCEPTABLE)
- ✅ Win rate documented (percentage of profitable trades)
- ✅ Entry trigger is specific (price + condition, not vague)
- ✅ Stop loss is specific $ amount (not "below support")
- ✅ First and second targets are specific prices
- ✅ Risk/reward ratio calculated and stated
- ✅ Entry, stop, and targets all have clear probability ratios

### Tone & Language

- ✅ Technical but accessible (investor, not quant researcher)
- ✅ Current state (right now, today)
- ✅ Action-oriented (what setup is happening)
- ✅ No hedging (not "might be", "could be")

---

## INTEGRATION NOTE

This Technical Analysis is **SECTION 2** of the new Actionable Reporting output.

**Sections in order:**
1. Executive Summary (1 page)
2. Technical Setup (THIS SECTION - 1 page)
3. Fundamental Case (1 page)
4. Position Management (½ page)

**Purpose:** After reading Executive Summary, user wants to know: "What is the technical setup? When do I enter? Where do I get out?"

This section answers those questions in 3-5 minutes without methodology buried in text.
