# STARTUP-REQUIREMENTS.v2.1.md

**Version:** 2.1 (Guardrail D Data Freshness Validation - Step 1A New)  
**Date:** December 1, 2025  
**Status:** Production Ready - Data Freshness Validation as First Step  
**Purpose:** Define all requirements for Stock Analysis Methodology v1.4.1 execution with Guardrail D data validation

---

## NEW STEP 1A: DATA FRESHNESS VALIDATION [INSERTED AT START - v2.1]

**Purpose:** Verify that framework can access current market data before proceeding with analysis

**When to Execute:** Every analysis startup (daily synthesis, ad-hoc stock analysis, etc.)

**Duration:** 2-3 minutes

---

### **Step 1A.1: Detect Current Operating Context**

```
ACTION: Determine current market operating context

METHOD:
  Current Time: Get current UTC time, convert to Eastern Time
  Holiday Check: Confirm date not in market holiday calendar
  Day of Week: Confirm day is trading day (M-F) or non-trading (Sat-Sun)
  Time of Day: Classify into 5 operating contexts:
    - PRE_MARKET (9:00-9:30 AM ET)
    - MARKET_OPEN (9:30 AM-4:00 PM ET)
    - AFTER_HOURS (4:00 PM-9:30 AM ET)
    - WEEKEND (Sat-Sun)
    - HOLIDAY (market closed)

EXAMPLE:
  Current: Monday Dec 1, 2025, 2:30 PM ET
  Holiday Check: ✅ No (regular trading day)
  Day of Week: ✅ Yes (Monday)
  Time of Day: ✅ MARKET_OPEN (2:30 PM is between 9:30-4 PM)
  
  Result: MARKET_OPEN context

CHECKPOINTS:
  ☐ Current time determined
  ☐ Holiday calendar loaded
  ☐ Day of week verified
  ☐ Operating context classified
```

---

### **Step 1A.2: Determine Expected Data Priorities for Context**

```
ACTION: Identify which data freshness priorities should be available

MARKET_OPEN (9:30 AM - 4:00 PM ET):
  Expected: Priority 1 & 2 data available (real-time ≤60 min)
  Freshness Requirement: Data age ≤60 minutes
  Confidence Level: FULL (100%) expected
  
  CHECK:
    ☐ Real-time market feeds responding
    ☐ Data age ≤30 min available (Priority 1)
    ☐ Recent data ≤60 min available (Priority 2)

AFTER_HOURS (4:00 PM - 9:30 AM ET):
  Expected: Priority 1 data available (previous day close)
  Freshness Requirement: Data age ≤24 hours (previous close)
  Confidence Level: HIGH (90%) expected
  
  CHECK:
    ☐ Previous trading day close data available
    ☐ Data age ≤24 hours since market close
    ☐ Pre-market data available if >12 hours past close

WEEKEND / HOLIDAY:
  Expected: Priority 1 data available (last trading day close)
  Freshness Requirement: Data age ≤3 trading days
  Confidence Level: MEDIUM-HIGH (85%) expected
  
  CHECK:
    ☐ Last trading day close data available
    ☐ Data age ≤3 trading days
    ☐ Data from adjacent trading day (Friday if weekend, etc.)

PRE-MARKET (9:00 AM - 9:30 AM ET):
  Expected: Priority 1 or 2 data available (pre-market or previous close)
  Freshness Requirement: Pre-market <5 min old, or previous close
  Confidence Level: MEDIUM-HIGH (80%) expected
  
  CHECK:
    ☐ Pre-market data <5 min old available, OR
    ☐ Previous close data available as fallback
```

---

### **Step 1A.3: Verify Data Sources Respond with Current Data**

```
ACTION: Test each market data source to confirm freshness

DATA SOURCES TO TEST:
  1. S&P 500 price data
  2. VIX data
  3. McClellan Oscillator data
  4. Equal-Weight Index (RSP) data
  5. Market Breadth (% above 50-day MA) data

VERIFICATION PROCESS:

For each source:
  a) Send test query
  b) Record response timestamp
  c) Calculate data age (current_time - response_timestamp)
  d) Compare age to context freshness requirement
  e) Flag if age exceeds threshold

EXAMPLE:

  Source: S&P 500
  Current Time: 2:30 PM ET (Monday, Market Open)
  Test Query: Sent @ 2:30 PM ET
  Response: S&P 500 = 5,850 @ 2:28 PM ET
  Data Age: 2 minutes
  Requirement: ≤60 min (for Market Open)
  Status: ✅ PASS (2 min < 60 min)
  
  Source: VIX
  Current Time: 2:30 PM ET (Monday, Market Open)
  Test Query: Sent @ 2:30 PM ET
  Response: VIX = 16.2 @ 2:28 PM ET
  Data Age: 2 minutes
  Requirement: ≤60 min (for Market Open)
  Status: ✅ PASS (2 min < 60 min)

TEST CHECKLIST:
  ☐ S&P 500 data freshness verified
  ☐ VIX data freshness verified
  ☐ McClellan data freshness verified
  ☐ RSP data freshness verified
  ☐ Breadth data freshness verified
  ☐ All sources within freshness requirements
```

---

### **Step 1A.4: Apply Decision Logic Based on Data Availability**

```
ACTION: Determine whether to proceed, proceed with caution, or escalate

DECISION TREE:

IF all data sources fresh AND age within context requirements:
  │
  └─ Status: ✅ PROCEED NORMALLY
  │
  │ Action: Continue to Step 2 (existing startup steps)
  │ Confidence: FULL
  │ Message: "All data sources current and fresh"
  │
  └─ Checkpoints:
       ☐ All sources within freshness requirements
       ☐ No staleness flags
       ☐ Confidence not degraded

ELSE IF most data sources fresh but some SLIGHTLY aged (≤20% over threshold):
  │
  └─ Status: ⚠️ PROCEED WITH CAUTION
  │
  │ Action: Proceed to Step 2, but flag in analysis output
  │ Confidence: DEGRADED (reduce by 5-10%)
  │ Message: "Some data sources slightly aged; proceed with caution"
  │
  └─ Example:
       S&P 500: 90 minutes old (>60 min threshold by 30 min)
       VIX: 2 minutes old (fresh)
       Breadth: 85 minutes old (>60 min threshold by 25 min)
       
       Action: Proceed, but reduce confidence by 10%

ELSE IF significant data staleness (>20% over threshold):
  │
  └─ Status: ⚠️ DEGRADED - PROCEED WITH SIGNIFICANT CAUTION
  │
  │ Action: Proceed to Step 2, but prominently flag staleness
  │ Confidence: SEVERELY DEGRADED (reduce by 20-30%)
  │ Message: "Data significantly aged; recommend revalidation"
  │
  └─ Example:
       S&P 500: 2 hours old (>60 min threshold by 60 min)
       VIX: 2 hours old (>60 min threshold by 60 min)
       
       Action: Proceed, but reduce confidence by 30%
              Include prominent ⚠️ flags in output

ELSE IF critical data unavailable OR extremely stale (>100% over threshold):
  │
  └─ Status: 🛑 STOP - ESCALATE TO STAKEHOLDER
  │
  │ Action: DO NOT proceed to Step 2
  │ Message: "Critical market data unavailable or >24 hours old"
  │ Escalation: Notify stakeholder with specific data needs
  │
  └─ Example:
       All market data from 5 days ago (weekend analysis with stale cache)
       
       Action: STOP
       Message: "Market data >3 trading days old. Cannot proceed."
       Escalation: "Please provide current market data or wait for market open"
```

---

### **Step 1A.5: Document Data Freshness Baseline**

```
ACTION: Record data freshness conditions at startup

CREATE DATA FRESHNESS BASELINE LOG:

{
  "startup_timestamp": "2025-12-01T14:30:00Z",
  "operating_context": "MARKET_OPEN",
  "data_freshness_baseline": {
    "sp500": {
      "timestamp": "2025-12-01T14:28:00Z",
      "age_minutes": 2,
      "requirement_minutes": 60,
      "status": "FRESH"
    },
    "vix": {
      "timestamp": "2025-12-01T14:28:00Z",
      "age_minutes": 2,
      "requirement_minutes": 60,
      "status": "FRESH"
    },
    "mcclelllan": {
      "timestamp": "2025-12-01T14:28:00Z",
      "age_minutes": 2,
      "requirement_minutes": 60,
      "status": "FRESH"
    },
    "rsp": {
      "timestamp": "2025-12-01T14:28:00Z",
      "age_minutes": 2,
      "requirement_minutes": 60,
      "status": "FRESH"
    },
    "breadth": {
      "timestamp": "2025-12-01T14:28:00Z",
      "age_minutes": 2,
      "requirement_minutes": 60,
      "status": "FRESH"
    }
  },
  "overall_assessment": {
    "all_sources_fresh": true,
    "any_stale_sources": false,
    "average_data_age_minutes": 2,
    "worst_data_age_minutes": 2,
    "confidence_baseline": 100,
    "confidence_degradation": 0,
    "startup_status": "PROCEED_NORMALLY"
  }
}

CHECKPOINT:
  ☐ Baseline recorded
  ☐ All data ages documented
  ☐ Startup status determined
  ☐ Decision made: proceed, proceed with caution, or escalate
```

---

### **Step 1A.6: Proceed or Escalate**

```
PROCEED TO STEP 2 IF:
  ✓ Operating context determined
  ✓ All data sources within freshness requirements
  ✓ Confidence baseline established
  ✓ Startup status = PROCEED or PROCEED_WITH_CAUTION
  
  ACTION: Continue to Step 2 (existing startup steps)

ESCALATE IF:
  ✗ Critical data unavailable
  ✗ Data >24 hours old (or context threshold exceeded by >100%)
  ✗ Startup status = STOP
  
  ACTION: Escalate to stakeholder
  MESSAGE: Specific data requirements
  OPTION 1: Wait for data to restore
  OPTION 2: Provide manual data input
  OPTION 3: Defer analysis until market open
```

---

## UPDATED STARTUP CHECKLIST (ALL EXISTING STEPS RENUMBERED +1)

```
STEP 1A: DATA FRESHNESS VALIDATION ✅ NEW
  ☐ Operating context detected
  ☐ Holiday calendar loaded
  ☐ Expected data priorities identified
  ☐ Data sources tested for freshness
  ☐ Decision made (proceed / caution / escalate)
  ☐ Baseline documented

STEP 2: [Previously Step 1] Validation Gate 10 - US Currency/Exchange
  ☐ Stock on US exchange NASDAQ, NYSE, AMEX, OTC
  ☐ Pricing in USD currency
  Must PASS before proceeding

STEP 3: [Previously Step 2] Validation Gate 4 - Data Availability
  ☐ 12-core data items verified (95% completeness)
  ☐ SEC reporting confirmed
  ☐ Market data accessible
  Must PASS before proceeding

STEP 4: [Previously Step 3] Validation Gate 1 - Market Regime Identification
  ☐ Growth / Value / Defensive / Transition identified
  ☐ Regime calibration applied
  ☐ Sector leadership ranked
  Must PASS before proceeding

STEP 5: [Previously Step 4] Phase 0 Baseline Establishment
  ☐ Portfolio context established
  ☐ Conviction history loaded
  ☐ Assumption identification (Phase 0 Guardrail C)
  Ready for Phase 1

[All subsequent steps renumbered +1]
```

---

## DATA FRESHNESS VALIDATION TEMPLATE

Use this template at every startup:

```markdown
# STARTUP DATA FRESHNESS VALIDATION — [Date] [Time] ET

## Context Determination
- Operating Context: [MARKET_OPEN | AFTER_HOURS | WEEKEND | HOLIDAY | PRE_MARKET]
- Current Time: [HH:MM] ET
- Trading Day: [Y/N]
- Expected Freshness Requirement: [≤X minutes old]

## Data Source Tests
| Source | Timestamp | Age | Requirement | Status |
|--------|-----------|-----|-------------|--------|
| S&P 500 | HH:MM ET | X min | ≤60 min | ✅ PASS |
| VIX | HH:MM ET | X min | ≤60 min | ✅ PASS |
| McClellan | HH:MM ET | X min | ≤60 min | ✅ PASS |
| RSP | HH:MM ET | X min | ≤60 min | ✅ PASS |
| Breadth | HH:MM ET | X min | ≤60 min | ✅ PASS |

## Freshness Assessment
- All Sources Fresh: YES
- Average Data Age: X minutes
- Worst Data Age: X minutes
- Confidence Degradation: 0%
- Startup Status: ✅ PROCEED NORMALLY

## Decision
Continue to Step 2 (existing startup process)
```

---

## PRODUCTION CONFIGURATION (v2.1)

All existing framework features preserved and active:

```
INCLUDEQUANTITATIVEANALYSIS: TRUE (Production)
INCLUDETAILRISKASSESSMENT: TRUE (Production)
INCLUDECORRELATIONANALYSIS: TRUE (Production - Testing in production)
INCLUDESUBSECTORANALYSIS: TRUE (Production - Testing in production)
INCLUDEANOMALYDETECTION: TRUE (Production - Testing in production)
INCLUDESCENARIOOPTIMIZATION: TRUE (Production - Testing in production)
ANALYSISFLOW: CONTINUOUSWITHCHECKPOINTS (Default)

NEW (v2.1):
GUARANTEE_DATA_FRESHNESS_VALIDATION: TRUE (MANDATORY first step)
```

---

**Status:** PRODUCTION READY - COMPLETE FILE FOR DOWNLOAD  
**Document Version:** STARTUP-REQUIREMENTS.v2.1  
**Authority:** Master-Architect-Prompt-v1.0.5 + QUALITY-Guardrails.v1.4.5

**Key Changes from v2.0:**
- Step 1A (Data Freshness Validation) inserted as NEW first step
- All existing steps renumbered +1 (Step 1 → Step 2, etc.)
- Guardrail D context-aware data validation integrated at startup
- Decision tree for proceed/caution/escalate implemented
- Data freshness baseline documented on every analysis start