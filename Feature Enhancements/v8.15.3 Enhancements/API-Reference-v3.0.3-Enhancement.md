# API-Reference v3.0.3: Technical Indicators Enhancement

**Update for:** Stock-Analyst v8.15.3 (RSI, MACD, Bollinger Bands, Volume)  
**Date:** December 24, 2025  
**Status:** Specification Ready (Implementation Q1 2026)

---

## EXECUTIVE SUMMARY

API-Reference.md v3.0.1 provides Golden/Death Cross signals. v3.0.3 adds **four technical indicators** all derived from the same single OHLC API call:

1. **RSI (Relative Strength Index)** — Momentum validation
2. **MACD (Moving Average Convergence Divergence)** — Trend confirmation
3. **Bollinger Bands** — Volatility extremes & entry zones
4. **Volume Confirmation** — Signal strength validation

**Key Principle:** All four indicators use the same 250-day OHLC data already fetched for Golden/Death Cross. **Zero additional API calls.**

---

## SECTION 7: RSI CALCULATION (v3.0.3 NEW)

### Tier 3.5A: RSI (Relative Strength Index)

**Purpose:** Detect overbought/oversold conditions and momentum alignment with trend

**Data Required:** 250 closes (already have from Golden/Death Cross OHLC)

**Calculation:**

```python
def calculate_rsi(closes: List[float], period: int = 14) -> float:
    """
    RSI = 100 - (100 / (1 + RS))
    RS = avg_gain / avg_loss
    
    Args:
        closes: List of closing prices (at least period+1 length)
        period: RSI lookback period (standard: 14, fast: 5, medium: 21)
    
    Returns:
        float: RSI value (0-100)
    """
    if len(closes) < period + 1:
        return None  # Insufficient data
    
    # Calculate price deltas
    deltas = [closes[i] - closes[i-1] for i in range(1, len(closes))]
    
    # Separate gains and losses
    gains = [delta if delta > 0 else 0 for delta in deltas]
    losses = [-delta if delta < 0 else 0 for delta in deltas]
    
    # Average gain and loss over period
    avg_gain = sum(gains[-period:]) / period
    avg_loss = sum(losses[-period:]) / period
    
    # Calculate RS and RSI
    if avg_loss == 0:
        rsi = 100 if avg_gain > 0 else 50
    else:
        rs = avg_gain / avg_loss
        rsi = 100 - (100 / (1 + rs))
    
    return round(rsi, 2)


# Usage: Calculate both fast and medium RSI
rsi_5 = calculate_rsi(closes, 5)      # Fast momentum (current day)
rsi_21 = calculate_rsi(closes, 21)    # Medium momentum (3-week)
```

**Integration with Golden Cross:**

```python
# After Golden Cross detection (gc_signal = +5, +2, or 0)

if golden_cross_signal == '+5':  # Strong Golden Cross
    if rsi_5 > 50 and rsi_21 > 50:
        rsi_boost = +2  # Both RSI periods in bull zone
    elif rsi_5 > 50 or rsi_21 > 50:
        rsi_boost = +1  # One RSI period bullish
    elif rsi_5 > 70 and rsi_21 > 70:
        rsi_boost = +1  # Overbought but trend intact
    elif rsi_5 < 30 and rsi_21 < 30:
        rsi_boost = -1  # Oversold, momentum weak
    else:
        rsi_boost = 0

elif golden_cross_signal == '+2':  # Partial Golden Cross
    if rsi_5 > 50:
        rsi_boost = +1  # Momentum confirms
    elif rsi_5 < 30:
        rsi_boost = -1  # Momentum divergence
    else:
        rsi_boost = 0

else:  # No Golden Cross (neutral or Death Cross)
    rsi_boost = 0  # RSI only applies when trend signal active

final_conviction += rsi_boost
```

**Interpretation:**
- RSI > 70: Overbought (potential pullback)
- RSI 50–70: Bullish momentum
- RSI 30–50: Bearish momentum
- RSI < 30: Oversold (potential bounce)

**Conviction Impact:**
- ✅ RSI aligns with trend: **+2 to +1 pts** (confidence boost)
- ⚠️ RSI diverges from trend: **-1 pt** (warning signal)

---

## SECTION 8: MACD CALCULATION (v3.0.3 NEW)

### Tier 3.5B: MACD (Moving Average Convergence Divergence)

**Purpose:** Confirm trend direction and detect momentum shifts (early warning of reversals)

**Data Required:** 250 closes (already have from OHLC)

**Calculation:**

```python
def calculate_macd(closes: List[float]) -> Dict[str, float]:
    """
    MACD Indicator:
    - MACD Line = 12-EMA - 26-EMA
    - Signal Line = 9-EMA of MACD Line
    - Histogram = MACD - Signal
    
    Args:
        closes: List of closing prices (at least 26 length, ideally 250)
    
    Returns:
        dict: {
            'macd_line': float,
            'signal_line': float,
            'histogram': float,
            'status': 'BULLISH_STRONG' | 'BULLISH' | 'BEARISH' | 'BEARISH_STRONG'
        }
    """
    if len(closes) < 26:
        return None  # Insufficient data
    
    # Calculate EMAs
    ema_12 = calculate_ema(closes, 12)
    ema_26 = calculate_ema(closes, 26)
    
    # MACD Line
    macd_line = ema_12 - ema_26
    
    # Signal Line (9-EMA of MACD)
    # For simplicity, use current MACD as signal on first call
    # In production, maintain rolling MACD history
    macd_history = [...]  # Previous MACD values (from history)
    signal_line = calculate_ema(macd_history + [macd_line], 9)
    
    # Histogram
    histogram = macd_line - signal_line
    
    # Determine status
    if macd_line > signal_line and histogram > 0:
        status = 'BULLISH_STRONG'
    elif macd_line > signal_line:
        status = 'BULLISH'
    elif macd_line < signal_line:
        status = 'BEARISH'
    else:
        status = 'BEARISH_STRONG'
    
    return {
        'macd_line': round(macd_line, 4),
        'signal_line': round(signal_line, 4),
        'histogram': round(histogram, 4),
        'status': status
    }


# Usage
macd_result = calculate_macd(closes)
macd_line = macd_result['macd_line']
signal_line = macd_result['signal_line']
histogram = macd_result['histogram']
```

**Integration with Golden Cross:**

```python
macd_result = calculate_macd(closes)

if golden_cross_signal == '+5':  # Strong Golden Cross
    if macd_result['status'] == 'BULLISH_STRONG':
        macd_boost = +3  # MACD confirms strongly
    elif macd_result['status'] == 'BULLISH':
        macd_boost = +2  # MACD confirms partially
    elif macd_result['status'] == 'BEARISH':
        macd_boost = -2  # MACD divergence warning
    else:  # BEARISH_STRONG
        macd_boost = -2  # Strong divergence warning

elif golden_cross_signal == '+2':  # Partial Golden Cross
    if macd_result['status'] in ['BULLISH_STRONG', 'BULLISH']:
        macd_boost = +1  # MACD confirms
    elif macd_result['status'] in ['BEARISH', 'BEARISH_STRONG']:
        macd_boost = -2  # MACD conflicts significantly
    else:
        macd_boost = 0

else:
    macd_boost = 0  # No trend signal = no MACD adjustment

final_conviction += macd_boost
```

**Interpretation:**
- MACD > Signal & Histogram > 0: Bullish momentum strengthening
- MACD > Signal & Histogram < 0: Bullish momentum weakening
- MACD < Signal & Histogram < 0: Bearish momentum strengthening
- MACD < Signal & Histogram > 0: Bearish momentum weakening

**Early Warning:** MACD crosses Signal line typically precede price crossovers by 1–3 days

**Conviction Impact:**
- ✅ MACD aligns with trend: **+3 to +1 pts** (strong confirmation)
- ⚠️ MACD diverges from trend: **-2 pts** (reversal warning)

---

## SECTION 9: BOLLINGER BANDS CALCULATION (v3.0.3 NEW)

### Tier 3.5C: Bollinger Bands

**Purpose:** Identify volatility extremes and optimal entry/exit zones

**Data Required:** 250 closes (already have from OHLC)

**Calculation:**

```python
def calculate_bollinger_bands(closes: List[float], period: int = 20, std_dev: float = 2.0) -> Dict:
    """
    Bollinger Bands:
    - Middle Band = 20-day SMA
    - Upper Band = Middle + (2 × std_dev)
    - Lower Band = Middle - (2 × std_dev)
    
    Args:
        closes: List of closing prices
        period: SMA period (standard: 20)
        std_dev: Number of standard deviations (standard: 2)
    
    Returns:
        dict: {
            'lower_band': float,
            'middle_band': float,
            'upper_band': float,
            'band_width': float,
            'position': 'UPPER' | 'MIDDLE' | 'LOWER' | 'ABOVE' | 'BELOW',
            'current_close': float
        }
    """
    if len(closes) < period:
        return None  # Insufficient data
    
    # Calculate middle band (20-day SMA)
    middle_band = sum(closes[-period:]) / period
    
    # Calculate standard deviation
    variance = sum((c - middle_band) ** 2 for c in closes[-period:]) / period
    std = variance ** 0.5
    
    # Calculate bands
    upper_band = middle_band + (std_dev * std)
    lower_band = middle_band - (std_dev * std)
    band_width = upper_band - lower_band
    
    # Determine position
    current_close = closes[-1]
    if current_close > upper_band:
        position = 'ABOVE'
    elif current_close < lower_band:
        position = 'BELOW'
    elif current_close > middle_band:
        position = 'UPPER'
    elif current_close < middle_band:
        position = 'LOWER'
    else:
        position = 'MIDDLE'
    
    return {
        'lower_band': round(lower_band, 2),
        'middle_band': round(middle_band, 2),
        'upper_band': round(upper_band, 2),
        'band_width': round(band_width, 2),
        'position': position,
        'current_close': round(current_close, 2)
    }


# Usage
bb_result = calculate_bollinger_bands(closes, 20, 2.0)
```

**Integration with Golden Cross:**

```python
bb_result = calculate_bollinger_bands(closes)

if golden_cross_signal == '+5':  # Strong Golden Cross
    if bb_result['position'] == 'BELOW':
        bb_boost = +2  # Ideal entry zone (lower band)
    elif bb_result['position'] == 'LOWER':
        bb_boost = +1  # Good entry zone
    elif bb_result['position'] == 'MIDDLE':
        bb_boost = +1  # Neutral zone
    elif bb_result['position'] == 'UPPER':
        bb_boost = 0   # Overbought
    elif bb_result['position'] == 'ABOVE':
        bb_boost = -1  # Extremely overbought, pullback likely
    else:
        bb_boost = 0

elif golden_cross_signal == '+2':
    if bb_result['position'] in ['BELOW', 'LOWER']:
        bb_boost = +1  # Good entry
    elif bb_result['position'] == 'ABOVE':
        bb_boost = -1  # Overbought warning
    else:
        bb_boost = 0

else:
    bb_boost = 0

final_conviction += bb_boost
```

**Interpretation:**
- Price below lower band: Oversold (potential bounce)
- Price at middle band: Fair value
- Price above upper band: Overbought (potential pullback)
- Wide bands: High volatility
- Narrow bands: Low volatility (quiet market)

**Risk Management:**
- Entry at lower band: Lower risk, higher probability
- Entry at upper band: Higher risk, overbought

**Conviction Impact:**
- ✅ Price at lower band: **+2 pts** (optimal entry)
- ⚠️ Price at upper band: **-1 pt** (overbought warning)

---

## SECTION 10: VOLUME CONFIRMATION (v3.0.3 NEW)

### Tier 3.5D: Volume Confirmation

**Purpose:** Validate that price moves are backed by institutional participation

**Data Required:** 20-day volume average + current volume (both in OHLC response)

**Calculation:**

```python
def calculate_volume_confirmation(volumes: List[float]) -> Dict[str, Any]:
    """
    Volume Confirmation:
    - Average Volume (20-day) = sum of last 20 volumes / 20
    - Current Volume = latest volume
    - Ratio = Current / 20-day Average
    
    Args:
        volumes: List of volumes (one per OHLC bar)
    
    Returns:
        dict: {
            'avg_volume': float,
            'current_volume': float,
            'volume_ratio': float,
            'strength': 'HIGH' | 'NORMAL' | 'LOW'
        }
    """
    if len(volumes) < 20:
        return None  # Insufficient data
    
    # Calculate 20-day average volume
    avg_volume = sum(volumes[-20:]) / 20
    current_volume = volumes[-1]
    
    # Calculate ratio
    if avg_volume == 0:
        volume_ratio = 1.0
    else:
        volume_ratio = current_volume / avg_volume
    
    # Determine strength
    if volume_ratio > 1.5:
        strength = 'HIGH'  # 50%+ above average
    elif volume_ratio > 1.0:
        strength = 'NORMAL'  # Above average
    else:
        strength = 'LOW'  # Below average
    
    return {
        'avg_volume': round(avg_volume, 0),
        'current_volume': round(current_volume, 0),
        'volume_ratio': round(volume_ratio, 2),
        'strength': strength
    }


# Usage
vol_result = calculate_volume_confirmation(volumes)
```

**Integration with Golden Cross:**

```python
vol_result = calculate_volume_confirmation(volumes)

if golden_cross_signal == '+5':  # Strong Golden Cross
    if vol_result['strength'] == 'HIGH':
        vol_boost = +1  # Institutional backing confirms
    elif vol_result['strength'] == 'NORMAL':
        vol_boost = 0   # Adequate volume
    else:  # LOW
        vol_boost = -1  # Weak signal, retail only

elif golden_cross_signal == '+2':  # Partial Golden Cross
    if vol_result['strength'] == 'HIGH':
        vol_boost = +1  # Volume confirms emerging signal
    elif vol_result['strength'] == 'LOW':
        vol_boost = -1  # Weak signal without volume
    else:
        vol_boost = 0

else:
    vol_boost = 0

final_conviction += vol_boost
```

**Interpretation:**
- Volume Ratio > 1.5: High institutional interest (strong)
- Volume Ratio 1.0–1.5: Normal participation
- Volume Ratio < 1.0: Below average (weak)

**Rule:** Trends backed by high volume are more likely to sustain

**Conviction Impact:**
- ✅ High volume: **+1 pt** (confirms signal)
- ⚠️ Low volume: **-1 pt** (weak signal)

---

## UNIFIED CONVICTION ADJUSTMENT FORMULA (v3.0.3)

**Final Conviction Score:**

```
Base Conviction (from fundamentals) = 50–65
+ Golden Cross Boost = +5 or +2 or 0
+ RSI Alignment = +2 to -1
+ MACD Alignment = +3 to -2
+ Bollinger Position = +2 to -1
+ Volume Confirmation = +1 to -1
────────────────────────────────
= Final Conviction Score (capped at tier max 70–75 for HIGH)
```

**Example:**

```python
base_conviction = 65           # Fundamental analysis score
golden_cross = +5              # Strong Golden Cross
rsi_boost = +1                 # RSI > 50
macd_boost = +2                # MACD bullish
bb_boost = +1                  # At lower band
vol_boost = +1                 # High volume

final_score = base_conviction + golden_cross + rsi_boost + macd_boost + bb_boost + vol_boost
            = 65 + 5 + 1 + 2 + 1 + 1
            = 75  (capped at HIGH tier max)

Result: Conviction 75 (HIGH tier, strong BUY)
```

---

## API COST ANALYSIS (v3.0.3)

**Single API Call (250-day OHLC) Enables All Four Indicators:**

| Indicator | API Calls | Cost | Calculation Time |
|:---|:---|:---|:---|
| Golden Cross | 0 | Free (OHLC) | < 10ms |
| Death Cross | 0 | Free (OHLC) | < 10ms |
| RSI | 0 | Free (closes) | < 5ms |
| MACD | 0 | Free (closes) | < 5ms |
| Bollinger Bands | 0 | Free (closes + std dev) | < 5ms |
| Volume Confirmation | 0 | Free (volumes) | < 5ms |
| **TOTAL** | **1** | **Single OHLC call** | **< 50ms** |

**Efficiency:** All six indicators (GC, DC, RSI, MACD, BB, Volume) cost exactly 1 API call

---

## INTEGRATION WITH STOCK-ANALYST v8.15.2

**Existing Workflow (v8.15.2):**
```
Tier 3.4A: Golden Cross (+5/+2/0)
Tier 3.4B: Death Cross (-5/-2/0)
```

**Enhanced Workflow (v8.15.3):**
```
Tier 3.4A: Golden Cross (+5/+2/0)
Tier 3.4B: Death Cross (-5/-2/0)
├─ Tier 3.5A: RSI Alignment (+2 to -1)
├─ Tier 3.5B: MACD Alignment (+3 to -2)
├─ Tier 3.5C: Bollinger Position (+2 to -1)
└─ Tier 3.5D: Volume Confirmation (+1 to -1)
```

**No Changes Required:**
- ✅ Tier 3.4 logic unchanged
- ✅ API calls unchanged (reuse OHLC)
- ✅ Backward compatible (v8.15.3 with v8.15.2 data)

---

## PRODUCTION CHECKLIST (v3.0.3)

- [ ] RSI calculation tested on 5, 14, 21 periods
- [ ] MACD line, signal line, histogram calculation validated
- [ ] Bollinger Bands: SMA + std dev calculation verified
- [ ] Volume confirmation ratio calculation checked
- [ ] All four indicators integrated into Tier 3.5
- [ ] Conviction adjustment matrix applied correctly
- [ ] No additional API calls required (all from OHLC)
- [ ] Processing time < 100ms per stock
- [ ] Backtest on 16 historical picks shows improvement
- [ ] Documentation updated

---

## VERSION HISTORY (API-Reference)

| Version | Date | Status | New |
|---------|------|--------|-----|
| 3.0.1 | Dec 23 | Production | Golden Cross |
| 3.0.2 | Dec 23 | Production | Death Cross |
| 3.0.3 | Dec 24 | Planned | RSI, MACD, BB, Volume |

---

**Update Type:** Additive (no breaking changes)  
**Integration:** Stock-Analyst v8.15.3+  
**Status:** Ready for Q1 2026 Implementation

