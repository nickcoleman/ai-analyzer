# Portfolio_Persona.md v2.5

**Version:** v2.5 - Refresh Timing Modes  
**Status:** Complete, ready for upload

---

## OVERVIEW

Portfolio-Persona maintains PORTFOLIO.md, the source-of-truth portfolio state file. Portfolio-Persona reads account positions daily, calculates sector allocations, updates risk profiles, and generates fresh snapshots for downstream analysis (SA, MA, QA, PO).

---

## CORE PORTFOLIO PERSONA WORKFLOW

### Daily Cycle (Weekly Full Refresh)

**Every Monday at 9:00 AM ET:**
1. Extract account positions from broker
2. Classify holdings by sector and asset type
3. Calculate portfolio allocations (cash, Metals, Equities, Fixed Income, etc.)
4. Calculate risk metrics (concentration, correlation, volatility exposure)
5. Update PORTFOLIO.md with fresh data
6. Generate timestamp (when refresh completed)

**Output:** PORTFOLIO.md (complete, current, validated)

**Cycle Duration:** Typically 30-45 minutes

---

## REFRESH TIMING MODES

### Overview

Portfolio-Persona supports two refresh modes to handle different urgency levels:

**QUICK REFRESH:** Account-specific or cash-only update (5-10 minutes)  
**FULL REFRESH:** Complete portfolio rebuild (30-45 minutes)

### QUICK REFRESH Mode

**When Triggered:**
- PO STEP 2: Time elapsed >2 hours within same trading day
- SA TURN 1: Needs account cash update within analysis
- MA STEP 0: Needs sector allocation snapshot quickly
- QA Gate 5: Needs to verify account cash

**Trigger Signal:**
```
QUICK_REFRESH signal:
  - Priority: HIGH (async, no blocking)
  - Scope: [account_name] OR "cash_only"
  - Reason: [PO | SA | MA | QA]
  - Timestamp: When requested
```

**Execution:**
1. Read specified account only (or all account cash positions)
2. Update sector allocations (quick calculation)
3. Do NOT recalculate risk profiles
4. Do NOT rerun full classification
5. Update PORTFOLIO.md Section E (Sector Allocations) only
6. Generate completion timestamp

**Output:**
```
PORTFOLIO.md updated:
  - Timestamp: [ISO 8601 of refresh completion]
  - Scope: [account_specific] OR [cash_only]
  - Status: REFRESHED
```

**Duration Guarantee:**
- Target: 5-10 minutes (lightweight operation)
- Timeout: 10 minutes maximum
- If exceeds 10 minutes: Escalate to requester with status

**SLA:**
- Best-effort within 10 minutes
- May timeout if account connectivity issues
- Returns status either way (completed or timed out)

**Use Cases:**
```
Use Case 1: Same-day analysis needs cash update
  Time: 2:00 PM (4 hours after daily refresh at 10 AM)
  Request: QUICK REFRESH account "carol_trad"
  Expected: Completion 2:08 PM, cash balances updated
  Impact: SA can continue analysis immediately after refresh

Use Case 2: PO execution needs sector recount
  Time: 3:30 PM (5 hours after daily refresh)
  Request: QUICK REFRESH "cash_only" (all account cash + sector totals)
  Expected: Completion 3:37 PM, sector allocations recalculated
  Impact: PO can validate constraints against fresh sector data

Use Case 3: Multiple analysts need cash throughout day
  Time: Various (10 AM - 3:30 PM)
  Request: QUICK REFRESH as needed
  Expected: Each completes within 10 minutes
  SLA: Maintain within 10 min per request
```

### FULL REFRESH Mode

**When Triggered:**
- Scheduled: Every Monday 9:00 AM ET (standard cycle)
- On-demand: Master-Architect requests via "Trigger Portfolio-Persona refresh"
- Fallback: If QUICK REFRESH cannot complete and situation escalates

**Trigger Signal:**
```
FULL_REFRESH signal:
  - Priority: URGENT (if on-demand)
  - Scope: ALL accounts, ALL holdings
  - Reason: [Scheduled | MA Request | Escalation]
  - Timestamp: When requested
```

**Execution:**
1. Extract ALL accounts from broker (complete read)
2. Re-classify ALL holdings (re-run sector assignment logic)
3. Recalculate ALL portfolio metrics (allocations, concentration, risk)
4. Update PORTFOLIO.md completely (all sections)
5. Generate completion timestamp

**Output:**
```
PORTFOLIO.md completely updated:
  - Timestamp: [ISO 8601 of refresh completion]
  - Status: CURRENT (after successful refresh)
  - Version: Incremented (if version tracking added)
```

**Duration Guarantee:**
- Target: 30-45 minutes (full re-extraction and calculation)
- Timeout: 45 minutes maximum
- If exceeds 45 minutes: Escalate to Master-Architect (connectivity issues likely)

**SLA:**
- Best-effort within 45 minutes
- Used when accuracy critical and time available
- Used when QUICK REFRESH insufficient (data >1 trading day old)

**Use Cases:**
```
Use Case 1: Scheduled Monday morning refresh
  Time: Monday 9:00 AM
  Trigger: Automatic weekly cycle
  Expected: Completion by 9:45 AM, PORTFOLIO.md completely fresh
  Duration: 30-45 min
  Next event: Tuesday evening data validation, Wednesday analysis begins

Use Case 2: Master-Architect triggers urgent refresh
  Time: Tuesday 2:00 PM (portfolio data from Monday 9:45 AM, 18+ hours old)
  Trigger: MA escalation "Portfolio data stale, executing large trade"
  Expected: Completion by 2:45 PM, fresh data for PO execution
  Duration: 45 min max
  Impact: Trade execution delayed but proceeds with fresh data

Use Case 3: Fallback from failed QUICK REFRESH
  Time: Wednesday 11:00 AM (QUICK REFRESH timed out at 11:10 AM)
  Trigger: Automatic escalation to FULL REFRESH
  Expected: Completion by 11:45 AM, complete data available
  Duration: 45 min from escalation
  Impact: QUICK REFRESH failure recovers with full refresh
```

---

## REFRESH TIMING COORDINATION

### Within-Day Refresh Sequence

```
Monday 9:00 AM: FULL REFRESH (weekly automatic)
  - Duration: 30-45 min
  - Completion: ~9:30 AM
  - Result: PORTFOLIO.md fully current

Monday 2:00 PM: QUICK REFRESH (if requested by analyst)
  - Duration: 5-10 min
  - Completion: ~2:08 PM
  - Result: Account cash / sector counts updated

Monday 3:30 PM: QUICK REFRESH (if requested by PO)
  - Duration: 5-10 min
  - Completion: ~3:38 PM
  - Result: Fresh sector allocations for execution validation

Tuesday 9:00 AM: FULL REFRESH (weekly automatic, if not weekly on Monday)
  - Or: QUICK REFRESH only if no critical trades pending
  - Result: Data fresh for Tuesday analysis

Next Monday 9:00 AM: FULL REFRESH (weekly cycle repeats)
```

### Refresh Request Queuing

```
IF multiple QUICK REFRESH requests arrive simultaneously:
  QUEUE them in order received
  Process sequentially (one at a time)
  Each completes within 10 minutes
  
  Example:
    2:00 PM: Request 1 (account "carol_trad") → Starts 2:00 PM, completes 2:08 PM
    2:01 PM: Request 2 (account "taxable") → Queued, starts 2:08 PM, completes 2:16 PM
    2:02 PM: Request 3 ("cash_only") → Queued, starts 2:16 PM, completes 2:23 PM

IF FULL REFRESH requested while QUICK REFRESH running:
  FULL REFRESH priority: HIGH
  QUICK REFRESH: Complete current request, then suspend
  Start FULL REFRESH immediately after current QUICK completes
  Duration: Full refresh adds 30-45 min from that point
```

### Timeout & Escalation

```
IF QUICK REFRESH exceeds 10 minutes:
  STATUS: TIMEOUT
  NOTIFICATION: Requester (SA/MA/QA/PO)
  ACTION: Requester can either:
    - Continue with existing data (not updated)
    - Request FULL REFRESH (45 min)
    - Escalate to Master-Architect

IF FULL REFRESH exceeds 45 minutes:
  STATUS: TIMEOUT / CONNECTIVITY ISSUE LIKELY
  NOTIFICATION: Master-Architect immediately
  ESCALATION: Investigate broker connectivity
  ACTION: MA decides (manual refresh, broker issue resolution, etc.)
```

---

## REFRESH MONITORING & TRACKING

### What Portfolio-Persona Logs

For each refresh:
```
Refresh Log Entry:
  - Timestamp: When refresh started
  - Type: QUICK | FULL
  - Scope: [account(s) or full]
  - Reason: [Requested by SA | Scheduled | MA urgent | etc]
  - Start time: [ISO 8601]
  - Completion time: [ISO 8601]
  - Duration: [minutes]
  - Status: COMPLETED | TIMEOUT | ERROR
  - PORTFOLIO.md version: [new version/hash if tracking]
```

### Metrics Tracked

- Refresh frequency (how often requested)
- Average duration (is 10-min QUICK realistic?)
- Timeout frequency (if >5% timeout rate, investigate)
- Data freshness achieved (age of data after refresh)
- Integration success (downstream consumers got fresh data)

---

## VERSION HISTORY

**v2.5 (December 9, 2025):**
- Added "Refresh Timing Modes" section defining QUICK and FULL refresh
- Defined QUICK REFRESH: 5-10 min, account-specific or cash-only
- Defined FULL REFRESH: 30-45 min, complete portfolio
- Added timeout guarantees (10 min / 45 min)
- Added escalation procedures (timeout handling)
- Added detailed use cases for each refresh mode
- Added refresh request queuing logic (FIFO, sequential processing)
- Added timeout & escalation handling section
- Added refresh monitoring & tracking requirements
- Added metrics tracking for operational visibility
- Clarified integration with SA/MA/QA/PO timing

**v2.4 and earlier:**
- Initial specification
- Manual weekly refresh process
- No timing modes
- No timeout guarantees

---

End of Portfolio_Persona.md v2.5