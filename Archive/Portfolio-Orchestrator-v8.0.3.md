# Portfolio-Orchestrator.md v8.0.3

**Version:** v8.0.3 - Sequential Execution & Refresh Timing  
**Status:** Complete, ready for upload  
**Last Updated:** December 9, 2025, 12:40 PM MST

---

## OVERVIEW

Portfolio-Orchestrator is responsible for pre-execution validation, sequential trade execution, cash buffer monitoring, and escalation management. PO receives approved positions from Quality-Assurance and manages the entire execution workflow through STEP 1-8.

---

## STEP 1 – STARTUP: PORTFOLIO DATA VALIDATION

### Purpose

Confirm Portfolio-Persona has latest portfolio state before executing any trades.

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

## STEP 2 – PRE-EXECUTION VALIDATION & REFRESH TIMING

### Purpose

Final validation before execution. Check for any portfolio changes since QA approval. Determine if refresh is needed.

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

### Step 2C: Refresh Timing for Execution Sequencing (PATCH 3)

**Purpose:** Ensure portfolio data is current before executing sequence of multiple trades.

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
    Note in execution: sector allocation may have shifted
  
  IF delay >15 min:
    Escalate to Master-Architect
    Options: Proceed with caveat, halt, wait for refresh

ELSE (trading_days_elapsed > 1):
  PORTFOLIO_STATE = TOO_STALE
  ACTION: Halt execution, trigger FULL_REFRESH
  (Existing logic unchanged)
```

**Implication:** Sequential execution may trigger refresh if data aged >2 hours same day.

---

## STEP 3 – SEQUENTIAL TRADE EXECUTION (APPROVAL ORDER) – PATCH 3

### Purpose

Execute approved trades in QA approval order with buffer validation between executions.

### Step 3A: Prepare Execution Queue

**Input:** QAReport trades from Quality-Assurance-Engineer (Gate 8: APPROVED_FOR_EXECUTION)

**Queue Construction:**

```
pending_trades_queue = [
  Trade_1 (approval_timestamp: 1:00 PM),
  Trade_2 (approval_timestamp: 1:15 PM),
  Trade_3 (approval_timestamp: 1:30 PM),
  ...
]

# Queue already ordered by approval_timestamp
# NO resorting by size/conviction/fee (Patch 3 Design Decision)
# Execute in this exact sequence
```

**Rationale:**
- Approval timestamp is governance control point (v8 principle)
- QA validated aggregate at Gate 5C (approval time)
- Each trade safe individually; aggregate safe at approval
- Execution order matches approval order (clear governance)
- Simpler, clearer, more maintainable

---

### Step 3B: Initialize Execution Context

```
execution_context = {
  trades_to_execute: pending_trades_queue,
  trades_executed: [],
  trades_halted: [],
  escalations: [],
  portfolio_start: PORTFOLIO.md.current(),
  execution_start_time: NOW(),
  buffer_start: calculate_buffer(PORTFOLIO.md)
}
```

---

### Step 3C: Sequential Execution Loop

```python
FOR each trade in pending_trades_queue:
  
  print(f"Executing trade: {trade.ticker} ${trade.size_dollars}")
  
  # Execute order to broker
  TRY:
    order_result = broker_api.execute_order(
      ticker=trade.ticker,
      dollars=trade.size_dollars,
      account=trade.account
    )
    
    trade.execution_price = order_result.price
    trade.execution_quantity = order_result.quantity
    trade.execution_timestamp = order_result.timestamp
    
  EXCEPT:
    # Broker error - log and continue
    trade.status = "EXECUTION_FAILED"
    escalations.append(trade)
    CONTINUE to next trade
  
  # Recalculate buffer after this execution
  NEW_CASH = PORTFOLIO.md.total_cash - trade.size_dollars
  NEW_BUFFER_PCT = NEW_CASH / PORTFOLIO.md.total_value
  
  # Evaluate buffer status
  IF NEW_BUFFER_PCT >= 0.15:
    # Buffer intact
    execution_context.trades_executed.append(trade)
    CONTINUE to next trade
  
  ELIF NEW_BUFFER_PCT >= 0.12:
    # Buffer marginal (caution threshold)
    execution_context.trades_executed.append(trade)
    Log caveat: "Buffer marginal"
    
    # Check if NEXT trade would breach further
    IF remaining_trades_count > 0:
      next_trade = remaining_trades[0]
      hypothetical_cash = NEW_CASH - next_trade.size_dollars
      hypothetical_buffer = hypothetical_cash / PORTFOLIO.md.total_value
      
      IF hypothetical_buffer < 0.12:
        # Next would breach threshold
        HALT remaining trades
        ESCALATE to Master-Architect
        BREAK loop
  
  ELSE (NEW_BUFFER_PCT < 0.12):
    # Buffer breach imminent
    execution_context.trades_executed.append(trade)
    execution_context.trades_halted.extend(remaining_trades)
    
    escalations.append({
      trigger: "buffer_breach",
      buffer_after: NEW_BUFFER_PCT,
      action: "HALT remaining, ESCALATE to MA"
    })
    
    BREAK loop

END FOR
```

---

### Step 3D: Handle Escalations

When buffer breach occurs:
- HALT remaining trades
- ESCALATE to Master-Architect
- Await MA decision: Approve, Defer, Halt
- Resume execution per MA decision

---

### Step 3E: Set Stop-Loss & Profit Targets

For each executed trade:
- Place stop-loss order at broker
- Place profit target orders per SAOutput specifications
- Record orders in execution log

---

### Step 3F: Update Execution Log

Log all trades executed with:
- Execution price, quantity, timestamp
- Account, sector, conviction
- Stop-loss and profit target details
- Buffer status after execution

---

### Step 3G: Route to STEP 4

Continue workflow: STEP 4 (Monitor Fill & Slippage) → STEP 5-8

---

## STEP 4 – MONITOR FILL & SLIPPAGE

### Purpose

Track execution quality. Monitor for slippage and fill rate anomalies.

### Process

**For each trade:**
- Compare execution_price to last_known_market_price at submission
- Calculate slippage = execution_price - order_price (in basis points)
- Check fill_quantity vs. order_quantity (partial fills?)
- Log any unusual execution characteristics

**Escalation Trigger:**
- Slippage > 50 basis points → Log and flag
- Partial fill (< 90% of order) → Log and flag
- No fill after 15 minutes → Escalate to Master-Architect

---

## STEP 5 – SECTOR ALLOCATION VERIFICATION (POST-EXECUTION)

### Purpose

Verify sector allocations remain within 35% cap after all executions.

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

Verify portfolio cash buffer remains above 15% minimum after all executions.

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

**Escalation #4: Buffer Breached Post-Execution**
- Trigger: Buffer drops below 12% after trade execution
- Escalates to: Master-Architect
- Action: HALT remaining trades, await decision

**Escalation #5: Sector Cap Breach**
- Trigger: Post-trade sector allocation > 35%
- Escalates to: Master-Architect
- Action: Request clarification OR rebalance positions

**Escalation #6: Execution Failed at Broker**
- Trigger: Broker rejects order (insufficient buying power, restricted list, etc.)
- Escalates to: Master-Architect
- Action: Request alternative account, size reduction, or deferral

---

## VERSION HISTORY

**v8.0.3 (December 9, 2025):**
- Enhanced STEP 2: Added Step 2C (Refresh Timing for Sequencing)
  - Trigger QUICK_REFRESH if portfolio data >2 hours old (same trading day)
  - Ensures sector allocations current before sequencing multiple trades
- Completely replaced STEP 3: Sequential Trade Execution
  - Execute trades in QA approval order (no resorting)
  - Validate buffer after each trade execution
  - Escalate if next trade would drop buffer below 12% threshold
  - Maintains v8 governance architecture (approval order is control point)
  - Added Step 3A: Prepare Execution Queue
  - Added Step 3B: Initialize Execution Context
  - Added Step 3C: Sequential Execution Loop with detailed broker integration
  - Added Step 3D: Handle Escalations
  - Added Step 3E: Set Stop-Loss & Profit Targets
  - Added Step 3F: Update Execution Log
  - Added Step 3G: Route to STEP 4
- Implementation rationale: Approval timestamp is decision point; execution respects approval sequence

**v8.0.2 (December 8, 2025):**
- Unified freshness standard to ≤1 trading day at STEP 1
- Added STEP 1 startup validation
- Added STEP 2 pre-execution analysis (sector check, pending trades)
- Expanded STEP 3-8 with detailed specifications
- Added escalation triggers for sector breach, buffer breach, execution failure

**v8.0.1 and earlier:**
- Initial specification with basic execution workflow

---

**End of Portfolio-Orchestrator.md v8.0.3**

