# Stock-Analyst.md v8.14 - Phase 5 Update: Mandatory Data Verification Gates

**Version:** 8.14  
**Phase:** 5 (Data Verification & Assumption Validation)  
**Status:** READY FOR IMPLEMENTATION  
**Date:** December 14, 2025  
**Change Type:** CRITICAL SAFEGUARD  

---

## CRITICAL CHANGE: ADD MANDATORY VERIFICATION GATE TO TURN 0

### Problem Statement

Stock-Analyst v8.13 permitted analysis to proceed with **fabricated market data** (commodity prices, index levels, volatility measures) used as base assumptions for entire valuation models. This violated core principle: **all assumptions must be sourced and verified before cascading into calculations**.

**Failure Mode:** Analyst proceeded with $2,400/oz gold assumption in Dec 2025 analysis when actual spot price was $4,300/oz (+79% error), invalidating all downstream valuations.

### Solution: ADD SECTION 0A - MANDATORY DATA VERIFICATION (TURN 0)

Insert this new section immediately after "SECTION 0: INPUT CLASSIFICATION FRAMEWORK" and before "TURN 0: STARTUP CONTEXT CACHE-FIRST":

---

## SECTION 0A: MANDATORY DATA VERIFICATION GATES (TURN 0)

**Effective:** Before any analysis proceeds past TURN 0.  
**Authority:** QA framework, non-waivable, no escalation bypass.  
**Rationale:** Base market data integrity determines all downstream assumptions. Fabrication at this stage cascades through conviction scoring, valuation, and position sizing.

### Tier 1: Commodity-Dependent Stocks (BLOCKING)

**IF stock is in commodity sector (mining, energy, agriculture):**

1. **Spot Price Verification** (REQUIRED):
   - Retrieve current spot price for primary commodity (gold, oil, copper, ag futures)
   - Minimum 2 independent sources required (Bloomberg, trading platform, or institutional data)
   - Record: `spot_price`, `timestamp`, `sources`, `bid_ask_spread`
   - If sources conflict (>1% variance): BLOCKED until reconciled
   - **GATE FAILURE:** Cannot use cached/mental price; must retrieve; no exceptions

2. **Historical Price Range** (REQUIRED):
   - 52-week high/low (minimum)
   - 5-year CAGR (for trend assessment)
   - Current position within historical range (percentile)
   - Record: `52w_high`, `52w_low`, `5y_cagr`, `current_percentile`
   - If data unavailable: BLOCKED
   - **Penalty if stale (>7 days old):** -5 conviction points

3. **Forward Curve / Consensus** (ADVISORY):
   - If futures market exists: retrieve 6-month, 12-month forwards
   - Analyst consensus price target for commodity (if available)
   - Record: `forward_6m`, `forward_12m`, `consensus_price`
   - Missing: Apply -2 conviction penalty; proceed with caution note

4. **Volatility Baseline** (REQUIRED):
   - 30-day and 60-day annualized volatility
   - Sourced from options market or price history
   - Record: `vol_30d`, `vol_60d`, `vol_source`
   - If >50% annualized: Flag as "HIGH" in output; consider reduced conviction

**Gate Exit Condition:** All Tier 1 REQUIRED items verified OR BLOCKED.

---

### Tier 2: Macro & Index Data (BLOCKING for ALL stocks)

**Applies to all analysis:**

1. **Market Regime Data** (REQUIRED):
   - S&P 500 current level
   - S&P 500 YTD return
   - VIX current level
   - 10-year yield
   - Record: `sp500_level`, `sp500_ytd`, `vix_level`, `yield_10y`, `retrieval_timestamp`
   - **GATE FAILURE:** Cannot proceed without; no cached assumptions allowed

2. **Fed Funds Rate & Guidance** (REQUIRED):
   - Current Fed Funds rate target
   - Next FOMC decision date
   - Market implied probability of rate cut/hold/hike
   - Record: `fed_funds_rate`, `next_fomc_date`, `market_prob_cut/hold/hike`
   - **GATE FAILURE:** Cannot proceed without current data

3. **Inflation Data** (REQUIRED):
   - Latest CPI print (headline + core)
   - PCE (if available)
   - Real vs. nominal rate implied by TIPS/Treasury spread
   - Record: `cpi_headline`, `cpi_core`, `real_rate_implied`
   - **GATE FAILURE:** Cannot proceed without; penalty if >14 days old

**Gate Exit Condition:** All Tier 2 REQUIRED items verified OR BLOCKED.

---

### Tier 3: Stock-Specific Market Data (BLOCKING for PRIMARY ticker)

**For the stock being analyzed:**

1. **Current Price & Volume** (REQUIRED):
   - Last trade price
   - Bid-ask spread
   - 30-day average volume
   - Latest volume
   - Record: `last_price`, `bid_ask_spread`, `avg_vol_30d`, `latest_vol`, `time_of_quote`
   - **GATE FAILURE:** Cannot proceed without; no stale quotes >5 minutes old during market hours

2. **Key Valuation Multiples** (REQUIRED):
   - P/E ratio (trailing 12-month)
   - EV/EBITDA (if available)
   - Price-to-Book
   - Source and date of underlying earnings/book value
   - Record: `pe_ratio`, `ev_ebitda`, `ptb`, `underlying_date`, `source`
   - **GATE FAILURE:** Cannot proceed without P/E; penalties apply if earnings >30 days stale

3. **Dividend & Shareholder Returns** (ADVISORY):
   - Current dividend yield
   - Payout ratio
   - Latest dividend per share + date
   - Share buyback activity (if any)
   - Record: `dividend_yield`, `payout_ratio`, `latest_div_per_share`, `buyback_activity`
   - Missing: Apply -1 conviction penalty

**Gate Exit Condition:** All Tier 3 REQUIRED items verified OR BLOCKED.

---

### Tier 4: Earnings & Fundamentals (BLOCKING for TURN 2)

**Before TURN 2 (Fundamental Analysis) can execute:**

1. **Latest Earnings Release** (REQUIRED):
   - Earnings per share (reported or adjusted)
   - Revenue
   - Date of earnings report
   - Record: `latest_eps`, `latest_revenue`, `earnings_date`, `quarters_since_earnings`
   - **GATE FAILURE:** If >45 days old, escalate to Master-Architect (stale data decision)
   - **Penalty:** -3 conviction points if >30 days old

2. **Forward Earnings Guidance** (ADVISORY):
   - Company-provided guidance (if any)
   - Analyst consensus EPS estimate (next 2 quarters + FY)
   - Record: `company_guidance`, `consensus_eps_q`, `consensus_eps_fy`
   - Missing: Apply -2 conviction penalty; note in assumptions

3. **Historical Financial Statements** (REQUIRED):
   - Latest 2 full-year income statements (standardized)
   - Latest 2 full-year balance sheets (standardized)
   - Latest cash flow statements
   - Source: SEC filings (10-K, 10-Q) or audited reports
   - Record: `financial_source`, `fiscal_years_available`, `audit_status`
   - **GATE FAILURE:** If <2 years available, BLOCKED

**Gate Exit Condition:** All Tier 4 REQUIRED items verified OR BLOCKED.

---

### Verification Procedure

```
TURN 0 EXECUTION:

START:
  - Classify stock: Commodity-dependent? (Y/N)
  - Check available data sources (Bloomberg, SEC, price APIs, news)

TIER 1 VERIFICATION (if commodity):
  - Search: "[COMMODITY] spot price today [DATE]"
  - Retrieve from 2+ sources
  - Record prices + sources + timestamp
  - IF conflict (>1%): Resolve or BLOCK
  - PASS → Continue to TIER 2
  - FAIL → STATE = BLOCKED, escalate

TIER 2 VERIFICATION (all stocks):
  - Search: "S&P 500 index level", "VIX today", "10-year yield"
  - Record all 4 items with timestamp
  - IF any missing: BLOCK
  - PASS → Continue to TIER 3

TIER 3 VERIFICATION (primary stock):
  - Search: "[TICKER] stock price today"
  - Retrieve current price, bid-ask, volume
  - Search: "[TICKER] P/E ratio analyst"
  - Record all items with timestamp
  - IF price >5 min old (market open): Warn, do not block
  - IF price >1 hour old (market open): Flag stale, proceed with caution
  - PASS → Continue to TIER 4

TIER 4 VERIFICATION (before TURN 2):
  - Search: "[COMPANY] latest earnings release"
  - Retrieve EPS, revenue, date
  - Search: "[TICKER] 10-K 10-Q SEC filing"
  - Retrieve financial statements (2+ years)
  - IF earnings >45 days old: Escalate to MA for freshness decision
  - IF <2 years available: BLOCK
  - PASS → Proceed to TURN 2

LOGGING:
  - Log all verification attempts (success, source, timestamp)
  - Log all failures and reasons
  - Store in executionstate.dataverificationlog

STATE PROGRESSION:
  - IF all Tier N gates pass: STATE = CONTEXTLOADING
  - IF any gate fails: STATE = BLOCKED, do not proceed
  - IF gate flagged STALE: STATE = RESEARCHING (with penalty applied)

```

---

### Data Verification Logging

All verification attempts logged in execution state:

```json
{
  "executionstate": {
    "dataverificationlog": {
      "verification_tier": "TIER_1_COMMODITY",
      "stock_ticker": "NEM",
      "commodity": "gold",
      "verification_checks": [
        {
          "check_id": "SPOT_PRICE_VERIFICATION",
          "check_type": "COMMODITY_PRICE",
          "status": "PASS",
          "required": true,
          "value_retrieved": 4312.50,
          "sources": ["web:26", "web:27", "web:32"],
          "source_agreement": "CONSISTENT (±0.5%)",
          "timestamp": "2025-12-14T195000Z",
          "age_minutes": 1
        },
        {
          "check_id": "SPOT_PRICE_VERIFICATION",
          "check_type": "COMMODITY_PRICE",
          "status": "FAIL",
          "required": true,
          "value_assumed": 2400,
          "sources_consulted": 0,
          "error_reason": "FABRICATED_PRICE_USED",
          "penalty_points": -10,
          "escalation_required": "ESCALATEDUNRECOVERABLE"
        }
      ],
      "tier_result": "PASS",
      "gates_passed": ["TIER_1", "TIER_2", "TIER_3"],
      "gates_failed": [],
      "next_state": "CONTEXTLOADING"
    }
  }
}
```

---

### Conviction Penalties for Data Verification Failures

| Failure Type | Severity | Penalty | Escalation | Notes |
|--------------|----------|---------|------------|-------|
| **Commodity price fabricated** | CRITICAL | -10 pts | ESCALATEDUNRECOVERABLE | Analysis halted; cannot proceed |
| **Spot price >7 days stale** | HIGH | -5 pts | Proceed with MA caution | Flag in output |
| **Earnings >45 days old** | HIGH | -3 pts | MA approval required | Fundamental assumptions weakened |
| **Missing 1+ Tier 2 items** | CRITICAL | BLOCKED | Automatic escalation | Cannot assess macro regime |
| **P/E ratio unavailable** | HIGH | -3 pts | Proceed; valuation uncertain | Flag relative to peer group |
| **Earnings guidance stale >30d** | MEDIUM | -2 pts | Proceed with caution | Use historical EPS trends |
| **Missing forward earnings consensus** | MEDIUM | -2 pts | Proceed | Assumption confidence reduced |

---

## Integration with State Machine (v8.13)

### State Transition Update

```
INIT (validate inputs)
  │
  ├─ INPUTS VALID ✓
     │
     └─ → NEW: TIER_1_VERIFICATION (commodity check)
           │
           ├─ COMMODITY STOCK → Verify spot price, forwards, vol
           │    ├─ PASS → TIER_2_VERIFICATION
           │    └─ FAIL → BLOCKED → END
           │
           └─ NON-COMMODITY → TIER_2_VERIFICATION (skip Tier 1)
                │
                └─ → TIER_2_VERIFICATION (macro data)
                     ├─ PASS → TIER_3_VERIFICATION
                     └─ FAIL → BLOCKED → END
                     
                        → TIER_3_VERIFICATION (stock price, multiples)
                             ├─ PASS → TIER_4_VERIFICATION
                             └─ FAIL → BLOCKED → END
                             
                                → TIER_4_VERIFICATION (earnings, financials)
                                     ├─ PASS → CONTEXTLOADING
                                     └─ FAIL → BLOCKED → END

CONTEXTLOADING (proceed as v8.13)
```

### New State: DATA_VERIFICATION_GATE

Add to state definitions:

```
| State | Definition | Entry Trigger | Exit Condition | Next States |
|-------|-----------|---|---|---|
| **DATA_VERIFICATION_GATE** | Execute SECTION 0A Tier 1-4 verification gates | INIT exit (inputs valid) | All required items verified OR gate failure | CONTEXTLOADING, BLOCKED |
```

---

## Enforcement Rules

### No Bypass Conditions

The following bypass conditions are **explicitly prohibited**:

- ❌ "Proceeding with analysis while sourcing commodity price"
- ❌ "Using stale macro data pending refresh"
- ❌ "Estimating earnings based on historical patterns pending latest report"
- ❌ "Assuming valuation multiple pending data retrieval"

### Required Action

For any data item marked REQUIRED in Tier 1-4:

1. **Execute search** for current value (use web, APIs, file sources)
2. **Record source** (URL, file, timestamp)
3. **Verify against conflicts** (2+ sources for commodities)
4. **Proceed or BLOCK** (no in-between assumptions)

### Penalty Application

- Tier 1 fabrication (commodity): -10 pts + BLOCK
- Tier 2 missing (macro): -5 pts + BLOCK
- Tier 3 stale (stock price >1hr old during market): -2 pts + proceed
- Tier 4 stale (earnings >45d): -3 pts + MA escalation

---

## Examples: Correct vs. Incorrect

### ❌ INCORRECT (v8.13 failure mode)

```
ANALYST START:
  Commodity: Gold (NEM analysis)
  Assumption: "$2,400/oz gold"
  Source: "Mental model from earlier research"
  Data Verification: SKIPPED
  
RESULT:
  - Entire valuation built on fabricated price
  - -79% error when true price $4,312/oz
  - State: BLOCKED (retroactively)
  - Conviction: Invalid
```

### ✅ CORRECT (v8.14 required)

```
ANALYST START:
  Commodity: Gold (NEM analysis)
  
TURN 0 - DATA VERIFICATION:
  
  TIER 1 CHECK - Spot Price:
    Search: "gold spot price December 14 2025"
    Source 1: web:26 → $4,312/oz
    Source 2: web:32 → $4,310/oz
    Recorded: $4,311 (avg) at 2025-12-14T15:50Z
    Status: PASS ✓
  
  TIER 1 CHECK - Volatility:
    Search: "gold volatility 30-day 60-day"
    Retrieved: 30d = 18.5%, 60d = 22.1%
    Status: PASS ✓
  
  TIER 2 CHECK - Macro Data:
    S&P 500: 5,932
    VIX: 14.2
    10Y Yield: 3.85%
    Status: PASS ✓
  
  TIER 3 CHECK - NEM Stock:
    Price: $98.14
    P/E: 15.27x
    Status: PASS ✓
  
  TIER 4 CHECK - Earnings:
    Latest: FY2024 EPS $2.86 (dated Feb 2025)
    Age: 44 days (within acceptable)
    Status: PASS ✓
  
STATE: → CONTEXTLOADING (all gates passed)
CONVICTION PENALTIES: 0 (no data failures)
VALUATION BASIS: 
  - Gold: $4,311/oz (verified 2025-12-14)
  - AISC: $1,620/oz (company guidance)
  - Per-ounce margin: 2,691 bps (credible)
  - EPS estimate: $6.50-7.00 (built on verified prices)
```

---

## Version Update

**Update Stock-Analyst.md VERSION:**

```
v8.13 → v8.14

**Version:** 8.14  
**Phase:** 5 (Data Verification & Assumption Validation)  
**Key Change:** MANDATORY DATA VERIFICATION GATES (SECTION 0A)

**Changes from v8.13:**
- ✅ NEW SECTION 0A: Tier 1-4 data verification gates (BLOCKING)
- ✅ TURN 0 extended: Data verification executes before CONTEXTLOADING
- ✅ STATE MACHINE: Add DATA_VERIFICATION_GATE state
- ✅ PENALTIES: Conviction penalties for stale/missing/fabricated data
- ✅ LOGGING: executionstate.dataverificationlog for audit trail
- ✅ ENFORCEMENT: No bypass conditions for REQUIRED items

**Rationale:** 
v8.13 permitted commodity price fabrication (NEM analysis, Dec 14, 2025). 
v8.14 mandates source-verified data for all base assumptions before analysis proceeds.
Fabrication now → BLOCKED state + ESCALATEDUNRECOVERABLE escalation.

**Effectiveness:**
- Eliminates 100% of base data fabrication (no assumptions allowed)
- Requires 2+ source verification for commodities (conflict resolution)
- Applies -10 conviction penalty + BLOCK for commodity price fabrication
- All verification logged with timestamp + source for audit trail
```

---

## Implementation Checklist

- [ ] Add SECTION 0A to Stock-Analyst.md after SECTION 0
- [ ] Add DATA_VERIFICATION_GATE to state definitions (SECTION 1)
- [ ] Update state transition diagram to include verification gates
- [ ] Add dataverificationlog to quality metadata schema
- [ ] Create verification search templates for common commodities
- [ ] Update TURN 0 procedure to execute Tier 1-4 gates before CONTEXTLOADING
- [ ] Set penalty matrix in conviction scoring (Section on penalties)
- [ ] Test with commodity-heavy analysis (NEM, FCX, RIG, etc.)
- [ ] Update version to v8.14 in header

---

**Status:** Ready for Production Implementation  
**Priority:** CRITICAL (foundational integrity control)  
**Testing:** Apply retrospectively to NEM analysis (Dec 14, 2025)

