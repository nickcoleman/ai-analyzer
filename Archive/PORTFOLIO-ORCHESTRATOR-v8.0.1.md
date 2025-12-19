# PORTFOLIO ORCHESTRATOR v8.0.1
## Trade Execution Engine with Portfolio Impact Management

**Version:** 8.0.1  
**Date Created:** December 9, 2025  
**Status:** TIER 4.2 - Complete Trade Execution Specification  
**Previous Version:** v8.0.0-alpha (incomplete)  
**Upstream Dependencies:** Stock-Analyst v8.0.5, Market-Analyst v8.0.1, QA Engineer v8.0.4

---

## EXECUTIVE SUMMARY

Portfolio Orchestrator v8.0.1 is the execution engine that:
1. Receives pre-approved trade recommendations from QA Engineer
2. Calculates real-time portfolio impact
3. Executes trades respecting all v8.0.1 constraints
4. Updates PORTFOLIO.md with trade results
5. Coordinates with Portfolio-Persona for weekly refresh
6. Handles execution errors and rollback procedures

PO acts as the **single point of trade execution**, ensuring all recommendations respect constraints before reaching live accounts. Post-execution, it initiates Portfolio-Persona update cycle to keep PORTFOLIO.md current.

---

## CORE RESPONSIBILITIES

### Pre-Execution Phase

**1. Receive Approved Trade Recommendation**
- Input: QAReport from QA Engineer (status = APPROVE)
- Verify: QAReport.portfolio_impact calculations
- Extract: Stock details, position size, account, reasoning

**2. Real-Time Portfolio State Snapshot**
- Read current PORTFOLIO.md (or latest cache)
- Verify timestamp ≤2 hours old (real-time requirement)
- If >2 hours old: Trigger Portfolio-Persona refresh before execution
- Extract: Current holdings, cash positions, sector allocations

**3. Final Pre-Execution Validation**
- Recalculate portfolio impact with current state
- Verify position still fits (no intervening trades)
- Confirm account cash still available
- Check for any new data that invalidates recommendation

**4. Execution Decision**
- Approve trade execution
- Or: Escalate if state changed materially
- Or: Request fresh analysis if >2 hours elapsed

### Execution Phase

**5. Execute Trade**
- Submit buy order for specified quantity
- Route to designated account (Nick Trad or Carol Trad)
- Set initial stop loss (from QAReport calculations)
- Set profit target (from QAReport calculations)
- Log execution timestamp

**6. Confirm Execution**
- Verify order filled
- Obtain fill price and timestamp
- Record actual fill quantity
- Capture trading costs/commissions

### Post-Execution Phase

**7. Calculate Actual Portfolio Impact**
- Actual fill price vs. recommended entry price
- Actual cost basis calculation
- Actual account cash impact
- Actual sector allocation impact
- Compare to projected (QAReport) vs. actual

**8. Update PORTFOLIO.md**
- Add new position to holdings
- Update cash positions (all accounts)
- Update sector allocations
- Update account-level positions
- Set update timestamp
- Flag Portfolio-Persona for weekly refresh

**9. Generate Execution Report**
- Trade confirmation
- Portfolio impact (before/after)
- Risk metrics updated
- Next review date
- Escalation flags (if any)

**10. Monitoring & Escalation**
- Track position against stop loss
- Monitor sector concentration
- Alert if constraints breached post-execution
- Escalate to Master-Architect if needed

---

## DATA FLOW: QA APPROVAL → EXECUTION

```
QA Engineer APPROVE
    ↓
QAReport (with portfolio_impact)
    ↓
Portfolio-Orchestrator RECEIVE
    ├─ Validate QAReport format
    ├─ Extract trade details
    └─ Quality gate check
        ↓
Read Current PORTFOLIO.md
    ├─ Verify freshness (≤2 hours)
    ├─ IF stale: Trigger Portfolio-Persona refresh
    └─ Extract current state
        ↓
Final Pre-Execution Validation
    ├─ Recalculate impact with current state
    ├─ Verify position still fits
    ├─ Confirm account cash available
    └─ Constraints still satisfied?
        ├─ YES → Proceed to execution
        ├─ NO (material change) → Escalate
        └─ MAYBE (minor change) → Log caveat, proceed
            ↓
Execute Trade
    ├─ Submit buy order
    ├─ Route to designated account
    ├─ Set stop loss
    ├─ Set profit target
    └─ Record execution
        ↓
Confirm Execution
    ├─ Verify fill
    ├─ Capture fill price
    ├─ Record commission
    └─ Obtain timestamp
        ↓
Calculate Actual Impact
    ├─ Compare projected vs. actual
    ├─ Calculate cost basis
    ├─ Update cash impact
    └─ Sector allocation impact
        ↓
Update PORTFOLIO.md
    ├─ Add position
    ├─ Update cash
    ├─ Update sectors
    ├─ Set timestamp
    └─ Flag Portfolio-Persona
        ↓
Generate Execution Report
    ├─ Confirmation
    ├─ Impact summary
    ├─ Risk metrics
    └─ Next review date
        ↓
Monitoring & Alert System
    ├─ Track stop loss
    ├─ Monitor sector concentration
    └─ Alert if breached
```

---

## STEP-BY-STEP EXECUTION WORKFLOW

### STEP 1: Receive & Validate QAReport

**Input:** QAReport from QA Engineer v8.0.4

**Validation Checks:**
```
IF QAReport.overall_status ≠ "APPROVE" THEN
  STOP: Cannot execute non-approved reports
  ACTION: Log and return to QA Engineer
  
IF QAReport missing required fields THEN
  STOP: Incomplete report
  FIELDS REQUIRED:
    - meta.timestamp
    - meta.stock_ticker
    - portfolio_impact (all fields)
    - audit_trail (qa_analyst_id, timestamp)
    - decision.approved = true
  
IF QAReport.gates contains FAIL THEN
  STOP: Gate failure recorded
  ACTION: Return to Stock-Analyst for revision
  
IF QAReport.escalation_triggers contains TRUE THEN
  STOP: Trigger fired
  ACTION: Escalate to Master-Architect
  
ELSE
  PASS: QAReport valid, extract trade details
```

**Trade Details Extraction:**
```
ticker = QAReport.stock_ticker
position_size = QAReport.portfolio_impact.position_percent_of_portfolio
position_size_dollars = portfolio_value × position_size
target_account = QAReport.account_placement.target_account or "Nick Trad"
entry_price = current_market_price (at execution time)
stop_loss_percent = QAReport.risk_management.stop_loss_percent
profit_target_percent = QAReport.risk_management.profit_target_percent
reasoning = QAReport.decision.reason
conviction_basis = QAReport.stock_analysis.conviction_tier
```

---

### STEP 2: Read Current PORTFOLIO.md

**Requirement:** Real-time portfolio state before execution

**Freshness Check:**
```
current_time = NOW()
portfolio_timestamp = PORTFOLIO.md line 2 timestamp
time_elapsed = current_time - portfolio_timestamp

IF time_elapsed > 2 hours THEN
  PORTFOLIO_STATE = STALE
  ACTION: Trigger Portfolio-Persona refresh (async)
  PROCEED: With caution, note stale data caveat
  RECOMMENDATION: Wait for Portfolio-Persona update
  
ELSE
  PORTFOLIO_STATE = CURRENT
  ACTION: Use PORTFOLIO.md directly
  
IF time_elapsed > 4 hours THEN
  STOP: Portfolio data too stale
  ACTION: Escalate to Master-Architect
  REASON: Cannot safely execute without current portfolio
```

**Extract Current State:**
```
FROM PORTFOLIO.md Section A (Portfolio Overview):
  total_portfolio_value = Float
  current_cash_percent = Float
  current_cash_dollars = Float
  
FROM PORTFOLIO.md Section E (Sector Allocation):
  FOR EACH sector:
    current_sector_percent = Float
    sector_cap = 0.35
    
FROM PORTFOLIO.md Accounts Breakdown:
  FOR EACH account (Nick Trad, Carol Trad):
    account_cash_available = Float
    current_holdings = Array of positions
    
FROM PORTFOLIO.md Current Holdings:
  FOR EACH position:
    ticker = String
    position_dollars = Float
    position_percent = Float
    account = String
```

---

### STEP 3: Final Pre-Execution Validation

**Recalculate Portfolio Impact with Current State:**

```
CALCULATE: post_trade_state with current PORTFOLIO.md values

// Sector impact
target_sector = QAReport.sector
current_sector_percent = PORTFOLIO.md[target_sector]
post_trade_sector_percent = current_sector_percent + position_size

IF post_trade_sector_percent > 0.35 THEN
  GATE_FAIL: "Sector would exceed cap with current state"
  ACTION: Escalate to Master-Architect
  DETAIL: Sector was {old_state}%, now {current_state}%, would be {post_trade}%
  REASON: Portfolio changed since QA validation

// Account cash impact
target_account = QAReport.target_account
account_cash_before = PORTFOLIO.md[target_account].cash
position_size_dollars = position_size × total_portfolio_value

IF position_size_dollars > account_cash_before THEN
  GATE_FAIL: "Account cash insufficient with current state"
  ACTION: Escalate to Master-Architect
  DETAIL: Account had {account_cash}$ available, needs {position_size_dollars}$
  REASON: Account state changed since QA validation

// Overall cash buffer
cash_before = PORTFOLIO.md.total_cash
post_trade_cash = cash_before - position_size_dollars
post_trade_cash_percent = post_trade_cash / total_portfolio_value
required_buffer = 0.15 (or risk-adjusted from MAOutput)

IF post_trade_cash_percent < required_buffer THEN
  GATE_FAIL: "Trade would violate cash buffer"
  ACTION: Escalate to Master-Architect
  DETAIL: Post-trade would be {post_trade_percent}%, need {required_buffer}%
  REASON: Portfolio state changed

ELSE
  ALL_VALIDATIONS_PASSED: Ready to execute
```

**Decision Logic:**
```
IF validation_failures > 0 THEN
  DECISION = ESCALATE
  ACTION: Send to Master-Architect with detailed delta analysis
  INCLUDE: Original QAReport + current state + delta analysis
  
ELSE IF time_elapsed > 1 hour THEN
  DECISION = PROCEED_WITH_CAVEAT
  ACTION: Execute but log warning
  CAVEAT: "Executed with 1h+ stale portfolio data; Portfolio-Persona refresh recommended"
  
ELSE
  DECISION = PROCEED
  ACTION: Execute trade immediately
```

---

### STEP 4: Execute Trade

**Pre-Trade Setup:**
```
ticker = QAReport.stock_ticker
quantity = (position_size_dollars / current_market_price) ROUNDED_DOWN
target_account = QAReport.target_account or "Nick Trad"
entry_type = "BUY"
time_in_force = "DAY" or "GTC" (per policy)
order_type = "MARKET" or "LIMIT" (with 0.5% limit band)

LOG_PRE_TRADE:
  timestamp: NOW()
  ticker: {ticker}
  quantity: {quantity}
  estimated_cost: {quantity × current_market_price}
  account: {target_account}
  stop_loss_trigger: {entry_price × (1 - stop_loss_percent)}
  profit_target: {entry_price × (1 + profit_target_percent)}
  reasoning: {conviction_basis}
```

**Execute Order:**
```
// Submit order to broker
order_result = submit_order(
  ticker: ticker,
  quantity: quantity,
  order_type: order_type,
  account: target_account,
  time_in_force: time_in_force
)

IF order_result.status = "REJECTED" THEN
  EXECUTION_FAILED: "Order rejected by broker"
  ACTION: Log failure, escalate to Master-Architect
  DETAIL: {order_result.error_message}
  NEXT: Determine if issue is temporary or permanent
  
ELSE IF order_result.status = "PENDING" THEN
  EXECUTION_PENDING: "Order submitted, waiting for fill"
  ACTION: Wait for confirmation (with timeout)
  TIMEOUT: 5 minutes max
  
ELSE IF order_result.status = "FILLED" THEN
  EXECUTION_SUCCESS: "Trade executed"
  CONTINUE to Step 5
```

**Set Risk Management Orders:**
```
// Set stop loss
stop_loss_price = fill_price × (1 - stop_loss_percent)
set_stop_loss(
  ticker: ticker,
  quantity: quantity,
  stop_price: stop_loss_price,
  account: target_account,
  condition: "TRIGGER_ON_CLOSE"
)

// Set profit target
profit_target_price = fill_price × (1 + profit_target_percent)
set_profit_target(
  ticker: ticker,
  quantity: quantity,
  limit_price: profit_target_price,
  account: target_account
)

LOG_RISK_ORDERS:
  stop_loss_set: true
  stop_price: {stop_loss_price}
  profit_target_set: true
  target_price: {profit_target_price}
```

---

### STEP 5: Confirm Execution

**Verify Fill:**
```
fill_confirmation = get_order_status(order_id)

IF fill_confirmation.status ≠ "FILLED" THEN
  ERROR: "Order not fully filled"
  ACTION: Escalate, may need manual intervention
  DETAIL: Status = {status}, filled {fill_confirmation.filled_quantity} of {quantity}

ELSE
  EXTRACT_FILL_DATA:
    fill_quantity = fill_confirmation.filled_quantity
    fill_price = fill_confirmation.average_fill_price
    fill_timestamp = fill_confirmation.fill_time
    commission = fill_confirmation.commission or calculate_commission()
    actual_cost = (fill_quantity × fill_price) + commission
```

**Record Execution:**
```
EXECUTION_RECORD:
  timestamp: {fill_timestamp}
  ticker: {ticker}
  quantity: {fill_quantity}
  fill_price: {fill_price}
  cost_basis: {actual_cost}
  commission: {commission}
  account: {target_account}
  order_id: {order_id}
  execution_status: "CONFIRMED"
  
LOG_TO_AUDIT_TRAIL:
  Original QAReport timestamp
  Execution timestamp (fill time)
  Time from approval to execution
  Fill price vs. recommended entry
  Commission paid
  Risk orders set (stop loss, target)
```

---

### STEP 6: Calculate Actual Portfolio Impact

**Compare Projected vs. Actual:**
```
// From QAReport (projected)
projected_position_percent = QAReport.position_sizing.final_size
projected_cost = projected_position_percent × PORTFOLIO.md.total_value
projected_entry_price = PORTFOLIO.md.current_price (at time of analysis)

// Actual execution
actual_quantity = fill_quantity
actual_fill_price = fill_price
actual_cost = (actual_quantity × actual_fill_price) + commission
actual_position_percent = actual_cost / PORTFOLIO.md.total_value

// Delta analysis
price_delta = actual_fill_price - projected_entry_price
cost_delta = actual_cost - projected_cost
pct_delta = (actual_position_percent - projected_position_percent) / projected_position_percent

DELTA_REPORT:
  price_impact: {price_delta} ({pct_delta}% from projected)
  cost_impact: {cost_delta}
  position_size_impact: {actual_position_percent} vs {projected_position_percent}
  status: (if deltas <2%: ACCEPTABLE, else: INVESTIGATE)
```

**Update Portfolio-Level Metrics:**
```
// Cash impact
cash_before = PORTFOLIO.md.cash
cash_after = cash_before - actual_cost
cash_percent_after = cash_after / PORTFOLIO.md.total_value

// Sector impact
sector_before = PORTFOLIO.md[sector].percent
sector_after = sector_before + actual_position_percent

// Account impact
account_cash_before = PORTFOLIO.md[target_account].cash
account_cash_after = account_cash_before - actual_cost

// Constraint check (all should pass, but verify)
IF cash_percent_after < 0.15 THEN
  ALERT: "Cash buffer constraint breached post-execution"
  ACTION: Log critical alert, escalate
  
IF sector_after > 0.35 THEN
  ALERT: "Sector cap breached post-execution"
  ACTION: Log critical alert, escalate
```

---

### STEP 7: Update PORTFOLIO.md

**Prepare Updated Portfolio:**
```
// Create new PORTFOLIO entry
NEW_PORTFOLIO = copy(PORTFOLIO.md)

// Update line 2 (timestamp)
NEW_PORTFOLIO.line_2 = "**Generated:** {TODAY} at {NOW} MST"

// Add new position to holdings
NEW_POSITION:
  ticker: {ticker}
  quantity: {fill_quantity}
  entry_price: {fill_price}
  cost_basis: {actual_cost}
  account: {target_account}
  entry_date: {fill_timestamp}
  stop_loss: {stop_loss_price}
  profit_target: {profit_target_price}
  conviction_tier: {conviction_basis}
  
NEW_PORTFOLIO.current_holdings += NEW_POSITION

// Update cash position (all accounts)
NEW_PORTFOLIO[target_account].cash = account_cash_after
NEW_PORTFOLIO.total_cash = cash_after
NEW_PORTFOLIO.cash_percent = cash_percent_after

// Update sector allocation
NEW_PORTFOLIO[sector].percent = sector_after
NEW_PORTFOLIO[sector].holdings.append(ticker)

// Update portfolio total
NEW_PORTFOLIO.total_value = PORTFOLIO.md.total_value (unchanged in same day)

// Add execution note
NEW_PORTFOLIO.notes += "Trade execution: {ticker} {fill_quantity} @ {fill_price}, {fill_timestamp}"
```

**Write Updated PORTFOLIO.md:**
```
WRITE_FILE(
  filepath: "PORTFOLIO.md",
  content: NEW_PORTFOLIO,
  commit_message: "Trade execution: {ticker} {fill_quantity} shares @ {fill_price}",
  timestamp: {fill_timestamp}
)

IF write_successful THEN
  LOG: "PORTFOLIO.md updated successfully"
  ACTION: Continue to Step 8
  
ELSE
  ERROR: "Failed to write PORTFOLIO.md"
  ACTION: Escalate to Master-Architect
  REASON: Portfolio state and execution mismatch
```

**Trigger Portfolio-Persona Refresh:**
```
// Signal Portfolio-Persona to perform weekly refresh
// (even if not the scheduled refresh day)

IF time_since_last_refresh < 6 hours THEN
  ACTION: Schedule Portfolio-Persona refresh for next cycle
  
ELSE
  ACTION: Trigger immediate Portfolio-Persona refresh
  REASON: >6 hours since last refresh, need current state validation
  
PORTFOLIO_PERSONA_SIGNAL:
  event: "TRADE_EXECUTED"
  ticker: {ticker}
  execution_timestamp: {fill_timestamp}
  position_size: {actual_position_percent}
  priority: "HIGH" (refresh soon)
```

---

### STEP 8: Generate Execution Report

**Execution Confirmation Report:**
```json
{
  "execution_report": {
    "meta": {
      "timestamp": "ISO8601 (fill time)",
      "report_generated": "ISO8601 (now)",
      "execution_status": "COMPLETED",
      "orchestrator_version": "8.0.1"
    },
    "trade_details": {
      "ticker": "String",
      "quantity": Integer,
      "fill_price": Float,
      "fill_timestamp": "ISO8601",
      "account": "String",
      "order_id": "String",
      "execution_type": "MARKET | LIMIT"
    },
    "cost_analysis": {
      "gross_cost": Float,
      "commission": Float,
      "total_cost_basis": Float,
      "position_percent_of_portfolio": Float,
      "position_dollars": Float
    },
    "price_analysis": {
      "projected_entry_price": Float,
      "actual_fill_price": Float,
      "price_delta": Float,
      "price_delta_percent": Float,
      "favorable": Boolean (if delta < -0.5% = better fill)
    },
    "portfolio_impact": {
      "cash_before": Float,
      "cash_after": Float,
      "cash_percent_before": Float,
      "cash_percent_after": Float,
      "cash_impact_dollars": Float,
      "sector_before_percent": Float,
      "sector_after_percent": Float,
      "sector_headroom_remaining": Float,
      "account_cash_before": Float,
      "account_cash_after": Float
    },
    "risk_management": {
      "stop_loss_price": Float,
      "stop_loss_percent": Float,
      "profit_target_price": Float,
      "profit_target_percent": Float,
      "risk_reward_ratio": Float
    },
    "constraints_validation": {
      "position_cap_6_percent": Boolean (passed),
      "sector_cap_35_percent": Boolean (passed),
      "cash_buffer_15_percent": Boolean (passed),
      "account_cash_sufficient": Boolean (passed),
      "all_constraints_met": Boolean
    },
    "audit_trail": {
      "qareport_timestamp": "ISO8601",
      "execution_timestamp": "ISO8601",
      "approval_to_execution_minutes": Integer,
      "portfolio_snapshot_timestamp": "ISO8601",
      "portfolio_age_minutes": Integer,
      "qa_analyst_id": "String",
      "orchestrator_id": "String"
    },
    "next_steps": {
      "position_review_date": "ISO8601 (usually 2-4 weeks)",
      "portfolio_persona_refresh": "Triggered",
      "monitoring_active": true,
      "escalation_required": Boolean,
      "escalation_reason": "String or null"
    }
  }
}
```

---

### STEP 9: Monitoring & Alert System

**Real-Time Position Monitoring:**
```
MONITORING_LOOP (continuous):

FOR EACH executed_position:
  current_price = get_current_price(ticker)
  entry_price = position.entry_price
  
  // Price movement tracking
  price_change_percent = (current_price - entry_price) / entry_price
  
  // Stop loss check
  IF current_price < position.stop_loss_price THEN
    ALERT: "Stop loss triggered"
    ACTION: Manual review (stop loss executed by broker)
    DETAIL: Current {current_price} < stop {position.stop_loss_price}
    
  // Profit target check
  ELSE IF current_price > position.profit_target_price THEN
    ALERT: "Profit target reached"
    ACTION: Consider exit (profit target active by broker)
    DETAIL: Current {current_price} > target {position.profit_target_price}
    
  // Conviction loss check (down 3x expected stop loss)
  ELSE IF price_change_percent < -(stop_loss_percent × 3) THEN
    ALERT: "Conviction loss - thesis broken"
    ACTION: Manual review, may need trimming
    DETAIL: Down {price_change_percent}% (expected max ~{stop_loss_percent}%)
    
  // Sector concentration check
  sector = position.sector
  sector_current = PORTFOLIO.md[sector].percent
  IF sector_current > 0.35 THEN
    ALERT: "Sector concentration at cap"
    ACTION: No new additions to this sector
    DETAIL: Sector at {sector_current}%, cap 35%

REPORTING:
  Daily: Price movement summaries
  Weekly: Position reviews
  On-alert: Immediate escalation
```

**Escalation Conditions:**
```
ESCALATE_TO_MASTER_ARCHITECT if:

1. Stop loss hit (position exited)
   → Analyze thesis failure
   → Review conviction basis
   
2. Conviction loss (down 3x stop loss)
   → Thesis broken unexpectedly
   → Consider position trimming
   
3. Sector hits cap (>35%)
   → Portfolio rebalancing decision
   → Trim or rotate positions
   
4. Account cash drops <15%
   → Deployment pause
   → Cash raise decision
   
5. Position diverges >10% from QA projection
   → Check if data issue or market surprise
   → Validate thesis still intact
```

---

## ERROR HANDLING & ROLLBACK

### Execution Failure Scenarios

**Scenario 1: Order Rejected by Broker**
```
IF order_result.status = "REJECTED" THEN
  EXECUTION_FAILED: true
  ACTION:
    1. Log order rejection reason
    2. Cancel any pending risk orders
    3. Return position analysis to Stock-Analyst
    4. Escalate to Master-Architect
    
  REASON_ANALYSIS:
    - Insufficient account balance? (cash constraint violated)
    - Trading halted? (can't execute)
    - Bad order format? (PO bug)
    - Broker connectivity? (temporary, retry)
    - Regulatory issue? (escalate)
    
  NEXT_STEP:
    - If temporary: Retry after fix
    - If account issue: Raise cash first
    - If broker issue: Escalate
    - If permanent: Reanalyze position
```

**Scenario 2: Partial Fill**
```
IF order_filled_quantity < order_quantity THEN
  EXECUTION_PARTIAL: true
  
  ANALYSIS:
    filled_percent = order_filled_quantity / order_quantity
    
    IF filled_percent > 0.8 THEN
      ACTION: Accept partial fill
      ADJUSTMENT: Recalculate position based on actual fill
      
    ELSE IF filled_percent > 0.5 THEN
      ACTION: Log partial fill, continue monitoring
      RESUBMIT: Remaining quantity as new order (or cancel)
      
    ELSE (filled_percent < 0.5) THEN
      ACTION: Escalate
      REASON: Significant unexecuted portion
      DECISION: Resubmit or cancel remainder
```

**Scenario 3: Portfolio State Changed Materially**
```
IF validation_in_step_3_failed THEN
  EXECUTION_BLOCKED: true
  
  REASON: Portfolio state changed between QA validation and execution
  
  EXAMPLES:
    - Another trade executed in interim (sector/cash changed)
    - Market event affected position (price gap, halt)
    - Time elapsed >4 hours (stale data risk)
    
  DECISION:
    IF change_is_minor (delta <2%) THEN
      PROCEED: With caveat in audit trail
      CAVEAT: "Executed despite minor portfolio state change"
      
    ELSE IF change_is_material (delta >2%) THEN
      ESCALATE: To Master-Architect
      REASON: Portfolio state diverged significantly
      RECOMMENDATION: Request fresh analysis from Stock-Analyst
      
    ELSE IF change_is_critical (e.g., sector now over cap) THEN
      STOP: Do not execute
      ESCALATE: Immediately
      REASON: Critical constraint would be violated
```

### Rollback Procedures

**Post-Execution Rollback (if constraints breached):**
```
IF post_trade_constraints_breached THEN
  SEVERITY_ASSESSMENT:
    IF breach_type = "MINOR" (e.g., 0.5% over cash buffer) THEN
      ACTION: Log, monitor, no rollback
      
    ELSE IF breach_type = "MAJOR" (e.g., sector exceeds cap) THEN
      ACTION: Consider position trim
      TRIGGER: Master-Architect decision
      
    ELSE IF breach_type = "CRITICAL" (e.g., hard constraint) THEN
      ACTION: Initiate rollback
      PROCEDURE:
        1. Immediately sell partial position to restore constraint
        2. Log rollback trade
        3. Update PORTFOLIO.md
        4. Escalate to Master-Architect
        5. Review what failed in validation
```

**Rollback Trade Details:**
```
ROLLBACK_TRADE:
  original_trade:
    ticker: {ticker}
    quantity: {quantity}
    fill_price: {fill_price}
    
  rollback_trade:
    ticker: {ticker}
    quantity: {rollback_quantity} (sufficient to restore constraint)
    order_type: "SELL_MARKET"
    reason: "Constraint restoration"
    
  constraint_restored:
    before: {constraint_status_after_original}
    after: {constraint_status_after_rollback}
    
  audit_note: "Rollback trade {rollback_order_id} executed to restore {constraint_name}"
```

---

## PORTFOLIO-PERSONA COORDINATION

### Sync Points

**Pre-Trade:**
```
// Get latest portfolio state from Portfolio-Persona

IF Portfolio-Persona last_refresh < 2 hours THEN
  USE: Cached PORTFOLIO.md
  STRATEGY: Execute with current data
  
ELSE
  REQUEST: Async Portfolio-Persona refresh
  WAIT: Maximum 15 minutes for refresh
  IF refresh completes: USE updated data
  IF timeout: USE stale data with caveat
  
REASON: Ensure execution sees latest portfolio state
```

**Post-Trade:**
```
// Trigger Portfolio-Persona refresh after trade

SIGNAL_EVENT:
  type: "TRADE_EXECUTED"
  ticker: {ticker}
  timestamp: {fill_timestamp}
  priority: "HIGH"
  
Portfolio-Persona RECEIVES signal and:
  1. Marks PORTFOLIO.md for immediate refresh
  2. Recalculates sector allocations
  3. Updates cash positions
  4. Validates constraints
  5. Generates weekly summary
  
TIMING: Should complete within 1-2 hours
```

**Weekly Refresh Cycle:**
```
// Portfolio-Persona runs every Sunday 8 PM MST

Standard Refresh:
  1. Extract all positions from brokers
  2. Calculate current allocations
  3. Validate constraints
  4. Update PORTFOLIO.md
  5. Flag any alerts
  
Post-Trade Refresh (same day):
  1. Expedited: Extract updated positions
  2. Calculate impact
  3. Update PORTFOLIO.md immediately
  4. Validate constraints
  5. Alert if issues found

PO USES: Latest PORTFOLIO.md from Portfolio-Persona
```

---

## EXECUTION POLICY & DECISION RULES

### When to Execute Immediately
```
IF all_conditions_met:
  ✅ QAReport status = APPROVE
  ✅ Portfolio data ≤2 hours old
  ✅ Final validation passed
  ✅ Account has sufficient cash
  ✅ Sector has headroom
  
THEN: EXECUTE IMMEDIATELY
```

### When to Wait for Portfolio-Persona Refresh
```
IF conditions_met:
  ⏳ Portfolio data 2-4 hours old
  ⏳ Portfolio-Persona refresh in progress
  
THEN: WAIT for Portfolio-Persona (max 15 min)
       IF refresh completes: EXECUTE
       IF timeout: EXECUTE with caveat
```

### When to Request Fresh Analysis
```
IF condition_exists:
  ⏸️ Portfolio data >4 hours old
  ⏸️ Material change since QA validation
  ⏸️ Market event altered assumptions
  
THEN: REQUEST fresh Stock-Analyst analysis
       DO NOT execute with stale recommendation
```

### When to Escalate Instead of Execute
```
IF condition_exists:
  🛑 Portfolio constraint would be breached
  🛑 Account cash insufficient
  🛑 Sector at/over capacity
  🛑 QAReport validation failed
  🛑 Broker order rejected
  
THEN: ESCALATE to Master-Architect
      DO NOT force execution
```

---

## AUDIT & COMPLIANCE LOGGING

Every execution is logged with complete audit trail:

```json
{
  "execution_audit": {
    "execution_id": "UUID",
    "timestamp_approved": "ISO8601 (from QAReport)",
    "timestamp_executed": "ISO8601 (fill time)",
    "approval_to_execution_duration": "ISO8601 Duration",
    "qa_engineer_id": "String",
    "portfolio_orchestrator_id": "String",
    
    "qareport_details": {
      "overall_status": "APPROVE",
      "gates_passed": 8,
      "escalation_triggers": 0,
      "portfolio_age_at_qa": "Integer minutes"
    },
    
    "execution_details": {
      "ticker": "String",
      "quantity": Integer,
      "fill_price": Float,
      "fill_timestamp": "ISO8601",
      "order_type": "MARKET | LIMIT",
      "account": "String"
    },
    
    "validation_steps": [
      "QAREPORT_RECEIVED: timestamp",
      "PORTFOLIO_READ: age minutes",
      "PRE_EXECUTION_VALIDATION: passed | failed",
      "TRADE_EXECUTED: timestamp",
      "FILL_CONFIRMED: timestamp",
      "PORTFOLIO_UPDATED: timestamp",
      "MONITORING_STARTED: timestamp"
    ],
    
    "constraints_verification": [
      "position_6_percent_cap: passed",
      "sector_35_percent_cap: passed",
      "cash_15_percent_buffer: passed",
      "account_cash_available: passed"
    ],
    
    "compliance": {
      "regulatory_checks_passed": Boolean,
      "insider_trading_check": "CLEAR",
      "concentration_warning": Boolean,
      "cash_constraint_warning": Boolean
    },
    
    "rollback_required": Boolean,
    "escalations_triggered": Integer,
    "alerts_generated": Integer
  }
}
```

---

## VERSION HISTORY

| Version | Date | Status | Changes |
|---------|------|--------|---------|
| 8.0.0-alpha | Oct 1, 2025 | Incomplete | Initial stub |
| 8.0.1 | Dec 9, 2025 | Complete | Full execution engine with pre-validation, constraint enforcement, Portfolio-Persona coordination, error handling, and rollback procedures |

---

## NEXT PHASE: IMPLEMENTATION CHECKLIST

When ready to implement v8.0.1:

- [ ] Integrate with broker APIs (order submission, fill confirmation)
- [ ] Create PORTFOLIO.md write procedures (atomic, with backup)
- [ ] Implement Portfolio-Persona signaling
- [ ] Create monitoring loop (real-time price tracking)
- [ ] Build audit logging system
- [ ] Test error scenarios (order rejection, partial fill, etc.)
- [ ] Test rollback procedures
- [ ] Create operational manual for PO execution
- [ ] Schedule portfolio refresh coordination with Portfolio-Persona

---

**PORTFOLIO ORCHESTRATOR v8.0.1 - COMPLETE EXECUTION SPECIFICATION**

Ready for TIER 4.3 (Market-Analyst + Alpha-Analyst) integration.

