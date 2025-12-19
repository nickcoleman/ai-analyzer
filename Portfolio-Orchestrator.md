# Portfolio-Orchestrator.md v8.0.4

**Version:** v8.0.4 - Trade Validation & Recommendation Engine  
**Status:** Complete, ready for deployment  
**Last Updated:** December 15, 2025, 5:52 PM MST  

---

## OVERVIEW

Portfolio-Orchestrator is a **trade validation and recommendation engine** that prepares trades for human execution. It receives approved positions from Quality-Assurance, validates them against portfolio constraints, and generates a recommendation report for human trader review and execution.

### Architectural Principle: HUMAN-IN-THE-LOOP

**Portfolio-Orchestrator does NOT execute trades.** Instead:

- ✅ **Validates** trades against constraints (cash buffer, sector caps, position limits)
- ✅ **Flags risks** and conflicts to human trader
- ✅ **Generates recommendation report** (SAFE / CONDITIONAL / BLOCKED)
- ✅ **Routes to human trader** for final approval
- ✅ **Monitors execution post-trade** and logs audit trail

### Why Human-in-the-Loop?

1. **Perplexity has no broker API access** (security, licensing constraints)
2. **eTrade requires human authorization** (compliance, 2FA, audit trail)
3. **Human trader makes final execution decision** (fiduciary responsibility)
4. **Trades executed manually on eTrade** by authorized user

### Execution Flow

```
1. Stock-Analyst produces recommendation (BUY NVDA 12%, conviction 74)
   ↓
2. Quality-Assurance validates analysis (APPROVED_FOR_EXECUTION)
   ↓
3. Portfolio-Orchestrator validates trade vs. constraints
   ↓
4. Human trader reviews Orchestrator's recommendation report
   ↓
5. Human trader executes on eTrade manually (with 2FA)
   ↓
6. Human trader reports execution to Orchestrator
   ↓
7. Orchestrator logs trades, updates PORTFOLIO.md, sets monitoring
```

---

## STEP 1 – STARTUP: PORTFOLIO DATA VALIDATION

### Purpose

Confirm Portfolio-Persona has latest portfolio state before validating any trades.

### Process

**Check portfolio status:**
- PORTFOLIO.md timestamp
- Current portfolio value, cash position, sector allocations
- Any pending trades that executed since QA approval
- Cash buffer status vs. 15% minimum

**Decision Logic:**
```
IF PORTFOLIO.md fresh (<=1 day old):
  portfolio_status = CURRENT
  proceed to STEP 2
  
ELSE:
  portfolio_status = STALE
  escalate to Master-Architect
  request: Approval to proceed with caveat OR trigger refresh
```

---

## STEP 2 – PRE-VALIDATION ANALYSIS & REFRESH TIMING

### Purpose

Final validation before recommending execution. Check for any portfolio changes since QA approval. Determine if refresh is needed.

### Step 2A: Sector Allocation Update Check

**Purpose:** Verify sector allocations haven't changed since position was approved.

**Calculate:**
```
FOR each sector:
  current_allocation_live = PORTFOLIO.md[sector].allocation_percent
  approved_allocation = QAReport.portfolio_context.sectors[sector].allocation_percent
  
  IF current_allocation_live significantly higher than approved:
    flag: "Sector allocation increased since approval"
    check if new allocation + pending_position exceeds cap
```

---

### Step 2B: Aggregate Pending Trades Analysis

**Purpose:** Analyze all pending trades to detect conflicts or cash constraints.

**Calculate:**
```
pending_trades_queue = [all approved trades from QA waiting for execution]
total_pending_dollars = sum of all position sizes
portfolio_cash_current = PORTFOLIO.md.total_cash
cash_after_all_pending = portfolio_cash_current - total_pending_dollars
buffer_after_all_pending = cash_after_all_pending / portfolio_value

IF buffer_after_all_pending >= 0.15:
  buffer_status = HEALTHY
  
ELIF buffer_after_all_pending >= 0.12:
  buffer_status = MARGINAL
  flag: "Buffer approaching minimum"
  
ELSE:
  buffer_status = AT_RISK
  flag: "Buffer would breach if all pending execute"
```

---

### Step 2C: Refresh Timing for Validation Sequencing

**Purpose:** Ensure portfolio data is current before recommending sequence of multiple trades.

**Decision Logic:**

```
trading_days_elapsed = Days since PORTFOLIO.md updated
time_elapsed = Hours since PORTFOLIO.md updated (same trading day)

IF trading_days_elapsed <= 1 AND time_elapsed <= 2 hours:
  PORTFOLIO_STATE = CURRENT
  ACTION: Use PORTFOLIO.md directly, proceed to STEP 3
  
ELIF trading_days_elapsed <= 1 AND time_elapsed > 2 hours:
  PORTFOLIO_STATE = STALE_WITHIN_DAY
  ACTION: Trigger QUICK REFRESH (async, account-specific)
  
  Rationale: Multiple pending trades may have changed sector allocations.
  Market may have moved. Headroom may have shifted.
  
  Signal: Portfolio-Persona QUICK_REFRESH
  Scope: Target account OR "cash_only"
  Target duration: 5-10 minutes
  Timeout: 10 minutes maximum
  
  IF refresh completes within 10 min:
    Use fresh data for STEP 3 validation
  
  ELIF refresh pending after 10 min:
    Proceed with caveat: "Portfolio data >2 hours old"
    Note in recommendation: sector allocation may have shifted
  
  IF delay >15 min:
    Escalate to Master-Architect
    Options: Proceed with caveat, halt, wait for refresh

ELSE (trading_days_elapsed > 1):
  PORTFOLIO_STATE = TOO_STALE
  ACTION: Halt validation, trigger FULL_REFRESH
  (Existing logic unchanged)
```

**Implication:** Validation of sequential trades may trigger refresh if data aged >2 hours same day.

---

## STEP 3 – VALIDATE TRADES & PREPARE RECOMMENDATIONS (APPROVAL ORDER)

### Purpose

Validate approved trades in QA approval order against constraints. Prepare recommendation report for human execution.

### Step 3A: Prepare Validation Queue

**Input:** QAReport trades from Quality-Assurance-Engineer (Gate 8: APPROVED_FOR_EXECUTION)

**Queue Construction:**

```
pending_trades_queue = [
  Trade_1 (approval_timestamp: 1:00 PM),
  Trade_2 (approval_timestamp: 1:15 PM),
  Trade_3 (approval_timestamp: 1:30 PM),
  ...
]... # Queue already ordered by approval_timestamp
# NO resorting by size/conviction/fee (Design Decision)
# Validate in this exact sequence
```

**Rationale:**
- Approval timestamp is governance control point (v8 principle)
- QA validated aggregate at Gate 5C (approval time)
- Each trade safe individually; aggregate safe at approval
- Validation order matches approval order (clear governance)
- Simpler, clearer, more maintainable

---

### Step 3B: Initialize Validation Context

```
validation_context = {
  trades_to_validate: pending_trades_queue,
  trades_safe: [],
  trades_conditional: [],
  trades_blocked: [],
  escalations: [],
  portfolio_start: PORTFOLIO.md.current(),
  validation_start_time: NOW(),
  buffer_start: calculate_buffer(PORTFOLIO.md)
}
```

---

### Step 3C: Sequential Validation Loop

```python
FOR each trade in pending_trades_queue:
  
  print(f"Validating trade: {trade.ticker} ${trade.size_dollars}")
  
  # Validate trade against constraints
  TRY:
    recommendation = validate_and_prepare_recommendation(
      ticker=trade.ticker,
      dollars=trade.size_dollars,
      account=trade.account,
      portfolio_state=PORTFOLIO.md
    )
    
  EXCEPT:
    # Validation error - flag and escalate
    trade.status = "VALIDATION_FAILED"
    trade.recommendation = "BLOCKED"
    escalations.append(trade)
    CONTINUE to next trade
  
  # Recalculate buffer impact
  HYPOTHETICAL_CASH = PORTFOLIO.md.total_cash - trade.size_dollars
  HYPOTHETICAL_BUFFER_PCT = HYPOTHETICAL_CASH / PORTFOLIO.md.total_value
  
  # Evaluate buffer impact
  IF HYPOTHETICAL_BUFFER_PCT >= 0.15:
    # Buffer intact - safe to execute
    trade.recommendation = "SAFE_TO_EXECUTE"
    trade.buffer_after = HYPOTHETICAL_BUFFER_PCT
    validation_context.trades_safe.append(trade)
    CONTINUE to next trade
  
  ELIF HYPOTHETICAL_BUFFER_PCT >= 0.12:
    # Buffer marginal - conditional recommendation
    trade.recommendation = "CONDITIONAL"
    trade.caveat = "Buffer marginal after execution"
    trade.buffer_after = HYPOTHETICAL_BUFFER_PCT
    validation_context.trades_conditional.append(trade)
    
    # Check if NEXT trade would breach further
    IF remaining_trades_count > 0:
      next_trade = remaining_trades[0]
      hypothetical_cash_next = HYPOTHETICAL_CASH - next_trade.size_dollars
      hypothetical_buffer_next = hypothetical_cash_next / PORTFOLIO.md.total_value
      
      IF hypothetical_buffer_next < 0.12:
        # Next would breach threshold - flag sequencing issue
        trade.sequencing_warning = "Next trade would breach buffer threshold"
  
  ELSE (HYPOTHETICAL_BUFFER_PCT < 0.12):
    # Buffer breach - blocked
    trade.recommendation = "BLOCKED"
    trade.reason = "Cash buffer would breach minimum (15%)"
    trade.buffer_after = HYPOTHETICAL_BUFFER_PCT
    validation_context.trades_blocked.append(trade)
    
    escalations.append({
      trigger: "buffer_breach",
      trade: trade.ticker,
      buffer_after: HYPOTHETICAL_BUFFER_PCT,
      action: "ESCALATE_TO_MA"
    })
    
    # Subsequent trades also blocked due to sequencing
    remaining_trades.forEach(t => {
      t.recommendation = "BLOCKED_BY_SEQUENCING"
      validation_context.trades_blocked.append(t)
    })
    BREAK loop

END FOR
```

---

### Step 3D: Generate Trade Recommendation Report

**Output:** Trade Recommendation Report

**Format:**

```
TRADE VALIDATION REPORT
Timestamp: [ISO 8601 timestamp]
Portfolio State: [portfolio value, cash, buffer %]

RECOMMENDATION: SAFE_TO_EXECUTE / CONDITIONAL / BLOCKED

TRADE DETAILS:
  Ticker: NVDA
  Amount: $87,000 (12% allocation)
  Current Sector: Technology (28% allocation)
  Post-Trade Sector: Technology (40% allocation) ⚠️ EXCEEDS CAP

VALIDATION RESULTS:
  ✓ Position size within limits (≤15%)
  ✓ Cash available for purchase
  ✓ Account authorized
  ✓ Security not on restricted list
  ⚠️ Technology sector allocation would reach 40% (cap 35%)
  ✓ Cash buffer maintained at 24%

CONSTRAINT IMPACTS:
  - Sector Allocation: Would exceed Technology cap by 5%
  - Cash Buffer: 24% (healthy, >15% minimum)
  - Buying Power: Sufficient ($87K available from $260K cash)

RECOMMENDATION: CONDITIONAL

  Status: Approved for execution, but requires action on sector allocation
  
  Options:
    A) Reduce another Technology position first (e.g., TQQQ 8% → 5%)
       Then execute NVDA 12% → Net Tech impact: 40% → 40% (at cap)
    B) Accept 40% Technology allocation (exceeds cap, escalate to Master-Architect)
    C) Reduce NVDA position to 10% instead of 12%
       Then execute NVDA 10% → Net Tech impact: 38% (within cap)
  
  Human Action Required: 
    Choose option A, B, or C, then report decision back to Orchestrator
    
  Orchestrator Action After Human Decision:
    - If option A: Validate TQQQ reduction, then validate NVDA addition
    - If option B: Escalate to Master-Architect for cap exception approval
    - If option C: Validate reduced NVDA position size

EXECUTION SEQUENCE (IF APPROVED):
  1. [If needed] Execute position reduction per human choice
  2. Execute NVDA buy order on eTrade
  3. Report execution completion to Orchestrator
  4. Orchestrator logs trades and updates PORTFOLIO.md

NEXT STEPS:
  Human trader review and decision required
  Expected response time: 15-30 minutes
```

---

### Step 3E: Handle Escalations

When validation flags issues:
- Escalation #1: Sector cap breach → Escalate to Master-Architect
- Escalation #2: Buffer would breach → Escalate to Master-Architect
- Escalation #3: Constraint conflict → Escalate to Master-Architect
- Await MA decision: Approve exception, Defer, Block
- Update recommendation report per MA decision

---

### Step 3F: Publish Recommendation Report

**Route to:**
- Human trader (for execution decision)
- Master-Architect (if escalations present)
- Audit log (for compliance trail)

**Format:** JSON + human-readable summary

---

## STEP 4 – MONITOR EXECUTION (POST-HUMAN EXECUTION)

### Purpose

After human trader executes on eTrade, track execution quality.

### Process

**For each trade human reports as executed:**
- Log execution price vs. last_known_market_price at submission
- Calculate slippage = execution_price - recommendation_price (in basis points)
- Check fill_quantity vs. order_quantity (partial fills?)
- Log execution characteristics

**Escalation Trigger:**
- Slippage > 50 basis points → Log and flag
- Partial fill (< 90% of order) → Log and flag
- No fill after 15 minutes → Escalate to Master-Architect

---

## STEP 5 – SECTOR ALLOCATION VERIFICATION (POST-EXECUTION)

### Purpose

Verify sector allocations remain within 35% cap after all human executions.

### Process

**For each sector:**
```
post_trade_sector_allocation = current_allocation + sum_executed_positions
sector_cap = 0.35

IF post_trade_sector_allocation <= 0.35:
  PASS: Sector constraint maintained
  
ELSE:
  FAIL: Sector cap breached
  escalate to Master-Architect
  action: Review execution or request sector rebalance
```

---

## STEP 6 – CASH BUFFER VERIFICATION (POST-EXECUTION)

### Purpose

Verify portfolio cash buffer remains above 15% minimum after all human executions.

### Process

```
total_cash_after_executions = PORTFOLIO.md.total_cash
portfolio_value = PORTFOLIO.md.total_value
buffer_after = total_cash_after_executions / portfolio_value

IF buffer_after >= 0.15:
  PASS: Buffer maintained
  
ELIF buffer_after >= 0.12:
  CAUTION: Buffer marginal, acceptable
  Log note for monitoring
  
ELSE:
  FAIL: Buffer breached
  escalate to Master-Architect
  action: Request deposit or position reduction
```

---

## STEP 7 – UPDATE EXECUTION REPORT

### Purpose

Create comprehensive execution report for stakeholders and future reference.

### Output

**Execution Report includes:**
- Execution timestamp
- Trades executed (ticker, size, price, account)
- Trades halted (if any)
- Sector allocations post-execution (by sector)
- Cash position post-execution
- Buffer status post-execution
- Any slippage notes
- Any escalations
- Next review date

---

## STEP 8 – ROUTING & NEXT STEPS

### Purpose

Route execution report to appropriate parties. Set monitoring schedule.

### Process

**Route to:**
- Quality-Assurance (for post-trade analysis)
- Stock-Analyst (if positions halted, for re-analysis)
- Master-Architect (for any escalations)
- Client/Stakeholder (execution summary)

**Set monitoring schedule:**
- Daily portfolio monitoring (sector allocations, cash buffer)
- Weekly rebalancing review
- Stop-loss and profit target tracking
- Quarterly thesis validation

---

## ESCALATION TRIGGERS

**Escalation #1: Portfolio Data Too Stale**
- Trigger: PORTFOLIO.md > 1 trading day old at STEP 1
- Escalates to: Master-Architect
- Action: Request approval with caveat OR trigger refresh

**Escalation #2: Sector Headroom Changed**
- Trigger: Sector allocation significantly increased since approval
- Escalates to: Master-Architect
- Action: Request decision (proceed, adjust position, defer)

**Escalation #3: Buffer Would Breach**
- Trigger: Aggregate pending trades would drop buffer below 15%
- Escalates to: Master-Architect
- Action: Request approval with caveat OR adjust execution queue

**Escalation #4: Sector Cap Breach**
- Trigger: Post-trade sector allocation > 35%
- Escalates to: Master-Architect
- Action: Request clarification OR rebalance positions

**Escalation #5: Execution Failed**
- Trigger: Human reports execution failed at eTrade
- Escalates to: Master-Architect
- Action: Request alternative account, size reduction, or deferral

**Escalation #6: Validation Conflict**
- Trigger: Trade fails multiple validation checks
- Escalates to: Master-Architect
- Action: Request override decision or defer trade

---

## VERSION HISTORY

**v8.0.4 (December 15, 2025):**
- Renamed to "Trade Validation & Recommendation Engine"
- Clarified HUMAN-IN-THE-LOOP architecture throughout
- Added "Architectural Principle: HUMAN-IN-THE-LOOP" section
- Removed misleading "broker_api.execute_order(...)" language
- Changed STEP 3 pseudocode to "validate_and_prepare_recommendation(...)"
- Updated all references from "execute" to "validate and recommend"
- Added "Human Execution Mapping" with recommendation options (SAFE/CONDITIONAL/BLOCKED)
- Added compliance notes (Perplexity/eTrade constraints)
- Updated STEP 4-8 to reflect post-human-execution monitoring
- Updated error handling: "Validation error" instead of "Broker error"
- All v8.0.3 logic unchanged (backward compatible)

**v8.0.3 (December 9, 2025):**
- Enhanced STEP 2: Added Step 2C (Refresh Timing for Sequencing)
- Completely replaced STEP 3: Sequential Trade Execution
- Added Step 3A: Prepare Execution Queue
- Added Step 3B: Initialize Execution Context
- Added Step 3C: Sequential Execution Loop with detailed broker integration
- Added Step 3D: Handle Escalations
- Added Step 3E: Set Stop-Loss & Profit Targets
- Added Step 3F: Update Execution Log
- Added Step 3G: Route to STEP 4

**v8.0.2 (December 8, 2025):**
- Unified freshness standard to ≤1 trading day at STEP 1
- Added STEP 1 startup validation
- Added STEP 2 pre-execution analysis (sector check, pending trades)
- Expanded STEP 3-8 with detailed specifications
- Added escalation triggers for sector breach, buffer breach, execution failure

**v8.0.1 and earlier:**
- Initial specification with basic execution workflow

---

**End of Portfolio-Orchestrator.md v8.0.4**
