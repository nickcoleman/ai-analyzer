# Stock-Analyst v8.15.3 Enhancements Roadmap

**Date:** December 24, 2025  
**Phase:** Planning & Specification  
**Status:** Ready for implementation (Q1 2026)

---

## EXECUTIVE SUMMARY

v8.15.2 validates Golden Cross and Death Cross signals. v8.15.3 adds **four technical indicators** to increase conviction granularity and reduce false signals.

**New Indicators:**
1. **RSI (Relative Strength Index)** — Momentum confirmation
2. **MACD (Moving Average Convergence Divergence)** — Trend continuation
3. **Bollinger Bands** — Volatility & reversal detection
4. **Volume Confirmation** — Signal strength validation

**Expected Impact:**
- Golden Cross win rate: 77.8% → ~82–85% (with RSI+MACD filters)
- False signal reduction: ~15–20%
- Conviction accuracy: +3–5 points per indicator

---

## INDICATOR SPECIFICATIONS

### 1. RSI (Relative Strength Index) - TIER 3.5A

**Purpose:** Detect overbought/oversold conditions and momentum divergence

**Calculation:**
- 5-period RSI: Fast momentum (intraday signal timing)
- 21-period RSI: Medium momentum (signal confirmation)

**Logic:**
```
RSI(5) > 70 AND RSI(21) > 70 → Overbought (bearish divergence risk)
RSI(5) < 30 AND RSI(21) < 30 → Oversold (bullish reversal)
RSI(5) > RSI(21) AND Golden Cross → Confirmation (+2 pts)
RSI(5) < RSI(21) AND Golden Cross → Weakness (-1 pt)
```

**Golden Cross Integration:**
- ✅ Active GC + RSI(5) > 50 → +2 conviction pts (momentum confirms trend)
- ✅ Active GC + RSI(5) < 30 → +0 pts (momentum too weak, wait for reversal)
- ✅ Active GC + RSI(5) > 70 → +1 pt (overbought risk, but trend still valid)

**Death Cross Integration:**
- ✅ Active DC + RSI(5) < 50 → -2 conviction pts (momentum confirms downtrend)
- ✅ Active DC + RSI(5) > 70 → -0 pts (momentum divergence, ignore DC)

**Expected Impact:**
- Reduces false GC signals (when momentum is weak)
- Adds conviction when momentum aligns with trend
- **Conviction adjustment: +2 to -1 pts**

**API Cost:** 1 API call (reuses OHLC from Golden Cross)

---

### 2. MACD (Moving Average Convergence Divergence) - TIER 3.5B

**Purpose:** Confirm trend direction and detect momentum shifts

**Calculation:**
- MACD Line = 12-day EMA − 26-day EMA
- Signal Line = 9-day EMA of MACD
- Histogram = MACD − Signal Line

**Logic:**
```
MACD > Signal AND Histogram > 0 → Bullish momentum (uptrend)
MACD < Signal AND Histogram < 0 → Bearish momentum (downtrend)
MACD crossing Signal → Momentum shift (trend reversal warning)
Histogram growing → Momentum strengthening
Histogram shrinking → Momentum weakening
```

**Golden Cross Integration:**
- ✅ GC + MACD > Signal + Histogram > 0 → +3 conviction pts (strong confirmation)
- ✅ GC + MACD crossing above Signal → +2 pts (momentum turning bullish)
- ✅ GC + MACD < Signal → +0 pts (conflicting signals, wait for clarity)
- ✅ GC + MACD crossing below Signal → -2 pts (momentum divergence warning)

**Death Cross Integration:**
- ✅ DC + MACD < Signal + Histogram < 0 → -3 pts (strong confirmation)
- ✅ DC + MACD crossing below Signal → -2 pts (momentum turning bearish)

**Expected Impact:**
- Confirms Golden Cross with momentum direction
- Early warning of trend reversal (MACD cross before price)
- **Conviction adjustment: +3 to -2 pts**

**API Cost:** 1 API call (reuses OHLC from Golden Cross)

---

### 3. Bollinger Bands - TIER 3.5C

**Purpose:** Detect volatility extremes and reversal zones

**Calculation:**
- Middle Band = 20-day SMA
- Upper Band = Middle + (2 × 20-day std dev)
- Lower Band = Middle − (2 × 20-day std dev)

**Logic:**
```
Price > Upper Band → Overbought (potential pullback)
Price < Lower Band → Oversold (potential bounce)
Price touching band + distance from band → Signal strength
Band width → Market volatility (wide = volatile, narrow = calm)
```

**Golden Cross Integration:**
- ✅ GC + Price between middle & upper band → +1 pt (healthy pullback room)
- ✅ GC + Price touching upper band → +0 pts (overbought, wait for pullback)
- ✅ GC + Price at lower band → +2 pts (bounce setup, high conviction)

**Death Cross Integration:**
- ✅ DC + Price touching lower band → -0 pts (oversold bounce coming)
- ✅ DC + Price between middle & lower band → -1 pt (weak downtrend)

**Expected Impact:**
- Filters out overbought GC entries
- Identifies best entry zones (lower band breakouts)
- Risk management (know overbought zones)
- **Conviction adjustment: +2 to -1 pts**

**API Cost:** 0 (calculated from OHLC already fetched)

---

### 4. Volume Confirmation - TIER 3.5D

**Purpose:** Ensure price moves are backed by institutional participation

**Calculation:**
- Average Volume (20-day) vs Current Volume
- Volume Ratio = Current Volume / 20-day Avg Volume

**Logic:**
```
Volume Ratio > 1.5 → High volume (institutional, strong signal)
Volume Ratio 1.0–1.5 → Normal volume (weak confirmation)
Volume Ratio < 1.0 → Low volume (retail only, weak signal)

For trend confirmation:
GC + High Volume → Strong bullish signal
GC + Low Volume → Weak bullish signal (likely to fail)
```

**Golden Cross Integration:**
- ✅ GC + Volume Ratio > 1.5 → +1 pt (institutional backing)
- ✅ GC + Volume Ratio 1.0–1.5 → +0 pts (no volume confirmation)
- ✅ GC + Volume Ratio < 1.0 → -1 pt (weak signal, no backing)

**Death Cross Integration:**
- ✅ DC + Volume Ratio > 1.5 → -1 pt (institutional selling)
- ✅ DC + Volume Ratio < 1.0 → +0 pts (no selling volume, ignore DC)

**Expected Impact:**
- Prevents chasing weak signals (low volume GC crosses)
- Validates institutional moves (high volume)
- **Conviction adjustment: +1 to -1 pts**

**API Cost:** 1 API call (volume data from OHLC)

---

## CONVICTION ADJUSTMENT MATRIX

**How all four indicators combine:**

```
Golden Cross Base: +5 pts
├─ RSI alignment:       +2 to -1
├─ MACD alignment:      +3 to -2
├─ Bollinger position:  +2 to -1
└─ Volume confirmation: +1 to -1

Potential Range: +5 + (-2) = +3 minimum (weak GC)
                 +5 + (+8) = +13 maximum (strong GC with all confirmations)

But capped at conviction tier max (see Guardrails v8.1.0)
```

**Example Scoring:**

| Scenario | Base | RSI | MACD | BB | Vol | Total | Action |
|:---|:---|:---|:---|:---|:---|:---|:---|
| Perfect setup | +5 | +2 | +3 | +2 | +1 | +13 → capped 70–75 | Strong BUY ✅ |
| Good setup | +5 | +1 | +2 | +1 | +0 | +9 → 60–65 | BUY |
| Weak setup | +5 | -1 | +0 | -1 | -1 | +2 → 50–55 | HOLD/Wait |
| Conflict | +5 | -1 | -2 | -1 | -1 | 0 → 45–50 | SKIP |

---

## IMPLEMENTATION PRIORITY

### Phase 1 (January 2026) - RSI + MACD
**Why first:**
- Most predictive for momentum
- Low API cost (reuse OHLC)
- High ROI (15–20% false signal reduction)
- Standard technical indicators (proven)

**Estimated impact:** +3–5 conviction pts accuracy

---

### Phase 2 (February 2026) - Bollinger Bands
**Why second:**
- Complements RSI/MACD
- No additional API calls (calculated from OHLC)
- Adds volatility context
- Risk management benefits

**Estimated impact:** +1–2 conviction pts accuracy

---

### Phase 3 (March 2026) - Volume Confirmation
**Why third:**
- Least common (not all stocks have volume data)
- Validates institutional backing
- Last refinement (nice-to-have, not critical)

**Estimated impact:** +0.5–1 conviction pts accuracy

---

## EXPECTED IMPACT ON WIN RATES

### Current v8.15.2 (Baseline)
- Golden Cross picks: 77.8% win rate
- Conviction accuracy: Assumed calibrated
- False signals: ~15–20%

### With RSI + MACD (Phase 1)
- Golden Cross win rate: 82–85% (filter weak signals)
- Conviction accuracy: +3–5 pts improvement
- False signals: Reduced to ~10–12%

### With Bollinger Bands (Phase 2)
- Golden Cross win rate: 84–86% (identify best entry zones)
- Conviction accuracy: +1–2 pts improvement
- Entry timing: Better (lower band breakouts)

### With Volume Confirmation (Phase 3)
- Golden Cross win rate: 85–87% (validate institutional backing)
- Conviction accuracy: +0.5–1 pt improvement
- Risk management: Better (avoid low-volume traps)

---

## RESOURCE ESTIMATE

| Indicator | API Calls | Development | Testing | Integration | Total |
|:---|:---|:---|:---|:---|:---|
| RSI | 0 (reuse OHLC) | 2 hours | 1 hour | 0.5 hours | 3.5 hours |
| MACD | 0 (reuse OHLC) | 2 hours | 1 hour | 0.5 hours | 3.5 hours |
| Bollinger | 0 (calculated) | 1 hour | 0.5 hours | 0.5 hours | 2 hours |
| Volume | 1 (volume data) | 1.5 hours | 0.5 hours | 0.5 hours | 2.5 hours |
| **Total** | | **6.5 hours** | **3 hours** | **2 hours** | **11.5 hours** |

**Effort:** Low-to-medium (1–2 weeks, part-time)

---

## TECHNICAL DEPENDENCIES

**v8.15.3 Requirements:**
- ✅ Finnhub API (already integrated) — provides OHLC data
- ✅ v8.15.2 base framework (Golden/Death Cross logic)
- ✅ Tier 3.4 validators (already exist)
- ✅ EMA calculation function (already exists for Death Cross)
- ✅ Standard deviation calculation (new, but simple)

**No new external dependencies needed.**

---

## PSEUDOCODE TEMPLATES

### RSI Calculation (Tier 3.5A)
```python
def calculate_rsi(closes: List[float], period: int = 14) -> float:
    """
    RSI = 100 - (100 / (1 + RS))
    RS = avg_gain / avg_loss
    """
    deltas = [closes[i] - closes[i-1] for i in range(1, len(closes))]
    gains = [d if d > 0 else 0 for d in deltas]
    losses = [-d if d < 0 else 0 for d in deltas]
    
    avg_gain = sum(gains[-period:]) / period
    avg_loss = sum(losses[-period:]) / period
    
    rs = avg_gain / avg_loss if avg_loss > 0 else 0
    rsi = 100 - (100 / (1 + rs))
    return rsi

# Usage
rsi_5 = calculate_rsi(closes, 5)
rsi_21 = calculate_rsi(closes, 21)
```

### MACD Calculation (Tier 3.5B)
```python
def calculate_macd(closes: List[float]) -> tuple:
    """
    MACD = 12-EMA - 26-EMA
    Signal = 9-EMA of MACD
    Histogram = MACD - Signal
    """
    ema_12 = calculate_ema(closes, 12)
    ema_26 = calculate_ema(closes, 26)
    macd_line = ema_12 - ema_26
    
    # Signal line (9-EMA of MACD line)
    macd_history = [...]  # Previous MACD values
    signal_line = calculate_ema(macd_history, 9)
    
    histogram = macd_line - signal_line
    return macd_line, signal_line, histogram
```

### Bollinger Bands (Tier 3.5C)
```python
def calculate_bollinger_bands(closes: List[float], period: int = 20) -> tuple:
    """
    Middle = SMA(20)
    Upper = Middle + (2 * std_dev)
    Lower = Middle - (2 * std_dev)
    """
    middle = sum(closes[-period:]) / period
    std_dev = (sum((c - middle)**2 for c in closes[-period:]) / period) ** 0.5
    
    upper = middle + (2 * std_dev)
    lower = middle - (2 * std_dev)
    return lower, middle, upper
```

---

## SUCCESS CRITERIA (Post-Implementation)

**Validation Metrics:**
- ✅ Golden Cross win rate improves 77.8% → 82%+ (minimum +4%)
- ✅ Conviction accuracy within ±5 pts (vs actual outcomes)
- ✅ False signal rate drops 15–20% → 10% or less
- ✅ Backtest on historical 16 picks shows improvement
- ✅ CAAP + 3 other picks validate with new indicators

**Performance Criteria:**
- ✅ API calls unchanged (reuse OHLC)
- ✅ Processing speed < 2 seconds per stock
- ✅ No regressions (v8.15.2 picks still validate)

---

## INTEGRATION POINTS (For Next Thread)

**Downstream Systems to Update:**
1. **API-Reference.md** — Add RSI, MACD, Bollinger, Volume specs
2. **Stock-Analyst.md** — Add Tiers 3.5A–D (RSI/MACD/BB/Volume)
3. **AGENT-OUTPUT-SCHEMA.md** — Add new conviction adjustment fields
4. **Conviction-Decomposition-Chart.md** — Show RSI/MACD/BB adjustments
5. **Golden-Cross-Chart.md** — Overlay RSI/MACD/Bollinger visualizations

---

## KNOWN RISKS & MITIGATIONS

| Risk | Impact | Mitigation |
|:---|:---|:---|
| Indicator conflicts (RSI vs MACD) | False signals | Conflict resolution logic (both must agree) |
| Overbought/oversold whipsaws | Choppy entries | Bollinger Band filtering, volume confirmation |
| Back-testing curve-fitting | Overfitting | Walk-forward validation on new picks |
| Indicator lag | Late entries | Use 5/21 RSI for speed vs confirmation |

---

## NEXT STEPS

1. **API-Reference.md Updates** (Next section)
   - Add RSI/MACD/Bollinger/Volume specifications
   - Python implementation code
   - Integration examples

2. **Stock-Analyst.md Implementation** (After API updates)
   - Add Tier 3.5 validators
   - Integrate with Tier 3.4 (Golden/Death Cross)
   - Conviction adjustment logic

3. **Backtesting Validation** (After implementation)
   - Test on 16 historical picks
   - Compare v8.15.2 vs v8.15.3 win rates
   - Validate success criteria

4. **Integration Checklist** (Next thread)
   - All downstream systems updated
   - No regressions
   - Production ready

---

## APPENDIX: Why These Four Indicators?

**Why RSI?**
- Most predictive for momentum shifts
- Simple calculation, no dependencies
- Proven in technical analysis (industry standard)

**Why MACD?**
- Best trend confirmation indicator
- Early warning of trend reversals
- Professional-grade (widely used by institutions)

**Why Bollinger Bands?**
- Identifies volatility extremes
- Entry/exit zones (lower band = buy, upper = sell)
- Risk management (know where overbought)

**Why Volume?**
- Validates signal strength
- Distinguishes institutional vs retail
- Prevents chasing weak signals

**Not Included:**
- Stochastic (redundant with RSI)
- ADX (complex, lower ROI)
- Ichimoku (complex, overkill for Golden Cross)

---

**Status: Ready for API-Reference updates and implementation planning.**
