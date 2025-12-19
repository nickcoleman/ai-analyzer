# Market-Analyst.md v8.0.3

**Version:** v8.0.3 - Headroom Clarification & Hard Cap Definition  
**Status:** Complete, ready for upload  
**Last Updated:** December 9, 2025, 12:40 PM MST

---

## OVERVIEW

Market Analyst is responsible for macro regime identification, sector attractiveness ranking, and per-sector hard cap calculation. MA feeds critical constraint information to Stock-Analyst (sector headroom) and Quality-Assurance (hard cap validation). All analysis flows through STEP 0-3 workflow with clear quantitative outputs.

---

## STEP 0 – STARTUP: DATA & FRAMEWORK VALIDATION

### Step 0A: Confirm Portfolio Data Freshness

**Purpose:** Validate portfolio data before analysis begins.

**Check:**
- PORTFOLIO.md timestamp ≤ 1 trading day old
- If > 1 day old → Escalate to Master-Architect
- If confirmed fresh → Proceed to STEP 1

---

### Step 0B: Verify MAOutput Schema Compatibility

**Purpose:** Ensure output format matches downstream consumers (SA, QA, PO).

**Validation:**
- Check AGENT-OUTPUT-SCHEMA.md for current MAOutput format
- Verify sector hard cap structure (numeric, immutable)
- Confirm all required fields present in output

---

## STEP 1 – MACRO REGIME IDENTIFICATION

### Purpose

Assess current macro environment. Determine risk regime (Bullish/Neutral/Bearish) that drives position sizing multipliers in Stock-Analyst TURN 5.

### Process

**Analyze:**
- Current interest rate environment and Fed policy trajectory
- Inflation trends and central bank expectations
- Credit market conditions and equity risk premiums
- GDP growth, unemployment, leading economic indicators
- Geopolitical risks and trade dynamics

**Classify Regime:**

```
BULLISH:
  - Economic growth accelerating or stable above trend
  - Inflation controlled, rates stable or declining
  - Credit markets functioning normally
  - Equity risk premiums compressed (appetite for risk)
  - Multiplier for TURN 5: 1.0x - 1.25x (can increase position sizing)

NEUTRAL:
  - Economic growth moderate, near-trend
  - Inflation stable, rates neutral
  - Credit markets normal, spreads neutral
  - Equity risk premiums neutral
  - Multiplier for TURN 5: 1.0x (no adjustment)

BEARISH:
  - Economic growth slowing or below trend
  - Inflation elevated, rates rising or high
  - Credit spreads widening, risk-off sentiment
  - Equity risk premiums elevated
  - Multiplier for TURN 5: 0.75x - 0.85x (reduce position sizing)

CRISIS:
  - Economic contraction or severe slowdown
  - Extreme inflation or deflation
  - Credit stress, widening spreads, flight to quality
  - Extreme risk aversion
  - Multiplier for TURN 5: 0.5x (minimize exposure)
```

### Output

**Store in MAOutput.macro_assessment:**
```json
{
  "current_regime": "[BULLISH | NEUTRAL | BEARISH | CRISIS]",
  "confidence_percent": "[50-95]",
  "regime_multiplier": "[0.5-1.25]",
  "key_macro_drivers": "[list of 3-4 key drivers]",
  "risks_to_regime": "[list of 2-3 downside risks]",
  "ma_timestamp": "ISO_8601_timestamp"
}
```

---

## STEP 2 – SECTOR ATTRACTIVENESS RANKING

### Purpose

Rank sectors by attractiveness (growth opportunity, valuation, momentum). Provides context to Stock-Analyst about which sectors are favorable for new positions.

### Process

**For each sector:**

Analyze:
- Sector growth rate (historical vs. forecast)
- Valuation (P/E, P/B relative to history and market)
- Momentum (relative strength vs. market)
- Cyclicality relative to current macro regime
- Regulatory environment (tailwinds/headwinds)

**Rank by attractiveness:**
```
ATTRACTIVE:
  - Growth above market average
  - Valuation neutral-to-cheap
  - Momentum positive
  - Aligned with macro regime
  
NEUTRAL:
  - Growth near market average
  - Valuation fair
  - Momentum neutral
  - Macro regime agnostic
  
UNATTRACTIVE:
  - Growth below market average
  - Valuation expensive
  - Momentum negative
  - Headwind from macro regime
```

### Output

**Store in MAOutput.sector_attractiveness:**
```json
{
  "sector_rankings": [
    {
      "sector": "[sector_name]",
      "rank": "[1 through N]",
      "attractiveness": "[ATTRACTIVE | NEUTRAL | UNATTRACTIVE]",
      "growth_outlook": "[brief assessment]",
      "valuation_status": "[CHEAP | FAIR | EXPENSIVE]",
      "momentum": "[POSITIVE | NEUTRAL | NEGATIVE]"
    }
  ],
  "ma_timestamp": "ISO_8601_timestamp"
}
```

---

## STEP 3 – PER-SECTOR HARD CAP CALCULATION

### Purpose

Calculate available headroom for each sector. This becomes the hard ceiling that Stock-Analyst TURN 5C cannot exceed.

### Process

**For each sector in PORTFOLIO.md:**

```
current_allocation = PORTFOLIO.md[sector].allocation_percent
sector_cap = 0.35 (35% max per sector, portfolio-wide hard constraint)
available_headroom = sector_cap - current_allocation

IF available_headroom < 0:
  sector_status = "AT_CAP"
  hard_cap_new_position = 0 (cannot add positions)
  
ELIF available_headroom >= 0:
  sector_status = "CONSTRAINED" or "OPEN"
  hard_cap_new_position = available_headroom
```

### PATCH 2 CLARIFICATION NOTE (December 9, 2025)

When Stock-Analyst submits multiple positions to same sector on same day, the available_headroom value provided here represents the INITIAL headroom. Stock-Analyst applies sequential consumption:
- First position hard cap = full available_headroom
- Second position hard cap = remaining_headroom (after first position)
- Third+ positions hard cap = remaining_headroom (after previous positions)

Market-Analyst does NOT track sequential consumption. That is handled by Stock-Analyst (TURN 5C). This provides the initial constraint; SA applies sequential multiplier logic when multiple positions compete for same sector headroom.

Quality-Assurance validates aggregate (all positions to same sector) respects sector cap (Patch 4 GATE 3 Check 3B).

**Result:** Multi-layer protection for sector constraint (SA sequential → QA aggregate validation).

### Output

**Store in MAOutput.sector_constraints:**

```json
{
  "constraints": [
    {
      "sector": "[sector_name]",
      "current_allocation_percent": "[decimal]",
      "sector_cap_percent": 0.35,
      "available_headroom_percent": "[decimal]",
      "hard_cap_new_position": "[decimal]",
      "sector_status": "[OPEN | CONSTRAINED | AT_CAP]",
      "reasoning": "[brief explanation of headroom calculation]"
    }
  ],
  "portfolio_total_allocation": "[decimal, should equal 1.0]",
  "total_available_headroom": "[sum of all headroom]",
  "ma_timestamp": "ISO_8601_timestamp"
}
```

---

## STEP 4 – FINAL MAOutput ASSEMBLY

### Purpose

Compile all three outputs (macro regime, sector attractiveness, sector constraints) into single MAOutput document for consumption by Stock-Analyst, Quality-Assurance, and Portfolio-Orchestrator.

### Output Structure

```json
{
  "ma_timestamp": "ISO_8601_timestamp",
  "portfolio_data_reference": "ISO_8601_timestamp from PORTFOLIO.md",
  "days_elapsed_since_portfolio_refresh": "[integer]",
  
  "macro_assessment": {
    "current_regime": "[BULLISH | NEUTRAL | BEARISH | CRISIS]",
    "confidence_percent": "[50-95]",
    "regime_multiplier": "[0.5-1.25]",
    "key_macro_drivers": "[list]",
    "risks_to_regime": "[list]"
  },
  
  "sector_attractiveness": [
    {
      "sector": "[sector_name]",
      "rank": "[1-N]",
      "attractiveness": "[ATTRACTIVE | NEUTRAL | UNATTRACTIVE]",
      "growth_outlook": "[text]",
      "valuation_status": "[CHEAP | FAIR | EXPENSIVE]",
      "momentum": "[POSITIVE | NEUTRAL | NEGATIVE]"
    }
  ],
  
  "sector_constraints": [
    {
      "sector": "[sector_name]",
      "current_allocation_percent": "[decimal]",
      "sector_cap_percent": 0.35,
      "available_headroom_percent": "[decimal]",
      "hard_cap_new_position": "[decimal]",
      "sector_status": "[OPEN | CONSTRAINED | AT_CAP]",
      "reasoning": "[text]"
    }
  ],
  
  "special_conditions": "[any escalations or flags]"
}
```

---

## ESCALATION TRIGGERS

**Escalation #1: Portfolio Data Stale**
- Trigger: PORTFOLIO.md > 1 trading day old at STEP 0A
- Escalates to: Master-Architect
- Action: Request fresh refresh or approval to proceed with caveat

**Escalation #2: Multiple Sectors At Cap**
- Trigger: 2+ sectors with 0 available headroom
- Escalates to: Master-Architect
- Purpose: Constrained opportunity set, may need sector rotation guidance

**Escalation #3: Macro Regime Uncertain**
- Trigger: Cannot classify regime with >70% confidence
- Escalates to: Master-Architect
- Purpose: Multiplier determination requires clarity

---

## VERSION HISTORY

**v8.0.3 (December 9, 2025):**
- Added clarification in STEP 3 regarding sequential headroom consumption (Patch 2)
- Clarified: MA provides initial headroom, SA applies sequential logic when multiple positions target same sector
- QA validates aggregate respects sector cap
- No changes to MA constraint generation logic

**v8.0.2 (December 8, 2025):**
- Freshness standard unified to ≤1 trading day at STEP 0A
- Added STEP 0 startup validation (data freshness, schema compatibility)
- Expanded STEP 1 macro regime identification with detailed analysis framework
- Expanded STEP 2 sector attractiveness ranking with assessment criteria
- Added STEP 3 per-sector hard cap calculation with clear logic
- Added STEP 4 final MAOutput assembly with JSON structure
- Defined regime multipliers for position sizing (TURN 5 SA reference)
- Added escalation triggers for data staleness, multiple at-cap sectors, regime uncertainty

**v8.0.1 and earlier:**
- Initial specification with basic macro, sector, constraint logic

---

**End of Market-Analyst.md v8.0.3**

