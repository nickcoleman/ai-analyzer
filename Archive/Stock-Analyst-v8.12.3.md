# Stock-Analyst.md v8.12.3
## Phase 3a Update: Input Classification Consolidation

**Version:** v8.12.3  
**Previous:** v8.12.2 (Phase 3)  
**Status:** Production Ready  
**Date:** December 14, 2025, 2:01 PM MST  
**Framework:** Stock-Analyst v8.13  
**Phase:** Phase 3a (Input Classification Consolidation)  
**Changes:** Input-Classification.md v8.0.1 consolidated into SECTION 0; all Phase 3 TURNs preserved

---

## PHASE 3 CHANGES SUMMARY

### Major Enhancements
1. **TURN 1 Rewritten:** Cache-first context loading (target: <2 seconds)
2. **Async Background Refresh:** MA, EMA, revisions refreshed non-blocking
3. **Input Source Tracking:** Every input logged with source + age + penalty
4. **Cached Context Loading:** Use cached outputs from MA, Alpha, EMA if available
5. **Graceful Degradation:** Missing inputs trigger local defaults + penalties
6. **Zero External Blocking:** TURN 1 never blocks on external API availability

### Framework Invariants Maintained
- ✓ 6-TURN architecture (TURN 1-6 all complete)
- ✓ Phase 2 evidence-based penalties (TURN 4-5 from v8.13 preserved)
- ✓ 10-gate QA protocol (TURN 6 from v8.13 preserved)
- ✓ 3/21/200 EMA technical analysis (TURN 3 from v8.13 preserved)
- ✓ Conviction multiplier stack (TURN 4 from v8.13 preserved)
- ✓ Local Golden Cross computation (TURN 3 from v8.13 preserved)
- ✓ Auto-rerun system (TURN 6 from v8.13 preserved)
- ✓ QUALITY-Guardrails A–E integration (Phase 2 from v8.13 preserved)

---

## SECTION 0: INPUT CLASSIFICATION FRAMEWORK

### Objective

This section classifies all inputs to Stock-Analyst v8.13 as either **REQUIRED** (analysis fails without) or **ADVISORY** (analysis proceeds with penalty). It enables cache-first TURN 1 startup <2 seconds with zero external blocking, using cached/default inputs when fresh data unavailable.

**Key Principle:** Decouple analysis from external API availability. Analysis never blocks on missing inputs; instead, uses cached/local alternatives and applies transparent penalties.

---

### PRINCIPLE: REQUIRED vs. ADVISORY - REQUIRED INPUTS

**Analysis cannot proceed without these inputs.** If missing, analysis is **BLOCKED** (unrecoverable error).

- **PORTFOLIO.md:** Local portfolio file (REQUIRED ANY AGE at startup, but escalation to Master-Architect for outdated portfolio happens at TURN 5 execution, not TURN 1 classification)
- **Stock Ticker:** User-provided ticker (REQUIRED REAL-TIME at analysis start)

---

### PRINCIPLE: REQUIRED vs. ADVISORY - ADVISORY INPUTS

**Analysis proceeds even if missing/stale.** Missing/stale status triggers:
1. Load cached version if available
2. Use local default value if no cache
3. Apply evidence-based penalty to conviction
4. Record input source in quality_metadata for transparency

---

### INPUT CLASSIFICATION TABLE

| Input | Category | Freshness Threshold | Missing Action | Penalty | Source |
|-------|----------|-----|--------|---------|--------|
| PORTFOLIO.md | REQUIRED | Any age (escalation at TURN 5) | BLOCK analysis | — | Local file |
| Stock Ticker | REQUIRED | Real-time at start | BLOCK analysis | — | User input |
| MA Output (regime) | ADVISORY | 4 hours | Use NEUTRAL default | -2 pts | Cached async |
| Alpha Context | ADVISORY | 24 hours | Use NONE | 0 pts | Cached async |
| EMA Data (200-day) | ADVISORY | Today's close | Skip Golden Cross | -3 pts | Cached async |
| Analyst Revisions | ADVISORY | 1 day | Skip sentiment | -1 pt | Cached async |

---

### INPUT CLASSIFICATION DETAILS

#### 1.1 PORTFOLIO.md (REQUIRED)

**Requirement:** MUST exist and be readable

**Freshness Standard:** Any age acceptable at classification stage (no freshness penalty at TURN 1). Escalation to Master-Architect for outdated data happens at TURN 5 execution, not TURN 1 classification.

**If Missing:** Analysis BLOCKED (unrecoverable)
- Error message: "PORTFOLIO.md not found or corrupted. Cannot proceed."
- Action: Analyst must locate/recover portfolio file
- Recovery: Manual file recovery or escalation

**Stored Location:** Local space file (~0.1 sec load time)

**Quality Metadata Logged:**
```json
{
  "portfolio_loaded": true,
  "portfolio_age_hours": 0.5,
  "portfolio_loaded_timestamp": "2025-12-13T09:30:00Z",
  "portfolio_status": "READY"
}
```

---

#### 1.2 Stock Ticker (REQUIRED)

**Requirement:** User MUST provide a valid stock ticker

**Freshness Standard:** Real-time user input at analysis start

**Validation Rules:**
- Ticker must exist in current market data
- Ticker must either be in PORTFOLIO.md or be a tradeable security
- Format: Standard stock symbol (e.g., AAPL, TSLA, CCS)

**If Missing:** Analysis BLOCKED (unrecoverable)
- Error message: "Stock ticker not provided. Please specify ticker (e.g., AAPL)."
- Action: Analyst must provide ticker
- Recovery: User resubmits with valid ticker

**Quality Metadata Logged:**
```json
{
  "stock_ticker": "AAPL",
  "ticker_validated": true,
  "ticker_status": "VALID"
}
```

---

#### 2.1 MARKET-ANALYST OUTPUT - Regime Context (ADVISORY)

**Requirement:** ADVISORY; analysis proceeds without, with penalty

**Maximum Cache Age:** 4 hours

**Cache-First Logic:**
- If cached <4 hrs old → Use cached regime; set status CACHED (X hours old)
- If cached ≥4 hrs old → Use NEUTRAL default; apply -2 pts penalty; set status DEFAULT (STALE)
- If missing entirely → Use NEUTRAL default; set status DEFAULT (NO_CACHE)

**Cached Fields:**
- macro_regime: BULLISH, NEUTRAL, BEARISH
- regime_confidence: 0-100
- sector_leadership: Top 3 sectors ranked
- volatility_assessment: Current market volatility level

**Local Default (if no cache):**
```json
{
  "macro_regime": "NEUTRAL",
  "regime_confidence": 0,
  "rationale": "Market-Analyst output unavailable; proceeding with neutral regime assumption",
  "cache_status": "DEFAULT",
  "async_refresh_triggered": true
}
```

**Penalty Application:**
- Base conviction reduced by **-2 points** if MA output is stale or missing
- Penalty reason: Macro regime context unavailable; conviction reduced to reflect uncertainty
- Penalty type: Evidence-based (see QUALITY-Guardrails.md)

**Async Refresh:**
- Background task: Fetch fresh MA output
- Max duration: 30 seconds
- Non-blocking: Analysis continues while refresh happens
- If timeout: Keep old cache, log warning

**Quality Metadata Logged:**
```json
{
  "ma_context_source": "CACHED (2 hours old) | DEFAULT (STALE) | DEFAULT (NO_CACHE)",
  "ma_context_age_hours": 2.0,
  "ma_status": "FRESH | STALE | DEFAULT",
  "macro_regime": "NEUTRAL",
  "ma_penalty_applied_points": -2 or 0,
  "ma_async_refresh_status": "QUEUED | COMPLETED | TIMEOUT"
}
```

**Degradation Rules:** See QUALITY-Guardrails.md, Section B (Data Validation)
- If MA output unavailable: Proceed with NEUTRAL regime
- Conviction adjusted per evidence-based penalty formula
- No impact on analysis completion or execution readiness

---

#### 2.2 ALPHA-PICKS-ANALYST CONTEXT (ADVISORY)

**Requirement:** ADVISORY; analysis proceeds without, no penalty

**Maximum Cache Age:** 24 hours

**Cache-First Logic:**
- If cached <24 hrs old → Use cached Alpha context
- If cached ≥24 hrs old → Use NONE; analyst can provide manually
- If missing entirely → Use NONE

**Cached Fields:**
- alpha_candidates: List of recent picks and their conviction
- tenor_window: 75–120 day optimal tenure (analysis window)
- validation_status: Whether Alpha picks have been validated

**Local Default (if no cache):**
```json
{
  "alpha_context": "NONE",
  "rationale": "Alpha-Picks-Analyst context unavailable; proceeding without context",
  "analyst_can_provide_manually": true,
  "cache_status": "DEFAULT (NONE)",
  "async_refresh_triggered": true
}
```

**Penalty Application:**
- **NO PENALTY** if missing (0 points)
- Reasoning: Alpha context is confirmatory, not essential to core thesis
- If analyst provides context later: May boost conviction; no penalty to remove

**Async Refresh:**
- Background task: Fetch fresh Alpha-Picks context
- Max duration: 30 seconds
- Non-blocking: Analysis continues
- If timeout: Keep NONE, proceed (no error)

**Quality Metadata Logged:**
```json
{
  "alpha_context_source": "CACHED (8 hours old) | USERPROVIDED | NONE",
  "alpha_context_available": true,
  "alpha_context_age_hours": 8,
  "alpha_async_refresh_status": "QUEUED | COMPLETED | TIMEOUT"
}
```

---

#### 2.3 EMA CACHE - 200-Day Price History (ADVISORY)

**Requirement:** ADVISORY; analysis proceeds without, with penalty

**Maximum Cache Age:** Today's close required (Ideal: 200 days of EMA data available)

**Cache-First Logic:**
- If ≥200 days available → Mark COMPLETE
- If <200 days available → Mark INCOMPLETE (will skip Golden Cross multiplier; apply -3 pts penalty)
- If today's close missing → Schedule async fetch

**Cached Fields:**
- ema_50_list: 50-day EMA values for past 20 trading days
- ema_200_list: 200-day EMA values for past 20 trading days
- last_update_timestamp: When cache was last refreshed
- days_available: Count of trading days in cache

**Local Default (if incomplete):**
```json
{
  "ema_cache_status": "INCOMPLETE",
  "days_available": 150,
  "reason": "Insufficient history for Golden Cross; need 200 days",
  "golden_cross_status": "SKIPPED",
  "golden_cross_multiplier": 1.0,
  "golden_cross_penalty_applied_points": -3,
  "async_fetch_triggered": true
}
```

**Penalty Application:**
- Golden Cross multiplier set to **1.0x** (neutral, no boost)
- Conviction reduced by **-3 points** if <200 days available
- Penalty reason: EMA history incomplete; cannot compute Golden Cross signal

**Async Fetch:**
- Background task: Fetch missing EMA data
- Max duration: 20 seconds
- Non-blocking: Analysis continues
- Target: Complete 200-day history for next analysis

**Quality Metadata Logged:**
```json
{
  "ema_cache_source": "LOCAL_CACHE (200 days) | INCOMPLETE (<200 days) | WILL_FETCH",
  "ema_cache_age_hours": 0,
  "ema_cache_days_available": 200,
  "ema_cache_completeness_percent": 100,
  "golden_cross_multiplier": 1.15 or 1.0,
  "ema_penalty_applied_points": -3 or 0,
  "ema_async_fetch_status": "QUEUED | COMPLETED | IN_PROGRESS"
}
```

---

#### 2.4 ANALYST REVISIONS - Sentiment & Estimate Changes (ADVISORY)

**Requirement:** ADVISORY; analysis proceeds without, with penalty

**Maximum Cache Age:** 1 day optimal

**Cache-First Logic:**
- If cached <1 day old → Use cached revisions
- If cached ≥1 day old → Skip sentiment validation; apply -1 pt penalty
- If missing → Skip sentiment check

**Cached Fields:**
- estimate_revisions: Recent upward/downward estimate changes
- analyst_sentiment: Buy, Hold, Sell estimates
- revision_momentum: Trend of revisions (accelerating/decelerating)

**Local Default (if missing):**
```json
{
  "analyst_revisions_status": "SKIPPED",
  "rationale": "Analyst revisions not available; skipping sentiment validation",
  "revision_penalty_applied_points": -1,
  "async_fetch_triggered": true
}
```

**Penalty Application:**
- Conviction reduced by **-1 point** if revisions unavailable
- Penalty reason: Analyst sentiment validation skipped; conviction reduced for conservatism

**Async Fetch:**
- Background task: Fetch latest analyst revisions
- Max duration: 15 seconds
- Priority: LOW (lowest priority async task)
- If timeout: Skip sentiment, proceed (no error)

**Quality Metadata Logged:**
```json
{
  "analyst_revisions_source": "CACHED (12 hours old) | SKIPPED",
  "analyst_revisions_available": true,
  "analyst_revisions_age_hours": 12,
  "revision_penalty_applied_points": -1 or 0,
  "analyst_async_fetch_status": "QUEUED | COMPLETED | TIMEOUT"
}
```

---

### INPUT CLASSIFICATION DECISION TABLE

**Use this table to determine if analysis can proceed:**

| Input | Required? | Missing? | Stale? | Action | Penalty | Proceed? |
|-------|-----------|----------|--------|--------|---------|----------|
| PORTFOLIO.md | YES | YES | — | BLOCK | — | NO |
| Ticker | YES | YES | — | BLOCK | — | NO |
| MA Output | NO | No | >4 hrs | Use NEUTRAL | -2 pts | YES |
| Alpha Context | NO | Yes | >24 hrs | Use NONE | 0 pts | YES |
| EMA Data | NO | Yes | <200d | Skip Golden Cross | -3 pts | YES |
| Analyst Revisions | NO | Yes | >1 day | Skip sentiment | -1 pt | YES |

---

### INPUT FRESHNESS TRIGGERS & ESCALATIONS

**Note:** PORTFOLIO.md freshness is validated at TURN 1, but escalation to Master-Architect happens at TURN 5 execution stage, not at TURN 1 classification stage.

#### 4.1 Portfolio Data Freshness

**Freshness Standards:**
- 1 trading day old: FRESH (no escalation)
- 1–3 trading days old: STALE (escalate to Master-Architect for approval)
- >3 trading days old: VERY STALE (block analysis, request refresh)

**Escalation Routing:** See Master-Architect.md v8.0.1, FRESHNESS ESCALATION AUTHORITY

#### 4.2 Async Background Refresh Timing

**All advisory inputs trigger async background refresh in parallel:**

```
ASYNC REFRESH TASKS (Started after TURN 1)

1. REFRESH_MA_OUTPUT
   - Fetch fresh Market-Analyst output
   - Max duration: 30 seconds
   - Priority: NORMAL
   - Retry: 1 attempt; timeout = keep cache

2. UPDATE_EMA_CACHE
   - Fetch latest 50/200 EMA values
   - Max duration: 20 seconds
   - Priority: NORMAL
   - Retry: 1 attempt; timeout = keep cache

3. FETCH_ANALYST_REVISIONS
   - Fetch latest analyst estimate revisions
   - Max duration: 15 seconds
   - Priority: LOW
   - Retry: 1 attempt; timeout = skip

All tasks run in parallel (non-blocking).
None of these delay analysis start.
Analysis proceeds while async tasks complete in background.
```

---

### INPUT SOURCES METADATA TRANSPARENCY

#### 5.1 Input Sources Recorded in quality_metadata

**Every analysis records where each input came from:**

```json
{
  "input_sources": {
    "portfolio": {
      "source": "LOCAL_PORTFOLIO.md",
      "loaded_timestamp": "2025-12-13T09:30:00Z",
      "age_hours": 0.5,
      "status": "READY",
      "penalty_points": 0
    },
    "macro_regime": {
      "source": "CACHED_MA (2 hours old) | DEFAULT (NEUTRAL)",
      "loaded_timestamp": "2025-12-13T09:30:00Z",
      "age_hours": 2.0,
      "cache_status": "FRESH | STALE | DEFAULT",
      "value": "NEUTRAL | BULLISH | BEARISH",
      "penalty_points": -2 or 0
    },
    "alpha_context": {
      "source": "CACHED (8 hours old) | USERPROVIDED | NONE",
      "loaded_timestamp": "2025-12-13T09:30:00Z",
      "age_hours": 8,
      "available": true,
      "penalty_points": 0
    },
    "ema_data": {
      "source": "LOCAL_CACHE (200 days) | INCOMPLETE (<200 days)",
      "loaded_timestamp": "2025-12-13T09:30:00Z",
      "age_hours": 0,
      "days_available": 200,
      "cache_completeness_percent": 100,
      "golden_cross_enabled": true,
      "penalty_points": -3 or 0
    },
    "analyst_revisions": {
      "source": "CACHED (12 hours old) | SKIPPED",
      "loaded_timestamp": "2025-12-13T09:30:00Z",
      "age_hours": 12,
      "available": true,
      "penalty_points": -1 or 0
    },
    "penalties_applied": {
      "ma_unavailable": -2 or 0,
      "ema_incomplete": -3 or 0,
      "sentiment_skipped": -1 or 0,
      "total_input_penalty": -6 or less
    }
  }
}
```

#### 5.2 Analyst Transparency Requirement

**All analyses must include input sources summary in quality_metadata:**

```
INPUT SOURCES TRANSPARENCY EXAMPLE

Portfolio: LOCAL_PORTFOLIO.md (0.5 hours old) — fresh
Macro Regime: CACHED_MA (2 hours old) — used as-is
EMA Data: LOCAL_CACHE (200 days available) — Golden Cross enabled
Analyst Revisions: CACHED (12 hours old) — used as-is

Input Penalties Applied: -0 pts (all inputs fresh)

This analysis proceeded with fresh data from all sources.
```

---

### CROSS-REFERENCES TO DEGRADATION RULES

For detailed degradation rules when inputs missing/stale, see:

- **QUALITY-Guardrails.md v8.1.0** – Guardrail B (Data Validation)
  - Specific rules for missing vs. stale inputs
  - Data source validation
  - Framework dependency verification

- **Quality-Assurance-Engineer.md v8.0.7** – Gate 1 (Portfolio Freshness)
  - Portfolio freshness validation at QA gate
  - Escalation routing to Master-Architect
  - Threshold definitions (fresh, stale, very stale)

- **Master-Architect.md v8.0.1** – Freshness Escalation Authority
  - Master-Architect decision options
  - Response SLAs (15 min, 30 min, held)
  - Exception handling for pre-market analysis

---

### COMPLIANCE CHECKLIST

#### Input Classification Complete
- ☐ All inputs classified as REQUIRED or ADVISORY
- ☐ REQUIRED inputs present (PORTFOLIO.md, Ticker)
- ☐ ADVISORY inputs checked for cache (MA, Alpha, EMA, Revisions)
- ☐ All missing/stale inputs handled per table (use default, apply penalty)

#### Cache-First Loading Implemented
- ☐ TURN 1 checks local caches first (<0.05 sec per cache)
- ☐ No external API blocking in TURN 1 startup
- ☐ Async background refresh queued (non-blocking)
- ☐ TURN 1 completes in <2 seconds

#### Penalties Applied Transparently
- ☐ MA unavailable: -2 pts or 0 if fresh
- ☐ EMA incomplete: -3 pts or 0 if 200 days
- ☐ Analyst revisions missing: -1 pt or 0 if available
- ☐ Alpha context missing: 0 pts (confirmatory only)
- ☐ Total penalties recorded in quality_metadata

#### Input Sources Documented
- ☐ Every input source recorded in quality_metadata.input_sources
- ☐ Cache ages logged (X hours old)
- ☐ Default vs. fresh designation clear
- ☐ Penalties applied visible to analyst

#### Async Background Tasks Queued
- ☐ REFRESH_MA_OUTPUT: Queued, max 30 sec
- ☐ UPDATE_EMA_CACHE: Queued, max 20 sec
- ☐ FETCH_ANALYST_REVISIONS: Queued, max 15 sec
- ☐ All tasks non-blocking, run in parallel

---

### EXAMPLES

#### Example 1: Fresh Data – All Inputs Available

```
Analysis Start: 2025-12-13 09:30 AM ET

TURN 1 Input Classification

Step 1A: Load PORTFOLIO.md
  Status: Ready (loaded 30 min ago)
  Age: 0.5 hours

Step 1B: Check MA Cache
  Status: Available (2 hours old, <4 hour max)
  Regime: BULLISH
  Age: 2 hours
  Penalty: 0 pts (fresh)

Step 1C: Check Alpha Cache
  Status: Available (8 hours old, <24 hour max)
  Available: YES
  Age: 8 hours
  Penalty: 0 pts (fresh)

Step 1D: Check EMA Cache
  Status: Complete (200 days available)
  Days: 200
  Golden Cross: ENABLED
  Penalty: 0 pts (complete)

Step 1E: Check Analyst Revisions
  Status: Available (12 hours old, <1 day max)
  Available: YES
  Age: 12 hours
  Penalty: 0 pts (fresh)

TURN 1 TOTAL LATENCY: 1.2 seconds

Input Penalties Applied: -0 pts (all fresh)

RESULT: All inputs fresh; analysis proceeds with full technical & sentiment context
```

#### Example 2: Stale MA Data – Cache 4+ Hours Old

```
Analysis Start: 2025-12-13 02:30 PM ET

TURN 1 Input Classification

Step 1B: Check MA Cache
  Status: Available (6 hours old, >4 hour max)
  Cached Regime: BULLISH (stale)
  Action: Use NEUTRAL default
  Penalty: -2 pts

Quality Metadata Alert:
  Macro-Analyst output stale (6 hours old). Proceeding with NEUTRAL regime default.
  Conviction reduced -2 pts to reflect macro uncertainty.
  Fresh MA output loading asynchronously.

Async Task:
  REFRESH_MA_OUTPUT queued (max 30 sec)
  Will update cache in background
  Does not block analysis start

TURN 1 TOTAL LATENCY: 1.1 seconds

Input Penalties Applied: -2 pts (MA stale)

RESULT: Analysis proceeds with NEUTRAL regime; fresh MA being loaded in background
```

#### Example 3: Missing EMA Cache – <200 Days Available

```
Analysis Start: 2025-12-13 10:00 AM ET

TURN 1 Input Classification

Step 1D: Check EMA Cache
  Status: Available (only 150 days old)
  Days: 150
  Days Required: 200
  Action: Skip Golden Cross, use 1.0x multiplier
  Penalty: -3 pts

Quality Metadata Alert:
  EMA history incomplete (150 days, need 200 days). Golden Cross analysis SKIPPED.
  Conviction reduced -3 pts to reflect technical uncertainty.
  Fresh EMA data loading asynchronously.

Async Task:
  UPDATE_EMA_CACHE queued (max 20 sec)
  Will fetch missing 50 days
  Cache refreshed for next analysis

TURN 1 TOTAL LATENCY: 1.3 seconds

Input Penalties Applied: -3 pts (EMA incomplete)

RESULT: Analysis proceeds without Golden Cross multiplier; data being fetched asynchronously
```

---

## TURN 1: STARTUP CONTEXT (CACHE-FIRST, ASYNC LOADING)

### Objective
Load portfolio context in <2 seconds with **zero external API blocking**. Use cached/local defaults when fresh data unavailable. Trigger async background refresh (non-blocking).

Reference: **See SECTION 0: INPUT CLASSIFICATION FRAMEWORK above for classification logic.**

### TURN 1 Flow: 6-Step Sequence

```
TURN 1: Startup Context (Cache-First, Async Loading)
Target Latency: <2 seconds (was 15 min with blocking calls)

Step 1A: Load Local Portfolio (0.1 sec)
  └─ Action: Load PORTFOLIO.md from local storage
  └─ Duration: ~0.1 sec (fast local file I/O)
  └─ Requirement: File MUST exist
  └─ Fallback: BLOCK (unrecoverable) if missing
  └─ Log: portfolio_loaded_timestamp, portfolio_age_hours

Step 1B: Load Cached MA Output (0.2 sec)
  └─ Action: Check local MA cache (if exists)
  └─ Duration: ~0.05 sec
  └─ Age Check:
     ├─ If cache <4 hours old → Use cached regime (BULLISH/NEUTRAL/BEARISH)
     ├─ If cache ≥4 hours old → Use NEUTRAL default
     └─ If missing → Use NEUTRAL default (no penalty at load; recorded at calculation)
  └─ Trigger Async: Queue REFRESH_MA_OUTPUT (non-blocking)
  └─ Log: ma_cache_age_hours, ma_status (FRESH | STALE | DEFAULT)

Step 1C: Load Cached Alpha Context (0.2 sec)
  └─ Action: Check local Alpha Picks cache (if exists)
  └─ Duration: ~0.05 sec
  └─ Age Check:
     ├─ If cache <24 hours old → Use cached context
     ├─ If cache ≥24 hours old → Use NONE
     └─ If missing → Use NONE
  └─ Trigger Async: Queue UPDATE_ALPHA_CONTEXT (non-blocking)
  └─ Log: alpha_cache_available (true|false), alpha_age_hours

Step 1D: Load EMA Cache (0.5 sec)
  └─ Action: Load cached 50/200 EMA values for past 20 trading days
  └─ Duration: ~0.05 sec
  └─ Requirement: Need 200-day history for Golden Cross
  └─ Age Check:
     ├─ If ≥200 days available → Mark complete
     └─ If <200 days → Mark incomplete (will skip Golden Cross multiplier; -3 pts penalty in TURN 4)
  └─ Trigger Async: Queue UPDATE_EMA_CACHE (non-blocking)
  └─ Log: ema_cache_completeness (%), ema_days_available, ema_cache_age_hours

Step 1E: Accept User Inputs (0.1 sec)
  └─ Action: Receive ticker, analysis_type (NEW_PICK | UPDATE | REBALANCE)
  └─ Duration: <0.1 sec
  └─ Validation: Stock ticker exists in PORTFOLIO or is tradeable
  └─ Requirement: User MUST provide valid ticker (BLOCK if missing)
  └─ Log: user_inputs_received_timestamp, analysis_type

Step 1F: Record Startup Metrics (0.05 sec)
  └─ Action: Measure total TURN 1 duration
  └─ Calculation: turn_1_latency_seconds = (current_time - turn_1_start_time)
  └─ Target: <2 seconds (was <5 sec in v8.13; now tightened for Phase 3)
  └─ Log: turn_1_latency_seconds, startup_context_complete

Total Startup Time: <2 seconds ✓ (no external blocking)

ASYNC BACKGROUND (Non-blocking, Queued During TURN 1):
  ├─ REFRESH_MA_OUTPUT: Fetch fresh Market-Analyst output (max 30 sec)
  ├─ UPDATE_EMA_CACHE: Fetch latest 50/200 EMA values (max 20 sec)
  ├─ UPDATE_ALPHA_CONTEXT: Fetch fresh Alpha-Picks context (max 30 sec)
  └─ FETCH_ANALYST_REVISIONS: Fetch latest analyst revisions (max 15 sec)

All async tasks run in parallel. None delay analysis start.
```

### Cache-First Loading Logic (Pseudocode)

```
FUNCTION turn_1_startup():
  
  // Step 1A: Load Portfolio
  IF file_exists(PORTFOLIO.md):
    portfolio = load_file(PORTFOLIO.md)
    portfolio_loaded = true
    portfolio_age_hours = (now - file_mtime(PORTFOLIO.md)) / 3600
  ELSE:
    ERROR: "PORTFOLIO.md not found; analysis BLOCKED"
    return BLOCK
  
  // Step 1B: Load MA Cache
  IF cache_exists(MA_CACHE) AND cache_age(MA_CACHE) < 4_hours:
    ma_regime = load_cache(MA_CACHE).regime
    ma_source = "CACHED"
    ma_age_hours = cache_age(MA_CACHE)
  ELSE IF cache_exists(MA_CACHE):
    ma_regime = "NEUTRAL"
    ma_source = "DEFAULT (STALE)"
    ma_age_hours = cache_age(MA_CACHE)
  ELSE:
    ma_regime = "NEUTRAL"
    ma_source = "DEFAULT (NO_CACHE)"
    ma_age_hours = null
  
  // Trigger async refresh
  async_queue.add(task: "REFRESH_MA_OUTPUT", priority: "NORMAL", max_duration: 30_sec)
  
  // Step 1C: Load Alpha Cache
  IF cache_exists(ALPHA_CACHE) AND cache_age(ALPHA_CACHE) < 24_hours:
    alpha_context = load_cache(ALPHA_CACHE)
    alpha_source = "CACHED"
    alpha_age_hours = cache_age(ALPHA_CACHE)
  ELSE:
    alpha_context = null
    alpha_source = "DEFAULT_NONE"
    alpha_age_hours = null
  
  // Trigger async refresh
  async_queue.add(task: "UPDATE_ALPHA_CONTEXT", priority: "NORMAL", max_duration: 30_sec)
  
  // Step 1D: Load EMA Cache
  IF cache_exists(EMA_CACHE):
    ema_data = load_cache(EMA_CACHE)
    ema_days = count_trading_days(ema_data)
    ema_completeness = (ema_days / 200) * 100
    IF ema_days >= 200:
      ema_ready = true
      ema_status = "COMPLETE"
    ELSE:
      ema_ready = false
      ema_status = "INCOMPLETE"
      ema_penalty = -3  // Will apply in TURN 4
  ELSE:
    ema_data = null
    ema_ready = false
    ema_status = "UNAVAILABLE"
    ema_penalty = -3
  
  // Trigger async refresh
  async_queue.add(task: "UPDATE_EMA_CACHE", priority: "NORMAL", max_duration: 20_sec)
  
  // Step 1E: Accept User Inputs
  stock_ticker = get_user_input("stock_ticker")
  analysis_type = get_user_input("analysis_type") OR default_to("UPDATE")
  
  IF NOT is_valid_ticker(stock_ticker):
    ERROR: f"Invalid ticker: {stock_ticker}; analysis BLOCKED"
    return BLOCK
  
  // Step 1F: Record Metrics
  turn_1_latency = current_time - turn_1_start_time
  IF turn_1_latency > 2_sec:
    WARNING: f"TURN 1 latency {turn_1_latency}s exceeded 2-sec target"
  
  // Queue analyst revisions fetch (low priority)
  async_queue.add(task: "FETCH_ANALYST_REVISIONS", priority: "LOW", max_duration: 15_sec)
  
  // Return startup context
  RETURN {
    portfolio: portfolio,
    ma_regime: ma_regime,
    alpha_context: alpha_context,
    ema_data: ema_data,
    stock_ticker: stock_ticker,
    analysis_type: analysis_type,
    input_sources: {
      portfolio: { source: "LOCAL", age_hours: portfolio_age_hours },
      ma_regime: { source: ma_source, age_hours: ma_age_hours },
      alpha: { source: alpha_source, age_hours: alpha_age_hours },
      ema: { source: ema_status, completeness: ema_completeness }
    },
    async_tasks_queued: async_queue.count(),
    turn_1_latency_seconds: turn_1_latency
  }
```

### Input Sources Metadata (Logged After TURN 1)

```json
{
  "input_sources": {
    "portfolio": {
      "source": "LOCAL_PORTFOLIO.md",
      "loaded_timestamp": "2025-12-13T09:30:00Z",
      "age_hours": 0.5,
      "status": "READY",
      "penalty_points": 0
    },
    "macro_regime": {
      "source": "CACHED_MA (2 hours old) | DEFAULT (NEUTRAL)",
      "loaded_timestamp": "2025-12-13T09:30:00Z",
      "age_hours": 2.0,
      "cache_status": "FRESH | STALE | DEFAULT",
      "value": "NEUTRAL",
      "penalty_applied_in_turn4": -2 or 0
    },
    "alpha_context": {
      "source": "DEFAULT_NONE",
      "loaded_timestamp": "2025-12-13T09:30:00Z",
      "age_hours": null,
      "available": false,
      "penalty_points": 0
    },
    "ema_data": {
      "source": "LOCAL_CACHE (COMPLETE - 200+ days)",
      "loaded_timestamp": "2025-12-13T09:30:00Z",
      "age_hours": 0,
      "days_available": 200,
      "cache_completeness_percent": 100,
      "golden_cross_enabled": true,
      "penalty_applied_in_turn4": 0
    },
    "analyst_revisions": {
      "source": "QUEUED_FOR_ASYNC_FETCH",
      "status": "PENDING",
      "penalty_points": -1 or 0 (applied if missing after async)
    },
    "penalties_summary": {
      "ma_stale_or_missing": -2 or 0,
      "ema_incomplete": -3 or 0,
      "analyst_revisions_missing": -1 or 0,
      "total_input_penalty_applied_in_turn4": -6 or less
    }
  },
  "turn_1_latency_seconds": 1.2,
  "startup_status": "CONTEXT_READY",
  "async_tasks_queued": 4,
  "async_task_details": [
    {
      "task": "REFRESH_MA_OUTPUT",
      "status": "QUEUED",
      "priority": "NORMAL",
      "max_duration_seconds": 30,
      "started_timestamp": "2025-12-13T09:30:01Z"
    },
    {
      "task": "UPDATE_EMA_CACHE",
      "status": "QUEUED",
      "priority": "NORMAL",
      "max_duration_seconds": 20,
      "started_timestamp": "2025-12-13T09:30:01Z"
    },
    {
      "task": "UPDATE_ALPHA_CONTEXT",
      "status": "QUEUED",
      "priority": "NORMAL",
      "max_duration_seconds": 30,
      "started_timestamp": "2025-12-13T09:30:01Z"
    },
    {
      "task": "FETCH_ANALYST_REVISIONS",
      "status": "QUEUED",
      "priority": "LOW",
      "max_duration_seconds": 15,
      "started_timestamp": "2025-12-13T09:30:01Z"
    }
  ]
}
```

### Async Background Refresh (Parallel, Non-Blocking)

After TURN 1 completes (latency <2 sec), async tasks run in background:

```
ASYNC BACKGROUND REFRESH TASKS (Started immediately after TURN 1)

All tasks run in parallel; none block analysis progression to TURN 2.

1. REFRESH_MA_OUTPUT (NORMAL Priority)
   ├─ Fetch fresh Market-Analyst output
   ├─ Compare with cached version
   ├─ If regime changed: Log alert in quality_metadata
   ├─ Max duration: 30 seconds
   ├─ Timeout behavior: Keep old cache, log warning
   └─ Update local MA cache with fresh data
   
2. UPDATE_EMA_CACHE (NORMAL Priority)
   ├─ Fetch latest 50/200 EMA values
   ├─ Verify ≥200 trading days available
   ├─ Update local EMA cache
   ├─ Max duration: 20 seconds
   ├─ Timeout behavior: Keep old cache, log warning
   └─ If completed: Golden Cross will have fresh data for next analysis
   
3. UPDATE_ALPHA_CONTEXT (NORMAL Priority)
   ├─ Fetch fresh Alpha-Picks context
   ├─ Check recent picks and conviction trends
   ├─ Update local Alpha cache
   ├─ Max duration: 30 seconds
   ├─ Timeout behavior: Keep NONE, proceed (no error)
   └─ If completed: Alpha context available for TURN 4 thesis validation

4. FETCH_ANALYST_REVISIONS (LOW Priority)
   ├─ Fetch latest analyst estimate revisions
   ├─ Check revision momentum (accelerating/decelerating)
   ├─ Update sentiment context
   ├─ Max duration: 15 seconds
   ├─ Timeout behavior: Skip revision check, proceed (no error)
   └─ If completed: Sentiment validation enabled in TURN 4

All tasks timeout independently. Completion of one task does not affect others.
Failed tasks are logged but do not block analysis.
```

---

## TURN 2: RESEARCH & FUNDAMENTAL ANALYSIS (PRESERVED FROM v8.13)

Analyst researches:
- Company fundamentals (revenue, growth, profitability, margins, ROIC)
- Industry dynamics and competitive position
- Macro tailwinds/headwinds (rates, credit, sector dynamics)
- Management quality and strategy
- Valuation relative to peers (P/E, EV/EBITDA, PEG, DCF)

**Deliverable:** Fundamental conviction score (60-80 typical) and thesis summary for TURN 4 base conviction.

---

## TURN 3: TECHNICAL ANALYSIS & LOCAL GOLDEN CROSS (PRESERVED FROM v8.13)

### 3A: EMA Technical Analysis (3/21/200)

Compute three exponential moving averages:
- EMA-3: 3-day EMA (immediate momentum)
- EMA-21: 21-day EMA (medium-term trend)
- EMA-200: 200-day EMA (long-term trend)

**Deliverable:** EMA multiplier (0.40 to 1.15) applied to base conviction in TURN 4.

### 3B: LOCAL GOLDEN CROSS COMPUTATION

Compute Golden Cross status locally; no escalation to MA. Golden Cross multiplier (0.70–1.20) applied to conviction in TURN 4.

**No Escalation:** Golden Cross signal remains local; multiplier applied in TURN 4.

---

## TURN 4: CONVICTION SCORING WITH EVIDENCE-BASED PENALTIES (PRESERVED FROM v8.13)

### 4A–4G: Full Conviction Calculation

All Phase 2 evidence-based penalty logic from v8.13 preserved in full:

- **4A:** Base conviction establishment
- **4B:** Assumption identification + confidence estimation
- **4C:** Evidence-based penalty calculation
- **4D:** Conviction multiplier stack
- **4E:** Conviction range calculation
- **4F:** Scenario sensitivity analysis
- **4G:** Hard stops applied

**Input Penalties Applied in TURN 4:**

During conviction calculation, apply input-source penalties recorded in TURN 1:

```
adjusted_conviction = base_conviction × ema_multiplier × golden_cross_multiplier - evidence_based_penalties - input_source_penalties

Where input_source_penalties include:
  - ma_penalty = -2 pts (if MA cache stale/missing)
  - ema_penalty = -3 pts (if EMA cache <200 days)
  - revision_penalty = -1 pt (if analyst revisions missing after async timeout)
```

**Quality Metadata:** All input_source penalties logged in `quality_metadata.input_sources.penalties_applied`

---

## TURN 5: CONSTRAINT VALIDATION & POSITION STRUCTURE (PRESERVED FROM v8.13)

All Phase 2 constraint validation logic from v8.13 preserved:

- **5A:** Conviction tier validation (softened tier ceilings)
- **5B:** Position sizing from conviction tier

---

## TURN 6: QA GATES & AUTO-RERUN SYSTEM (PRESERVED FROM v8.13)

### 10 QA Gates + Auto-Rerun

All Phase 1 + Phase 2 QA logic from v8.13 preserved:

- **Gates 1–8:** Standard QA checks (portfolio freshness, sector constraints, cash validation, etc.)
- **Gates 9–10:** EMA technical analysis + Golden Cross detection
- **Auto-Rerun:** Up to 3 attempts per check; recovers from schema/calculation errors

**NEW in Phase 3:** Input source tracking includes async task completion status:

```json
{
  "async_task_status": {
    "REFRESH_MA_OUTPUT": "COMPLETED | TIMEOUT",
    "UPDATE_EMA_CACHE": "COMPLETED | TIMEOUT",
    "UPDATE_ALPHA_CONTEXT": "COMPLETED | TIMEOUT",
    "FETCH_ANALYST_REVISIONS": "COMPLETED | TIMEOUT"
  },
  "penalties_from_async_timeouts": {
    "ma_timeout": -2 or 0,
    "ema_timeout": -3 or 0,
    "revision_timeout": -1 or 0,
    "total": -6 or less
  }
}
```

---

## QUALITY METADATA UPDATES (Phase 3 Enhancements)

Comprehensive quality_metadata structure now includes input_sources tracking:

```json
{
  "quality_metadata": {
    "analysis_id": "SA_20251213_AAPL_001",
    "analysis_timestamp": "2025-12-13T09:30:00Z",
    
    "turn_1_latency_seconds": 1.2,
    
    "input_sources": {
      "portfolio": {
        "source": "LOCAL_PORTFOLIO.md",
        "age_hours": 0.5,
        "penalty_points": 0
      },
      "macro_regime": {
        "source": "CACHED_MA (2 hours old)",
        "age_hours": 2.0,
        "penalty_applied_in_turn4": -2 or 0
      },
      "alpha_context": {
        "source": "DEFAULT_NONE",
        "penalty_points": 0
      },
      "ema_data": {
        "source": "LOCAL_CACHE (200+ days)",
        "completeness_percent": 100,
        "penalty_points": 0
      },
      "analyst_revisions": {
        "source": "ASYNC_FETCH_PENDING",
        "penalty_if_timeout": -1 or 0
      },
      "penalties_summary": {
        "total_input_penalty": -6 or less,
        "transparency": "All input sources logged with age + penalty"
      }
    },
    
    "async_task_status": {
      "REFRESH_MA_OUTPUT": "COMPLETED | TIMEOUT",
      "UPDATE_EMA_CACHE": "COMPLETED | TIMEOUT",
      "UPDATE_ALPHA_CONTEXT": "COMPLETED | TIMEOUT",
      "FETCH_ANALYST_REVISIONS": "COMPLETED | TIMEOUT",
      "all_tasks_completed_or_timed_out": true,
      "tasks_completed_count": 4,
      "tasks_timed_out_count": 0
    },
    
    "conviction_calculation": {
      "base": 75,
      "ema_multiplier": 1.05,
      "golden_cross_multiplier": 1.15,
      "pre_penalty_conviction": 90.56,
      "evidence_based_penalties": -2.68,
      "input_source_penalties": -0,
      "final_point_estimate": 87.88,
      "conviction_range": [80, 94],
      "tier": "VERY_HIGH",
      "transparency": "75 × 1.05 × 1.15 - 2.68 - 0 = 87.88 → [80-94]"
    },
    
    "checks_executed": {
      "total": 10,
      "passed": 10,
      "failed": 0,
      "details": [
        { "check_name": "PORTFOLIO_FRESHNESS", "status": "PASSED" },
        { "check_name": "INPUT_SOURCES_DOCUMENTED", "status": "PASSED" },
        // ... remaining 8 gates ...
      ]
    },
    
    "rerun_summary": {
      "total_reruns": 2,
      "failed_checks_recovered": [
        "CONVICTION_CALCULATION",
        "SECTOR_CONSTRAINT_VALIDATION"
      ],
      "recovery_rate_percent": 100
    },
    
    "alerts": [
      {
        "alert_id": "ASYNC_MA_TIMEOUT",
        "severity": "INFO",
        "message": "MA refresh timeout; using cached regime",
        "penalty_points": -2,
        "disclosure": "Analysis proceeded with 2-hour-old cached MA output"
      }
    ]
  }
}
```

---

## DEGRADATION RULES & INPUT PENALTIES

When inputs missing or stale, analysis proceeds with penalty and disclosure:

| Input | Missing/Stale | Action | Penalty | Disclosure |
|-------|---|---|---|---|
| PORTFOLIO.md | Missing | BLOCK | — | Unrecoverable error |
| Stock Ticker | Missing | BLOCK | — | Unrecoverable error |
| MA Output | >4 hrs old | Use NEUTRAL | -2 pts (in TURN 4) | Alert in quality_metadata |
| Alpha Context | >24 hrs old OR missing | Use NONE | 0 pts | Alert in quality_metadata |
| EMA Cache | <200 days | Skip Golden Cross | -3 pts (in TURN 4) | Alert in quality_metadata |
| Analyst Revisions | >1 day OR missing | Skip sentiment | -1 pt (in TURN 4) | Alert in quality_metadata |

---

## TURN 1 LATENCY COMPARISON (Phase 3 vs Phase 2)

| Phase | TURN 1 Target | Approach | Blocking Calls | Latency |
|-------|---|---|---|---|
| **Phase 2 (v8.13)** | <5 sec | Cache-first but no async | MA/EMA/Alpha no longer block | 1-2 sec |
| **Phase 3 (v8.12.2)** | <2 sec | Cache-first + async refresh | Async queued immediately after startup | 1-2 sec |

**Improvement:** Same latency (1-2 sec), but now async background refresh happens in parallel, improving data freshness for future analyses.

---

## EXAMPLE: TURN 1 EXECUTION TRACE

```
2025-12-13 09:30:00 - TURN 1 START (Analysis of AAPL)

09:30:00.0 - Step 1A: Load PORTFOLIO.md
  Status: SUCCESS (loaded in 0.1 sec)
  Portfolio Age: 0.5 hours
  
09:30:00.1 - Step 1B: Check MA Cache
  Status: SUCCESS (found cache 2 hours old)
  Cache Age: 2 hours < 4-hour max
  Action: Use cached BULLISH regime
  Async: REFRESH_MA_OUTPUT queued
  
09:30:00.15 - Step 1C: Check Alpha Cache
  Status: NOT_FOUND (no cache older than 24 hours)
  Action: Use NONE
  Async: UPDATE_ALPHA_CONTEXT queued
  
09:30:00.20 - Step 1D: Load EMA Cache
  Status: SUCCESS (200 days available)
  Days: 200 = 100% complete
  Action: Ready for Golden Cross
  Async: UPDATE_EMA_CACHE queued
  
09:30:00.25 - Step 1E: Accept User Inputs
  Status: SUCCESS
  Ticker: AAPL (validated)
  Type: UPDATE
  
09:30:00.30 - Step 1F: Record Startup Metrics
  TURN 1 Latency: 0.3 seconds ✓
  Startup Status: CONTEXT_READY
  
09:30:00.31 - Async Tasks Started (Non-blocking)
  ├─ REFRESH_MA_OUTPUT: Started
  ├─ UPDATE_EMA_CACHE: Started
  ├─ UPDATE_ALPHA_CONTEXT: Started
  └─ FETCH_ANALYST_REVISIONS: Started

09:30:00.35 - TURN 2 START
  (Analysis continues while async tasks run in background)
  
[Background: Async tasks complete by 09:30:25]
  REFRESH_MA_OUTPUT: COMPLETED (2.1 sec)
  UPDATE_EMA_CACHE: COMPLETED (1.8 sec)
  UPDATE_ALPHA_CONTEXT: COMPLETED (0.9 sec)
  FETCH_ANALYST_REVISIONS: COMPLETED (0.7 sec)

[Quality Metadata Logged at TURN 1 Completion]
  input_sources.macro_regime.source: "CACHED_MA (2 hours old)"
  input_sources.macro_regime.penalty_applied_in_turn4: 0 (fresh)
  input_sources.ema_data.source: "LOCAL_CACHE (COMPLETE)"
  input_sources.ema_data.penalty_points: 0 (complete)
  input_sources.analyst_revisions.source: "ASYNC_FETCH_COMPLETED"
  input_sources.analyst_revisions.penalty_points: 0 (available)
  
  async_task_status.all_tasks_completed_or_timed_out: true
  async_task_status.tasks_timed_out_count: 0
```

---

## FRAMEWORK INVARIANTS MAINTAINED

✅ **6-TURN Architecture:** All 6 TURNs complete (TURN 1 enhanced, TURN 2-6 preserved)  
✅ **Phase 2 Evidence-Based Penalties:** TURN 4 logic from v8.13 preserved  
✅ **Conviction Ranges:** Phase 2 TURN 4 range generation preserved  
✅ **10-Gate QA Protocol:** All gates from v8.13 preserved  
✅ **3/21/200 EMA Technical Analysis:** TURN 3 core framework intact  
✅ **Conviction Multiplier Stack:** TURN 4 multipliers preserved  
✅ **Local Golden Cross:** No escalation to MA (TURN 3 preserved)  
✅ **Auto-Rerun System:** Up to 3 attempts per check (TURN 6 preserved)  
✅ **Graceful Degradation:** Missing/stale inputs trigger defaults + penalties  
✅ **Zero External Blocking:** TURN 1 <2 sec, async queue only  

---

## COMPLIANCE CHECKLIST

**Before analysis delivery, verify:**

- ☐ TURN 1 latency <2 seconds (was <5 in Phase 2)
- ☐ All four async tasks queued (MA, EMA, Alpha, Revisions)
- ☐ No external blocking calls in TURN 1 (all async)
- ☐ Input sources documented in quality_metadata
- ☐ Penalties applied transparently (MA, EMA, Revisions)
- ☐ Async task status logged (COMPLETED or TIMEOUT)
- ☐ TURN 2-6 all executed per Phase 2 spec
- ☐ Final conviction includes input-source penalties
- ☐ All 10 QA gates passed
- ☐ Quality metadata complete + transparent

---

## VERSION HISTORY

| Version | Date | Summary | Status |
|---------|------|---------|--------|
| v8.12.1 | Dec 13, 2025 | Phase 1: Cache-first startup, local Golden Cross, auto-rerun | Superseded |
| v8.12.2 | Dec 13, 2025 | Phase 3: Async background refresh; input_sources tracking; <2 sec TURN 1 | Superseded |
| **v8.12.3** | **Dec 14, 2025** | **Phase 3a: Input-Classification consolidated into SECTION 0** | **Active** |

---

**END OF Stock-Analyst.md v8.12.3**

**Status:** Production Ready - Phase 3a Deliverable  
**Last Updated:** December 14, 2025, 2:01 PM MST  
**Phase Authority:** Master-Architect (Phase 3a Consolidation)  
**Approval:** Phase 3a DIRECTIVE AUTHORIZED  
**Next Phase:** Phase 4 Ready
