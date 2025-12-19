# PORTFOLIO-ORCHESTRATOR.md v8.0.2

**Version:** v8.0.2 - Freshness, Refresh Timing, & Stop-Loss Management  
**Status:** Complete, ready for upload

---

## PORTFOLIO-ORCHESTRATOR v8.0.2 SPECIFICATION

### PURPOSE

Portfolio-Orchestrator executes approved trades in real-time. PO is the final step before orders go to broker. PO validates:
- Current portfolio state matches approval assumptions
- Freshness of portfolio data
- Cash availability
- Constraint compliance at execution time
- PORTFOLIO.md update completion

PO workflows through 8 sequential execution steps.

---

## STEP 1: RECEIVE APPROVED TRADE

**Input:** QAReport (from Quality-Assurance-Engineer Gate 8)

**Validate:**
- Trade approved with timestamp
- All QA gates passed
- No escalations outstanding
- Account placement confirmed

**Store in Execution Context:**
```
execution_context = {
  trade_id: [unique ID],
  approval_timestamp: [ISO 8601],
  qa_gates_passed: [yes/no],
  account: [account_name],
  stock_ticker: [ticker],
  position_size_percent: [decimal],
  position_size_dollars: [currency],
  sector: [sector_name],
  conviction: [HIGH/MEDIUM/LOW],
  account_placement: [account_name]
}
```

---

## STEP 2: READ CURRENT PORTFOLIO.MD

**Purpose:** Read fresh portfolio state to validate nothing has changed since approval.

**Freshness Check:**

```
current_time = NOW()
portfolio_timestamp = PORTFOLIO.md line 2 timestamp
time_elapsed = current_time - portfolio_timestamp
trading_days_elapsed = COUNT_TRADING_DAYS(portfolio_timestamp, current_time)

IF trading_days_elapsed ≤ 1 trading day AND time_elapsed ≤ 2 hours:
  PORTFOLIO_STATE = CURRENT
  ACTION: Use PORTFOLIO.md directly for execution
  PROCEED: Execute trade immediately
  
ELIF trading_days_elapsed ≤ 1 trading day AND time_elapsed > 2 hours:
  PORTFOLIO_STATE = STALE (within same trading day)
  ACTION: Trigger QUICK REFRESH (async)
  
  Type: Account-specific refresh (target account only)
  Scope: Re-read account positions + cash, update sector allocation
  Duration: 5-10 minutes (lightweight)
  Timeout: 10 minutes
  
  Decision:
    IF Portfolio-Persona refresh completes within 10 min:
      Use fresh data for STEP 3 validation
    IF Portfolio-Persona refresh pending after 10 min:
      Proceed with caveat (2+ hour old data flagged)
    IF delay >15 min:
      Escalate to Master-Architect for decision
      Option 1: Proceed with caveat documented
      Option 2: Halt execution, wait for full refresh
  
ELIF trading_days_elapsed > 1 trading day:
  PORTFOLIO_STATE = TOO STALE
  ACTION: HALT execution
  
  Trigger FULL REFRESH:
    Type: Complete portfolio refresh (all accounts)
    Scope: Full extraction, reclassification, risk profile update
    Duration: 30-45 minutes (full cycle)
    Timeout: 45 minutes
  
  ESCALATE to Master-Architect:
    Message: "Portfolio data exceeds freshness threshold (>1 trading day old). Requesting FULL REFRESH before execution."
    Options:
      Option 1: Wait for full refresh (45 min timeout)
      Option 2: Trigger expedited refresh (best effort)
      Option 3: Cancel execution
      Option 4: Proceed with extreme caution (requires explicit MA approval)
  
  IF Portfolio-Persona refresh completes within 45 min:
    Use fresh data, retry STEP 2
  IF timeout:
    Escalate to Master-Architect again (cannot proceed safely)
```

**Portfolio-Persona Integration:**

When PO triggers Portfolio-Persona refresh:
```
Send ASYNC signal: "Portfolio refresh needed for trade execution"
Include: Account name (if account-specific), urgency level

DO NOT BLOCK: PO continues other validations in parallel
TIMEOUT: 10 min (QUICK) or 45 min (FULL)

If refresh completes: Use fresh version for STEP 3 validation
If timeout: Route decision to Master-Architect
```

**Portfolio State Output:**

```
PORTFOLIO_STATE: "CURRENT" | "STALE" | "TOO_STALE" | "REFRESH_PENDING"
PORTFOLIO_TIMESTAMP: [ISO 8601]
HOURS_ELAPSED: [decimal]
TRADING_DAYS_ELAPSED: [integer]
REFRESH_STATUS: "NOT_NEEDED" | "QUICK_REFRESH_SENT" | "FULL_REFRESH_SENT" | "REFRESH_COMPLETE"
REFRESH_DURATION_MINUTES: [integer if complete]
PORTFOLIO_CURRENT: [yes/no]
```

---

## STEP 3: FINAL PRE-EXECUTION VALIDATION

**Purpose:** Re-validate constraints against current portfolio state.

**Check 3A: Account Cash Re-validation**

```
account_cash_current = PORTFOLIO.md[account].cash (fresh read from STEP 2)
position_size_dollars = execution_context.position_size_dollars

IF account_cash_current >= position_size_dollars:
  CHECK_PASS: "Account still has sufficient cash"
  
ELSE:
  CHECK_FAIL: "Account cash insufficient (changed since approval)"
  ACTION: Escalate to Master-Architect
  DETAIL: Was approved with ${approved_cash}, now has ${account_cash_current}
  Reason: Other trades may have executed since approval
```

**Check 3B: Sector Constraint Re-validation**

```
current_sector_percent = PORTFOLIO.md[sector].allocation_percent
position_size_percent = execution_context.position_size_percent

post_trade_sector = current_sector_percent + position_size_percent

IF post_trade_sector > 0.35:
  CHECK_FAIL: "Sector would exceed cap at execution time"
  DETAIL: Current {current_sector_percent}% + Position {position_size_percent}% = {post_trade_sector}% > 35%
  ACTION: Escalate to Master-Architect
  REASON: Market moved, sector no longer has headroom
  
ELSE:
  CHECK_PASS: "Sector allocation acceptable"
```

**Check 3C: Position Hard Cap Re-validation**

```
position_size_percent = execution_context.position_size_percent

IF position_size_percent > 0.06:
  CHECK_FAIL: "Position exceeds 6% hard cap (should not happen)"
  ACTION: Halt execution, investigate SA error
  
ELSE:
  CHECK_PASS: "Position respects 6% cap"
```

**Check 3D: Portfolio Buffer Re-validation**

```
portfolio_cash_current = PORTFOLIO.md.total_cash
portfolio_value = PORTFOLIO.md.total_portfolio_value
required_buffer = 0.15 × portfolio_value

cash_after_trade = portfolio_cash_current - position_size_dollars

IF cash_after_trade >= required_buffer:
  CHECK_PASS: "Portfolio buffer maintained"
  
ELSE:
  CHECK_FAIL: "Portfolio buffer would be breached"
  ACTION: Escalate to Master-Architect
  DETAIL: Current ${portfolio_cash_current} - Position ${position_size_dollars} = ${cash_after_trade} < Required ${required_buffer}
```

**Check 3E: Final Decision**

```
If all checks pass:
  PROCEED to STEP 4 (Execute Trade)
  
If any check fails:
  ESCALATE to Master-Architect with full context
  Await MA decision before proceeding
```

---

## STEP 4: EXECUTE TRADE

**Purpose:** Submit order to broker.

**Actions:**

```
Generate order instruction:
  - Broker ID
  - Account
  - Ticker
  - Quantity (shares = dollars / current_price)
  - Order type (market | limit)
  - Price limit (if limit order)
  - Execution timing (immediate | at-close)

Submit order to broker API

Record execution:
  - Order ID (from broker)
  - Submit timestamp
  - Order status (pending | filled | partial | rejected)
```

---

## STEP 5: SET STOP-LOSS & PROFIT-TARGETS

**Purpose:** Establish risk management guardrails before trade execution. Place stop-loss orders and profit-taking levels in broker system.

**Input:** SAOutput.risk_management (from SA TURN 5 Step 5F)

### Step 5A: Stop-Loss Placement

**Retrieve from SAOutput:**
```
stop_loss_percent = SAOutput.stop_loss_percent (calculated by SA based on conviction)
stop_loss_dollars = SAOutput.stop_loss_dollars (dollar amount below entry)

Example:
  HIGH Conviction (5% position): Stop at 20% below entry = $4,230 loss on $21,150 position
  MEDIUM Conviction (3% position): Stop at 15% below entry = $1,545 loss on $12,690 position
  LOW Conviction (1.5% position): Stop at 10% below entry = $615 loss on $6,150 position
```

**Execute Stop-Loss Order:**
```
Broker API Call:
  order_type = "STOP"
  order_subtype = "STOP_LOSS"
  ticker = [stock_ticker]
  quantity = [filled_quantity from STEP 4]
  stop_price = entry_price × (1 - stop_loss_percent)
  
  Example:
    Entry price: $42.30
    Stop loss percent: 0.20
    Stop price: $42.30 × (1 - 0.20) = $33.84
  
  Transmit to broker: Place stop order
  Record: Stop order ID, timestamp, stop price
  Status: "STOP_ORDER_ACTIVE"
```

### Step 5B: Profit Target Placement

**Retrieve from SAOutput:**
```
profit_targets = SAOutput.profit_targets (array of target prices and quantities)

Example structure:
  [
    {
      "target_num": 1,
      "price_multiple": 1.33,
      "quantity_percent": 0.50,
      "description": "50% of position at +33% gain"
    },
    {
      "target_num": 2,
      "price_multiple": 2.0,
      "quantity_percent": 0.30,
      "description": "30% of position at 2x entry"
    },
    {
      "target_num": 3,
      "price_multiple": null,
      "quantity_percent": 0.20,
      "description": "20% hold without target (trail stop or hold)"
    }
  ]
```

**Execute Profit Target Orders:**
```
Broker API Call (for each target):
  order_type = "LIMIT"
  ticker = [stock_ticker]
  quantity = filled_quantity × target_quantity_percent
  limit_price = entry_price × target_price_multiple
  
  Example:
    Entry price: $42.30
    Target 1: 50% position at 1.33x = 0.5 × quantity at $56.26
    Target 2: 30% position at 2.0x = 0.3 × quantity at $84.60
    Target 3: 20% hold (no auto-sell, trail or manual)
  
  Transmit to broker: Place limit order(s)
  Record: Target order IDs, timestamps, target prices
  Status: "PROFIT_TARGETS_ACTIVE"
```

### Step 5C: Order Management Configuration

**Broker System Settings:**
```
Set stop-loss and profit target orders as:
  - IOC (Immediate or Cancel): If must fill or cancel same day
  - GTC (Good Till Cancelled): If can remain active until hit
  - GTD (Good Till Date): Set expiration (30 days typical)
  
Typical Configuration:
  - Stop-loss: GTC (stay active, protect downside indefinitely)
  - Profit target 1: GTC (stay active, take profits when hit)
  - Profit target 2: GTC (stay active, take profits when hit)
  - Profit target 3: Manual management (trail stop if available)
```

### Step 5D: Log Risk Management Orders

**Record in execution log:**
```
Risk Management Order Log:
  {
    "trade_id": [trade_id],
    "entry_timestamp": [execution_time],
    "entry_price": [actual_fill_price],
    "position_size_shares": [filled_quantity],
    "stop_order": {
      "broker_order_id": [order_id],
      "stop_price": [dollar_amount],
      "stop_percent": [percentage below entry],
      "timestamp": [when placed],
      "status": "ACTIVE"
    },
    "profit_targets": [
      {
        "target_num": 1,
        "broker_order_id": [order_id],
        "target_price": [dollar_amount],
        "quantity_to_sell": [shares],
        "timestamp": [when placed],
        "status": "ACTIVE"
      }
    ]
  }
```

### Step 5E: Risk Limits Verification

**Verify stop-loss enforces maximum loss:**
```
max_loss = position_size_dollars × stop_loss_percent

Example:
  Position: $21,150
  Stop at 20%: Maximum loss $4,230
  Check: Is $4,230 acceptable loss for this position?
  If acceptable: Proceed with stop order
  If too large: Reduce position size and restart from STEP 4
```

### Step 5F: Monitoring Readiness

**Flag for next step (STEP 6):**
```
Output to STEP 6:
  - Stop-loss order active and monitored
  - Profit targets queued for execution
  - Risk management framework in place
  - Position is "live" - can now monitor fills and adjustments
```

---

## STEP 6: MONITOR FILL & SLIPPAGE

**Purpose:** Confirm trade fills and monitor price impact.

**Poll broker for:**
```
- Order status (filled/partial/pending)
- Execution price
- Actual quantity filled
- Commission & fees
- Timestamp of fill
```

**Slippage Check:**
```
recommended_price = SAOutput.analysis_price (from day of analysis)
actual_price = broker execution price
slippage_percent = (actual_price - recommended_price) / recommended_price

Log for future patch: {
  price_variance: slippage_percent,
  commission_impact: fees_paid,
  partial_fill: (filled_quantity < requested_quantity)
}
```

---

## STEP 7: ACCOUNT & SECTOR IMPACT

**Purpose:** Calculate position's impact on account and sector.

**Calculate:**

```
position_value = filled_quantity × execution_price

account_impact:
  account_cash_updated = account_cash - position_value
  account_sector_updated = account_sector + position_value / portfolio_value
  
sector_impact:
  sector_allocation_updated = sector_current + position_value / portfolio_value
  
portfolio_impact:
  portfolio_cash_updated = portfolio_cash - position_value
  new_position_added: {
    ticker: [ticker],
    account: [account],
    value: position_value,
    sector: [sector]
  }
```

---

## STEP 8: UPDATE PORTFOLIO.MD

**Purpose:** Commit trade execution to portfolio state.

**Process:**

```
Step 8A: Prepare Update
  - Create NEW_PORTFOLIO content with updated allocations
  - Validate against schema: All required fields present
  - Generate content_hash

Step 8B: Write to File
  - Write to PORTFOLIO.md
  - Update timestamp (execution time)
  - Include: Trade ID, execution price, fill time

Step 8C: Verify Update
  - Read PORTFOLIO.md fresh (not cached)
  - Confirm position added
  - Confirm cash decreased
  - Confirm sector/account allocations updated
  - Confirm version timestamp updated

Step 8D: Log in Audit Trail
  - Record: trade_id, ticker, quantity, price, timestamp
  - Status: "EXECUTED_AND_RECORDED"
  - Backup: Keep copy of pre-execution PORTFOLIO.md
```

---

## EXECUTION ERROR HANDLING

**If order rejected by broker:**
```
Status: EXECUTION_FAILED
Action: Do not update PORTFOLIO.md
Log: Rejection reason
Escalate: To Master-Architect
Next: Determine if retry, modify, or cancel
```

**If fill partial:**
```
Status: PARTIALLY_FILLED
Action: Record actual fill quantity
Log: Partial fill details
Decision: Accept partial fill or try to fill remainder?
  Option 1: Accept partial, complete position separately
  Option 2: Cancel remainder, submit new order for balance
```

**If PORTFOLIO.md update fails:**
```
Status: WRITE_FAILED
Action: Do not consider trade complete
Log: Write failure reason
Escalate: To Master-Architect
Issue: Broker has execution, but portfolio state not updated (critical state divergence)
Recovery: Manual reconciliation needed
```

---

## VERSION HISTORY

**v8.0.2 (December 9, 2025):**
- Unified portfolio freshness standard to ≤1 trading day in STEP 2
- Added QUICK REFRESH mode (5-10 min, account-specific) in STEP 2
- Added FULL REFRESH mode (30-45 min, complete portfolio) in STEP 2
- Added 10-minute timeout for QUICK REFRESH in STEP 2
- Added 45-minute timeout for FULL REFRESH in STEP 2
- Added Portfolio-Persona integration details in STEP 2
- Added market-moves handling for same-day data (>2 hours old) in STEP 2
- Enhanced STEP 3 with all constraint re-validation checks
- Fully implemented STEP 5: Set Stop-Loss & Profit-Targets with complete logic
- Added stop-loss placement with conviction-based percentages in STEP 5A
- Added profit target placement with multiple tiers in STEP 5B
- Added broker API call specifications for STOP and LIMIT orders in STEP 5B
- Added order management configuration (GTC, GTD, IOC options) in STEP 5C
- Added comprehensive risk management order logging in STEP 5D
- Added maximum loss verification check in STEP 5E
- Added monitoring readiness flag for STEP 6 in STEP 5F
- Updated STEP 6 with placeholder for future slippage handling
- Updated STEP 8 with placeholder for future transactional write specification
- Added comprehensive error handling section

**v8.0.1 and earlier:**
- Initial specification
- Basic freshness check (≤2 hours)
- Standard trade execution
- Basic PORTFOLIO.md update

---

End of PORTFOLIO-ORCHESTRATOR.md v8.0.2