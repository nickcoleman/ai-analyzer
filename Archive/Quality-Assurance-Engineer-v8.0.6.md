# Quality-Assurance-Engineer.md v8.0.6-FIXED

**Version:** v8.0.6-FIXED - With Schema Validation Enforcement  
**Status:** Complete, ready for upload  
**Last Updated:** December 10, 2025, 8:29 AM MST

---

## OVERVIEW

Quality-Assurance-Engineer is responsible for validating all position proposals before execution. QA operates as an 8-gate approval/rejection system with escalation points. All inputs flow from Stock-Analyst output (SAOutput), and all approvals route to Portfolio-Orchestrator for execution.

**CHANGE in v8.0.6-FIXED:** GATE 6 now enforces mandatory Schema Validation to prevent incomplete output bypass.

---

## GATE 1 – PORTFOLIO DATA FRESHNESS

### Purpose

Validate that the portfolio data used in analysis is still fresh enough to inform approval decision.

### Process

**Check:**
- SAOutput.portfolio_timestamp vs. current_time
- Days elapsed since portfolio data reference point
- If fresh (≤1 trading day) → PASS, proceed to GATE 2
- If stale (>1 day) → Escalate to Master-Architect

**Logic:**
```
days_elapsed = current_time - SAOutput.portfolio_timestamp

IF days_elapsed <= 1 trading day:
  GATE1_RESULT = PASS
  proceed to GATE 2
  
ELSE:
  GATE1_RESULT = ESCALATE
  escalate to Master-Architect
  ask: "Approve analysis with stale portfolio OR halt"
```

---

## GATE 2 – STOCK EXISTENCE & UNIQUENESS

### Purpose

Verify the stock recommendation is real, valid, and not a duplicate submission.

### Process

**Checks:**
- Ticker exists in financial markets (not fabricated)
- Sector classification matches known taxonomy
- Check SAOutput.pending_trades_queue for duplicates
  - If same ticker submitted twice same day → REJECT duplicate
  - Note reason: "Duplicate position same day"
- Verify stock is not on restricted/prohibited list

**Logic:**
```
IF ticker_exists AND sector_valid:
  GATE2_RESULT = PASS
  proceed to GATE 3
  
ELSE IF duplicate_submission:
  GATE2_RESULT = FAIL
  action: REJECT duplicate submission
  escalate to Master-Architect
  
ELSE:
  GATE2_RESULT = FAIL
  action: REJECT invalid ticker/sector
```

---

## GATE 3 – SECTOR & PORTFOLIO CONSTRAINTS

### Purpose

Validate individual position respects sector allocation cap and validates sequential headroom when multiple positions target same sector.

### Check 3A: Current Sector Allocation

**Purpose:** Confirm sector hasn't already breached 35% cap.

**Logic:**
```
current_sector_allocation = PORTFOLIO.md[sector].allocation_percent
sector_cap = 0.35

IF current_sector_allocation >= sector_cap:
  GATE_FAIL: "Sector at cap, cannot add positions"
  escalate to Master-Architect
  
ELSE:
  validation passes for this check
```

---

### Check 3B: Sequential Headroom Validation (PATCH 2)

**Purpose:** When multiple positions target same sector, validate each position respected its available headroom.

**When Triggered:** If pending_trades_list contains 2+ trades to same sector.

**Validation Logic:**

```
IF sector_has_multiple_pending_positions:
  
  FOR each position in sector_pending (in chronological order):
    
    Calculate headroom available to this position:
      headroom_at_this_position = sector_cap - (current_allocation + sum_earlier_positions)
    
    Validate: position_size <= headroom_at_this_position
    
    IF position_size > headroom_at_this_position:
      GATE_FAIL: "Position violates sequential headroom constraint"
      Detail: Position {i} size {size}% exceeds available headroom {headroom}%
      
    ELSE:
      Validation passes for this position
  
  FINAL: Sum all positions, verify aggregate <= sector_cap
    aggregate_sector = current_allocation + sum_all_positions
    IF aggregate_sector > 0.35:
      GATE_FAIL: "Aggregate positions exceed sector cap"
    ELSE:
      GATE_PASS: "Sequential headroom and aggregate valid"
```

**Example:**

```
Metals: 30.7% current, 4.3% headroom
Position A: 3.5% (approved 1:00 PM)
  Headroom at A: 4.3%
  A size 3.5% <= 4.3% ✓

Position B: 0.8% (approved 1:15 PM)
  Headroom at B: 4.3% - 3.5% = 0.8%
  B size 0.8% <= 0.8% ✓

Aggregate: 30.7% + 3.5% + 0.8% = 35.0% ✓ (at cap, within limit)

GATE_PASS: "Sequential positions valid"
```

**Decision:** If all validations pass, GATE 3 PASSES. If any fail, GATE 3 FAILS and escalates.

---

## GATE 4 – CONVICTION & POSITION SIZING

### Purpose

Validate conviction level is justified and position size matches conviction tier.

### Process

**Checks:**
- Conviction level (HIGH/MEDIUM/LOW) supported by thesis evidence
- Base position size (1-5%) aligns with conviction tier
- All multipliers (risk regime, account cash, sector headroom) correctly applied
- Final position size ≤ 6%
- Final position size ≤ sector headroom

**Logic:**
```
IF conviction_matches_evidence AND position_aligns_conviction:
  GATE4_RESULT = PASS
  proceed to GATE 5
  
ELSE:
  GATE4_RESULT = FAIL
  action: REJECT position, suggest revision
  escalate to Stock-Analyst for re-sizing
```

---

## GATE 5 – ACCOUNT & PORTFOLIO CASH VALIDATION (PATCH 4)

### Purpose

Three-level cash check: account-level, then aggregate portfolio-level, with escalation on breach.

### Gate 5A: Individual Account Has Cash (MANDATORY, HARD FAIL)

**Purpose:** Confirm account has immediate cash for position.

**Logic:**
```
account_cash = PORTFOLIO.md[target_account].cash
position_size_dollars = SAOutput.final_position_size_dollars

IF account_cash >= position_size_dollars:
  GATE_PASS
  Proceed to Gate 5B
  
ELSE:
  GATE_FAIL: "Account cash insufficient"
  Action: HARD FAIL, escalate to Master-Architect
  Cannot proceed - no individual account has cash
```

---

### Gate 5B: Account Working Buffer (MEDIUM PRIORITY, FLAG IF LOW)

**Purpose:** Confirm account working buffer adequate after pending trades.

**Logic:**
```
account_cash_after_pending = account_cash - position_size - sum_other_pending_to_account
account_working_buffer = account_cash_after_pending / account_value
minimum_account_buffer = 0.10 (10% - softer than portfolio 15%)

IF account_cash_after_pending / account_value >= 0.10:
  GATE_PASS
  Proceed to Gate 5C
  
ELSE:
  GATE_WARN: "Account working buffer low"
  Action: NON-BLOCKING flag, proceed with note
  Output note: "Account {name} working buffer only {X}% after pending"
  Proceed to Gate 5C (caution flag)
```

---

### Gate 5C: Portfolio Aggregate Buffer (HIGH PRIORITY, ESCALATE IF BREACH)

**Purpose:** Confirm total pending trades don't collectively breach portfolio buffer.

**Logic:**
```
total_pending_all_accounts = sum of all pending trades all accounts
portfolio_cash_after_all = total_cash - position_size - total_pending_all
portfolio_buffer_after = portfolio_cash_after_all / portfolio_value
minimum_portfolio_buffer = 0.15 (15% hard constraint)

IF portfolio_buffer_after >= portfolio_value * 0.15:
  GATE_PASS
  Proceed to Gate 5D
  
ELIF portfolio_buffer_after >= portfolio_value * 0.12:
  GATE_CAUTION: "Portfolio buffer marginal (12-15%)"
  Action: PROCEED with caution, note in output
  Output: "Buffer marginal, acceptable but monitor"
  Proceed to Gate 5D
  
ELSE:
  GATE_ESCALATE: "Portfolio buffer breach imminent"
  Action: ESCALATE TO MASTER-ARCHITECT
  Message: "Portfolio buffer {X}% below 15% minimum after pending"
  Stop further gate checks - await MA decision
```

---

### Gate 5D: Account-Level Sector Concentration (LOW PRIORITY, INFO FLAG)

**Purpose:** Warn if account getting concentrated in one sector.

**Logic:**
```
account_sector_current = sum of account positions in this sector
account_sector_after = account_sector_current + position_value
account_total_value = total positions in account
account_sector_pct = account_sector_after / account_total_value

IF account_sector_pct <= 0.20 (20%):
  GATE_PASS
  Proceed to Gate 5E
  
ELSE:
  GATE_FLAG: "Account concentration in {sector} above 20%"
  Action: NON-BLOCKING, informational flag
  Output: "Note: Growth Account Tech concentration at {X}%, consider diversification"
  Proceed to Gate 5E
```

---

### Gate 5E: Position-to-Account Impact (LOW PRIORITY, INFO ONLY)

**Purpose:** Show position impact on account as percentage.

**Logic:**
```
account_value = total portfolio value in account
position_size_dollars = position size in dollars
position_as_pct_of_account = position_size_dollars / account_value

IF position_as_pct_of_account <= 0.25 (25%):
  GATE_PASS
  
ELSE:
  GATE_FLAG: "Position is large relative to account"
  Detail: "{X}% of {account} value"
  Action: INFORMATIONAL note for execution
```

---

### Gate 5 Sequential Flow Logic

**Process:**

```
GATE 5A: Individual Account Cash
  IF account_cash < position_size:
    RESULT = FAIL
    Escalate immediately, stop remaining gates
    
  ELSE:
    Continue to Gate 5B

GATE 5B: Account Working Buffer
  IF account_buffer < 10%:
    FLAG (non-blocking)
    Continue to Gate 5C
    
  ELSE:
    Continue to Gate 5C

GATE 5C: Portfolio Aggregate Buffer
  IF portfolio_buffer >= 15%:
    PASS
    Continue to Gate 5D
    
  ELIF portfolio_buffer >= 12%:
    CAUTION FLAG
    Continue to Gate 5D
    
  ELSE (portfolio_buffer < 12%):
    ESCALATE to Master-Architect
    Stop gates, await MA decision

GATE 5D: Account Sector Concentration
  IF account_sector_pct > 20%:
    FLAG (informational)
    Continue to Gate 5E
    
  ELSE:
    Continue to Gate 5E

GATE 5E: Position-to-Account Impact
  IF position_pct > 25%:
    FLAG (informational)
    GATE5_RESULT = PASS with flag
    
  ELSE:
    GATE5_RESULT = PASS

FINAL DECISION:
  If 5A FAIL → RESULT = FAIL
  If 5C ESCALATE → RESULT = ESCALATE (await MA)
  Otherwise → RESULT = PASS (with flags noted)
```

---

## GATE 6 – THESIS QUALITY & SCHEMA VALIDATION

### Purpose

Validate thesis logic AND enforce mandatory schema compliance.

### Part A: Qualitative Check (The Thesis)

**Checks:**
- Bull case clearly articulated with catalysts
- Bear case acknowledges real risks
- Risk/reward asymmetry favorable (3:1 or better for HIGH conviction)
- Conviction level justified by evidence strength

### Part B: Structural Check (The Schema) -- NEW

QA Engineer MUST validate the physical presence of these fields in `SAOutput` (per AGENT-OUTPUT-SCHEMA.md v8.0.3):

| Field Check | Requirement | Hard Stop? |
| :--- | :--- | :--- |
| **Citations** | Must exist for all numeric data | **YES** |
| **Assumption Type** | Must be defined for each driver | **YES** |
| **Conviction Calc** | Must show 100 - Penalty logic | **YES** |
| **Gate Results** | Must show status for Gates 1-8 | **YES** |

**Logic:**
```
IF Thesis_Quality == PASS AND Schema_Validation == PASS:
  GATE6_RESULT = PASS
  proceed to GATE 7
  
ELSE IF Schema_Validation == FAIL:
  GATE6_RESULT = FAIL
  Reason: "Schema Violation: Missing [Field Name]"
  Action: RETURN to Stock-Analyst (Do not pass to Orchestrator)
```

---

## GATE 7 – MASTER-ARCHITECT GUIDANCE COMPLIANCE

### Purpose

Verify position doesn't violate any standing Master-Architect directives.

### Process

**Check:**
- Any sector exclusions or restrictions (from MA directives)
- Any account restrictions or special conditions
- Any conviction caps or position size limits in effect
- Any escalations from previous trades same day

**Logic:**
```
IF position_complies_with_all_directives:
  GATE7_RESULT = PASS
  proceed to GATE 8
  
ELSE:
  GATE7_RESULT = FAIL
  action: REJECT, escalate to Master-Architect
```

---

## GATE 8 – FINAL APPROVAL

### Purpose

Final QA approval check. If all prior gates pass (or have acceptable flags), issue final approval for execution.

### Process

**Summary:**
- Gate 1: Portfolio freshness PASS
- Gate 2: Ticker valid, no duplicates PASS
- Gate 3: Sector & sequential headroom PASS
- Gate 4: Conviction & position sizing PASS
- Gate 5: Account & portfolio cash (no hard fails, escalations awaiting MA approval)
- Gate 6: Thesis quality & schema validation PASS (Fixed v8.0.6)
- Gate 7: MA guidance compliance PASS

**Decision:**
```
IF all gates PASS or have acceptable FLAGS:
  GATE8_RESULT = APPROVED_FOR_EXECUTION
  
  Create QAReport with:
    - All position details
    - Approval timestamp
    - Any flags (buffer low, sector concentrated, etc.)
    - Next action: route to Portfolio-Orchestrator
  
ELSE IF any HARD FAIL (Gate 5A, Gate 3, Gate 6 Schema):
  GATE8_RESULT = REJECTED
  
  Create QAReport with:
    - Specific gate that failed
    - Reason for failure
    - Action: return to Stock-Analyst or escalate
  
ELSE IF Gate 5C ESCALATE (buffer breach):
  GATE8_RESULT = ESCALATED
  
  Create escalation with:
    - Full buffer analysis
    - Master-Architect decision points
    - Pending MA approval
```

---

## ESCALATION TRIGGERS

**Escalation #1: Portfolio Data Stale**
- Trigger: Gate 1 failure (>1 day old)
- Escalates to: Master-Architect
- Action: Request approval with caveat OR trigger refresh

**Escalation #2: Sector At Cap**
- Trigger: Gate 3A failure (sector >= 35%)
- Escalates to: Master-Architect
- Action: Request decision (defer, alternative sector, etc.)

**Escalation #3: Conviction Insufficient**
- Trigger: Gate 4 failure
- Escalates to: Stock-Analyst
- Action: Request thesis revision or return to research

**Escalation #4: Account Cash Insufficient**
- Trigger: Gate 5A failure
- Escalates to: Master-Architect
- Action: Request account transfer, position reduction, or deferral

**Escalation #5: Portfolio Buffer Breach**
- Trigger: Gate 5C failure (<12%)
- Escalates to: Master-Architect
- Action: Request approval with risk acknowledgment, deferral, or halt

**Escalation #6: Thesis/Schema Failure (Fixed)**
- Trigger: Gate 6 failure (Quality or Schema)
- Escalates to: Stock-Analyst
- Action: Return for mandatory fix (Citations, Assumptions, etc.)

**Escalation #7: Master-Architect Directive Violation**
- Trigger: Gate 7 failure
- Escalates to: Master-Architect
- Action: Request clarification or exemption

---

## VERSION HISTORY

**v8.0.6-FIXED (December 9, 2025):**
- **CRITICAL FIX:** Enhanced GATE 6 to include Schema Validation hard stop.
- Checks for presence of Citations, Assumption Types, Conviction Calc, and Gate Results.
- Prevents incomplete SAOutput from proceeding to GATE 7/8.
- Added Escalation #6 for Thesis/Schema failure.

**v8.0.6 (December 9, 2025):**
- Enhanced GATE 3: Added Check 3B (Sequential Headroom Validation)
  - Validates positions to same sector respect sequential headroom logic
  - Validates aggregate positions don't exceed sector cap
- Completely restructured GATE 5: Account & Portfolio Cash Validation
  - Replaced single GATE 5 with hierarchy: GATE 5A-5E
  - 5A (MANDATORY): Account has cash - hard fail if no
  - 5B (MEDIUM): Account working buffer adequate - flag if low
  - 5C (HIGH): Portfolio buffer adequate - escalate if <12%
  - 5D (LOW): Account sector concentration - informational flag
  - 5E (LOW): Position-to-account impact - informational only
  - Added decision tree for gate sequencing
  - Added precedence rules (5A > 5C > 5B > 5D/5E)
  - Added detailed sequential flow logic
- Patch 2 integration: Check 3B validates sequential headroom
- Patch 4 integration: GATE 5 enforces cash hierarchy with escalation

**v8.0.5 (December 8, 2025):**
- Unified freshness standard to ≤1 trading day in GATE 1
- Expanded GATE 2-8 specifications with clear pass/fail logic
- Added per-gate escalation triggers
- Added QAReport output structure
- Clarified gate dependencies and decision flow

**v8.0.4 and earlier:**
- Initial gate specifications (not fully detailed)

---

**End of Quality-Assurance-Engineer.md v8.0.6-FIXED**