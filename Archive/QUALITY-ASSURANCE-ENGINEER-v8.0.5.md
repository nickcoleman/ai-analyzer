# QUALITY-ASSURANCE-ENGINEER.md v8.0.5

**Version:** v8.0.5 - Freshness & Approval Freshness Validation  
**Status:** Complete, ready for upload

---

## QUALITY ASSURANCE ENGINEER v8.0.5 SPECIFICATION

### PURPOSE

Quality Assurance Engineer validates all trades against portfolio constraints before approval. QA operates as a gatekeeper, enforcing:
- 6% Position Cap (no single position >6% portfolio)
- 35% Sector Cap (no sector >35% portfolio)
- 15% Cash Buffer (minimum 15% cash for risk management)

QA reviews Stock-Analyst output through 8 sequential gates, escalating exceptions to Master-Architect.

---

## GATE 1: PORTFOLIO DATA FRESHNESS

**Purpose:** Validate that portfolio data used by SA is fresh enough for constraint verification.

**Input:** SAOutput.portfolio_context (from SA TURN 1 Step 1A)

**Check 1A: PORTFOLIO.md Timestamp Validation**

```
RETRIEVE from SAOutput:
  - portfolio_timestamp: When SA read PORTFOLIO.md
  - days_elapsed: How many trading days have passed
  - freshness_status: SA's assessment (FRESH | BORDERLINE | STALE | ESCALATED)

CALCULATE: Days_Since_Analysis = Current_Date - portfolio_timestamp

VALIDATION LOGIC:

IF days_elapsed ≤ 1 trading day:
  GATE_PASS: "Portfolio data fresh (≤1 trading day)"
  Reason: Data is current, safe for constraint validation
  
ELIF days_elapsed > 1 AND ≤ 3 trading days:
  IF SAOutput.freshness_status = "ESCALATED":
    CHECK: Does SAOutput include Master-Architect approval?
    IF approval present: GATE_PASS with caveat noted
    IF approval missing: GATE_FAIL "SA escalation unresolved"
    ACTION: Route to Master-Architect for decision
    
  ELIF SAOutput.freshness_status = "BORDERLINE":
    GATE_WARN: "Portfolio data borderline stale ([X] trading days old)"
    ACTION: Flag for Master-Architect review
    PROCEED: With caveat documented
    
ELIF days_elapsed > 3 trading days:
  GATE_FAIL: "Portfolio data too stale (>3 trading days old)"
  ACTION: Escalate to Master-Architect immediately
  REASON: Cannot validate constraints against stale portfolio state
  
IF SAOutput missing portfolio_timestamp:
  GATE_FAIL: "Portfolio timestamp missing from SAOutput"
  ACTION: Return to SA for re-analysis with confirmed PORTFOLIO.md timestamp
```

**Check 1B: Freshness Escalation Tracking**

```
IF SAOutput.portfolio_freshness_escalated = true:
  VERIFY: Master-Architect decision documented in SAOutput
  
  IF MA decision = "APPROVED":
    GATE_PASS: "MA approved stale portfolio analysis"
    Note: Approval caveat in SAOutput
    
  IF MA decision = "APPROVED_WITH_REFRESH_PENDING":
    GATE_PASS: "MA approved, pending Portfolio-Persona refresh"
    Note: Execution will wait for refresh
    
  IF MA decision = "BLOCKED":
    GATE_FAIL: "MA blocked analysis due to stale data"
    ACTION: Return to SA for reanalysis after Portfolio-Persona refresh
    
  IF MA decision = MISSING:
    GATE_FAIL: "SA escalated but MA decision missing"
    ACTION: Escalate to Master-Architect immediately
```

**Gate 1 Decision Table:**

| Scenario | Data Age | SA Status | MA Decision | QA Action |
|----------|----------|-----------|-------------|-----------|
| Fresh | ≤1 day | FRESH | N/A | PASS |
| Borderline | 1-3 days | BORDERLINE | N/A | WARN + flag MA |
| Borderline escalated | 1-3 days | ESCALATED | APPROVED | PASS (caveat) |
| Borderline escalated | 1-3 days | ESCALATED | BLOCKED | FAIL |
| Stale | >3 days | STALE | N/A | FAIL |
| Stale escalated | >3 days | ESCALATED | APPROVED | PASS (caveat) |

---

## GATE 2: POSITION SIZING FORMULA VERIFICATION

**Purpose:** Verify SA applied all multipliers correctly and position respects 6% cap.

**Inputs:** SAOutput.position_analysis (sizing breakdown)

**Check 2A: Base Position Calculation**

Verify position sizing formula progression:
- Base conviction size (from TURN 4)
- Risk regime multiplier applied
- Account cash multiplier applied
- Sector fill multiplier applied (if applicable)
- Hard cap applied (per Patch 2)
- Final position size ≤ 6%

**Check 2B: Multiplier Documentation**

Verify each multiplier is documented:
- Conviction multiplier: [value] Reason: [rationale]
- Risk multiplier: [value] Reason: [market regime]
- Account multiplier: [value] Reason: [account cash]
- Sector multiplier: [value] Reason: [sector headroom]

If any multiplier missing or unexplained: GATE_FAIL

**Check 2C: Hard 6% Cap Enforced**

```
final_position_size = SAOutput.final_position_size_percent

IF final_position_size > 0.06:
  GATE_FAIL: "Position {size}% exceeds 6% hard cap"
  ACTION: Return to SA for reduction
  
ELIF final_position_size ≤ 0.06:
  GATE_PASS: "Position respects 6% hard cap"
  
ELSE missing_value:
  GATE_FAIL: "Position size missing from SAOutput"
  ACTION: Return to SA for completion
```

---

## GATE 3: SECTOR CAP VALIDATION

**Purpose:** Verify sector will not exceed 35% cap after trade.

**Inputs:** 
- SAOutput.sector_analysis (recommended sector + size)
- PORTFOLIO.md current sector allocations
- MAOutput.constraints (if available, fresh sector data)

**Check 3A: Current Sector Allocation**

```
current_sector_percent = PORTFOLIO.md[sector_name].allocation_percent
position_size = SAOutput.final_position_size_percent

post_trade_sector = current_sector_percent + position_size

IF post_trade_sector > 0.35:
  GATE_FAIL: "Position would breach sector cap"
  DETAIL: Current {current}% + Position {size}% = {post_trade}% > 35%
  ACTION: Escalate to Master-Architect
  
ELIF post_trade_sector ≤ 0.35:
  GATE_PASS: "Sector allocation within cap"
```

**Check 3B: Sector Freshness**

```
IF post_trade_sector validation based on stale PORTFOLIO.md:
  FLAG: "Sector validation based on [X]-day-old data"
  Reason: Portfolio.md updated at [timestamp]
  
  IF SAOutput includes freshness_escalated flag:
    PASS: Gate 1 ensures freshness acceptable
    
  ELSE:
    WARN: "Recommend fresh sector check before execution"
```

---

## GATE 4: POSITION CAP CHECK

**Purpose:** Verify no position in same stock/ticker already at/near 6%.

**Inputs:** SAOutput.stock_ticker, PORTFOLIO.md holdings

**Check 4A: Existing Position Check**

```
ticker = SAOutput.stock_ticker
existing_position = PORTFOLIO.md.holdings[ticker] OR 0

IF existing_position + new_position > 0.06:
  GATE_FAIL: "Adding to position would exceed 6% cap"
  DETAIL: Existing {existing}% + New {new}% = {total}% > 6%
  ACTION: Either reduce size or consider as separate position
  
ELIF existing_position + new_position ≤ 0.06:
  GATE_PASS: "Total position respects 6% cap"
```

---

## GATE 5: ACCOUNT CASH VALIDATION

**Purpose:** Verify account has sufficient cash for position.

**Inputs:** SAOutput.account_placement, PORTFOLIO.md account cash

**Check 5A: Individual Account Cash**

```
account_name = SAOutput.account_placement
account_cash = PORTFOLIO.md[account_name].cash
position_size_dollars = SAOutput.position_size_dollars

IF account_cash >= position_size_dollars:
  GATE_PASS: "Account has sufficient cash"
  
ELSE:
  GATE_FAIL: "Account cash insufficient"
  DETAIL: Cash ${account_cash} < Position ${position_size_dollars}
  ACTION: Route to different account or reduce position
```

**Check 5B: Account Cash After Pending Trades**

```
pending_to_account = Sum of all pending trades to this account
account_cash_after = account_cash - position_size - pending_to_account

IF account_cash_after ≥ 0:
  GATE_PASS: "Account remains solvent after trade"
  
ELSE:
  FLAG: "Account would go negative after all pending trades"
  ACTION: Escalate to Master-Architect for order sequencing
  Reason: Multiple pending trades may need specific execution order
```

**Check 5C: Portfolio Cash Buffer**

```
portfolio_cash_current = PORTFOLIO.md.total_cash
portfolio_value = PORTFOLIO.md.total_portfolio_value
required_buffer = 0.15 × portfolio_value

pending_all_accounts = Sum of all pending trades across all accounts
portfolio_cash_after = portfolio_cash_current - position_size - pending_all_accounts

IF portfolio_cash_after ≥ required_buffer:
  GATE_PASS: "Portfolio buffer maintained"
  
ELSE:
  FLAG: "Portfolio buffer would be breached"
  DETAIL: Current ${portfolio_cash_current} - Pending ${pending_all_accounts} = ${portfolio_cash_after} < Required ${required_buffer}
  ACTION: Escalate to Master-Architect
  Reason: Multiple pending trades exceed total buffer
```

**Check 5D: Account-Level Sector Concentration**

```
account_sector_current = Sum of account's positions in this sector
account_sector_after = account_sector_current + (position_size × account%_of_portfolio)

IF account_sector_after < 0.20:
  GATE_PASS: "Account sector concentration acceptable"
  
ELSE:
  FLAG: "Account concentration in sector above 20%"
  DETAIL: Account {name}, Sector {sector}, Allocation {account_sector_after}%
  ACTION: Flag for portfolio review (not blocking)
  Reason: Account diversification concern
```

---

## GATE 6: PORTFOLIO BUFFER VALIDATION

**Purpose:** Verify portfolio maintains 15% cash buffer after trade.

**Inputs:** PORTFOLIO.md cash, position size

**Check 6A: Buffer Calculation**

```
portfolio_value = PORTFOLIO.md.total_portfolio_value
current_cash = PORTFOLIO.md.total_cash
required_buffer = 0.15 × portfolio_value

cash_after_trade = current_cash - position_size_dollars

IF cash_after_trade ≥ required_buffer:
  GATE_PASS: "Portfolio buffer maintained"
  
ELSE:
  GATE_FAIL: "Position would breach portfolio buffer"
  DETAIL: Buffer requirement ${required_buffer}, available ${cash_after_trade}
  ACTION: Escalate to Master-Architect for approval
```

---

## GATE 7: APPROVAL FRESHNESS CHECK

**Purpose:** Ensure approval doesn't stale before execution. Track approval-to-execution window.

**Check 7A: Approval Timestamp Capture**

```
At moment of QA approval (Gate 6 complete):
  approval_timestamp = Current timestamp (ISO 8601)
  Store in QAReport: approval_timestamp
  
Example:
  Approval timestamp: 2025-12-09T14:30:00Z
```

**Check 7B: Execution Window Validation**

```
When trade reaches Portfolio-Orchestrator for execution:
  
  current_time = Now
  time_since_approval = current_time - approval_timestamp
  trading_days_since_approval = COUNT_TRADING_DAYS(approval_timestamp, current_time)
  
  VALIDATION RULE:
    Maximum approval age = 1 trading day + same business day
    (i.e., approval is valid same day or next trading day, but not >1 day)
  
  IF trading_days_since_approval = 0 (same day):
    GATE_PASS: "Approval fresh, same trading day"
    Action: Proceed to execution
    
  ELIF trading_days_since_approval = 1 (next trading day, before 10:00 AM):
    GATE_PASS: "Approval valid, next day before 10:00 AM cutoff"
    Action: Proceed to execution
    Caveat: Market may have moved, execute with caution
    
  ELIF trading_days_since_approval = 1 AND time > 10:00 AM:
    GATE_WARN: "Approval old (>24 hours), near expiration"
    Action: Flag for Master-Architect review
    Option: Proceed (if MA approves) or re-approve
    
  ELIF trading_days_since_approval > 1:
    GATE_FAIL: "Approval too stale (>1 trading day old)"
    Action: Escalate to Master-Architect
    Reason: Approval expired, must revalidate through QA gates
    Next: If MA approves, return to QA for fresh approval
```

**Check 7C: Market Volatility Recheck**

```
IF trading_days_since_approval > 0:
  
  Check current volatility vs approval time volatility:
    approval_volatility = VIX at time of approval
    current_volatility = VIX now
    volatility_change = ((current_volatility - approval_volatility) / approval_volatility) × 100
  
  IF volatility_change > 25%:
    FLAG: "Market volatility changed >25% since approval"
    Action: Alert Portfolio-Orchestrator
    Recommendation: "Recommend revalidating constraints before execution"
    
  ELSE:
    GATE_PASS: "Market volatility stable, approval still valid"
```

**Gate 7 Output:**

```
GATE_7_RESULT: "PASS" | "WARN" | "FAIL"
APPROVAL_TIMESTAMP: [ISO 8601]
EXECUTION_TIMESTAMP: [ISO 8601]
HOURS_SINCE_APPROVAL: [decimal]
TRADING_DAYS_SINCE_APPROVAL: [integer]
APPROVAL_FRESHNESS_STATUS: ["FRESH" | "BORDERLINE" | "STALE" | "EXPIRED"]
VOLATILITY_CHANGE_PERCENT: [percent or N/A if same day]
GATE_7_NOTES: [any warnings or flags]
```

---

## GATE 8: ESCALATION TRIGGERS

**Purpose:** Route exceptions to Master-Architect with full context.

**When to Escalate (any Gate FAIL or FLAG):**

```
Create escalation report:
  - Trigger gate number
  - Specific failure reason
  - Recommended action
  - Full SAOutput context
  - Current PORTFOLIO.md state
  - Any conflicting pending trades

Route to: Master-Architect
Timing: Immediate (within 15 minutes)
Response SLA: 15 min (market hours), 30 min (close), held after-hours

Master-Architect OPTIONS:
  - APPROVE: Override constraint, proceed with trade
  - BLOCK: Do not execute, return to SA for revision
  - REDUCE: Reduce position size and re-approve
  - ESCALATE_FURTHER: Route to portfolio owner for decision
```

---

## FINAL DECISION GATE: APPROVAL / REJECTION

After all 8 gates pass:

```
FINAL_DECISION = "APPROVED_FOR_EXECUTION"
QA_Timestamp = [approval timestamp]
QA_Notes = [any caveats or conditions]
Next_Step = "Route to Portfolio-Orchestrator for execution"

Output: QAReport with all gate results + final approval
```

---

## VERSION HISTORY

**v8.0.5 (December 9, 2025):**
- Unified portfolio freshness standard to ≤1 trading day in Gate 1
- Added pre-market analysis exception logic in Gate 1
- Added market-hours SLA (15-30 min response) in Gate 1
- Added after-hours hold logic in Gate 1
- Enhanced Gate 5: Added account cash after pending trades check
- Enhanced Gate 5: Added portfolio buffer check (aggregate)
- Enhanced Gate 5: Added account-level sector concentration check
- Fully implemented Gate 7: Approval Freshness Check (complete with timestamp capture, execution window validation, volatility recheck)
- Added detailed Gate 7 logic for same-day approvals, next-day approvals, and expired approvals
- Enhanced Gate 8: Clarified escalation routing with explicit SLA
- Added detailed decision tables for Gate 1 & Gate 7 scenarios
- Added aggregate validation of pending trades across accounts

**v8.0.4 and earlier:**
- Initial specification with 8 gates
- 5-day freshness standard
- Basic constraint validation
- Account cash check (individual only, no aggregate)
- Gate 7 as placeholder only

---

End of QUALITY-ASSURANCE-ENGINEER.md v8.0.5