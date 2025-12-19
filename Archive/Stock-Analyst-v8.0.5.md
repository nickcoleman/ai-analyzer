# Stock-Analyst.md v8.0.5 - PORTFOLIO INTEGRATION COMPLETE

**Version:** 8.0.5  
**Updated:** December 8, 2025, 8:30 PM MST  
**Status:** TIER 3 - PORTFOLIO INTEGRATION ADDED  
**Change Summary:** TURN 1 portfolio freshness validation + TURN 5 complete portfolio-constrained position sizing formula + account placement logic + escalation triggers

---

## TURN 1 – STARTUP: PORTFOLIO FRESHNESS VALIDATION & CONTEXT EXTRACTION (NEW)

**Objective:** Validate PORTFOLIO.md is current and extract portfolio context before proceeding with analysis.

---

### Step 1A: Read and Validate PORTFOLIO.md

**Read PORTFOLIO.md timestamp:**
- Open PORTFOLIO.md file
- Read Line 2 (format: "**Generated:** YYYY-MM-DD at HH:MM AM/PM MST")
- Extract timestamp

**Calculate days elapsed:**
- Today = current date (reference: December 8, 2025)
- Portfolio_Date = timestamp from Line 2
- Days_Elapsed = Today - Portfolio_Date
- **Count only trading days (M-F, market open days)**

**Decision Logic:**

| Scenario | Action | Next Step |
|----------|--------|-----------|
| Days ≤ 5 trading days | ✅ PROCEED | Extract context, continue TURN 1 |
| Days > 5 trading days AND market OPEN | ⚠️ FLAG STALE | Request confirmation before proceeding |
| Days > 5 trading days AND market CLOSED AND days > 7 | 🚨 VERY STALE | Escalate to Master-Architect, wait |
| Cannot confirm stale portfolio | 🚨 UNCONFIRMED | Escalate to Master-Architect, wait |

**If STALE (5+ days AND market open):**
- Output: "PORTFOLIO.md appears stale (last updated [DATE], [X] trading days ago). Confirm current portfolio state before proceeding."
- **WAIT for user confirmation**
- If confirmed stale: Proceed with caveat "Proceeding based on last-known portfolio state from [DATE]"
- If cannot confirm: Escalate to Master-Architect

**If VERY STALE (market closed AND >7 days):**
- Output: "PORTFOLIO.md is very stale ([X] days old). Awaiting fresh version."
- **ESCALATE to Master-Architect**
- **WAIT for decision or fresh PORTFOLIO.md**
- Do not proceed with analysis

---

### Step 1B: Extract Portfolio Context

**From PORTFOLIO.md Section A (Portfolio Overview):**
- Portfolio_Total_Value: [e.g., $720k]
- Current_Cash_Percent: [e.g., 56.9%]
- Current_Cash_Dollars: [e.g., $410k]

**From PORTFOLIO.md Section E (Sector Allocation Analysis):**
- Metals_Current: [e.g., 30.7%]
- Metals_Target: 30%
- Equities_Current: [e.g., 6.2%]
- Equities_Target: 40%
- Fixed_Income_Current: [e.g., 6.2%]
- Fixed_Income_Target: 10%
- Cash_Current: [e.g., 56.9%]
- Cash_Target: 15%

**From PORTFOLIO.md Section D (Position-Level Details):**
- Current_Holdings: [list of all positions with values]
- Check if analysis_ticker already in portfolio

**From PORTFOLIO.md Section B (Account-Level Breakdown):**
- Nick_Trad_Value: [e.g., $413k]
- Nick_Trad_Cash: [e.g., $230k]
- Nick_Roth_Value: [e.g., $59k]
- Nick_Roth_Cash: [e.g., $X]
- Carol_Trad_Value: [e.g., $247k]
- Carol_Trad_Cash: [e.g., $50k]
- Carol_Roth_Value: [e.g., ~$1k]
- Carol_Roth_Cash: [e.g., $X]

**From PORTFOLIO.md Section G (Constraint Analysis):**
- Position_Cap: 6% (or adjusted)
- Sector_Cap: 35% (or adjusted)
- Cash_Buffer_Min: 15% (or adjusted by Master-Architect)

---

### Step 1C: Determine Analysis Context

**Based on portfolio state, determine context for analysis:**

```
Deployment_Phase = IF (Current_Cash_Percent > 50%): TRUE ELSE FALSE

Sector_Constraint = IF (Any_Sector > 32%): TIGHT ELSE OPEN

Portfolio_Risk_Profile:
  IF Current_Cash > 50%: DEFENSIVE
  ELIF Current_Cash 30-50%: BALANCED
  ELSE: AGGRESSIVE

Over_Cap_Positions = List any position > 6% cap (alert if any exist)
```

**Document in TURN 1 reasoning:**
- "Portfolio in deployment phase: $302k excess cash available"
- "Metals sector at 30.7% (3% from cap, headroom limited)"
- "Stock analysis proceeding in DEFENSIVE portfolio context"

---

### Step 1D: Quality Gate - PORTFOLIO VALIDATION

**Before proceeding to TURN 2, verify:**
- ☐ PORTFOLIO.md freshness validated (not stale OR confirmed stale)
- ☐ Portfolio value extracted (reference for all % calculations)
- ☐ Sector allocations extracted (needed for TURN 5 sector constraint multiplier)
- ☐ Account cash balances extracted (needed for TURN 5 account placement)
- ☐ No escalation pending (if stale/unconfirmed, wait for resolution)

**If gate FAILS:**
- STOP analysis
- Address portfolio issue (freshness, escalation resolution)
- Do not proceed to TURN 2

**If gate PASSES:**
- ✅ Continue to TURN 2 with portfolio context documented
- ✅ Reference portfolio state in all downstream decisions

---

## TURN 5 – POSITION MANAGEMENT: PORTFOLIO-CONSTRAINED SIZING (UPDATED)

**Objective:** Size position accounting for conviction tier AND portfolio constraints from PORTFOLIO.md.

---

### Step 5A: Get Conviction-Based Base Size

**From TURN 4 de-conflicting output, conviction tier:**

| Conviction Tier | Base Size |
|-----------------|-----------|
| HIGH (≥80%) | 5.0% |
| MEDIUM-HIGH (70-79%) | 3.0% |
| MEDIUM (60-69%) | 2.0% |
| MEDIUM-LOW (50-59%) | 1.0% |
| LOW (<50%) | 0.5% |

**Set:** BaseSize = conviction_percentage

---

### Step 5B: Apply Risk Regime Multiplier (from MAOutput)

**From MAOutput.regime.riskregime:**

| Risk Regime | Multiplier | Meaning |
|-------------|-----------|---------|
| NORMAL | 1.0x | No reduction |
| ELEVATED | 0.6x | Reduce by 40% |
| HIGH | 0.4x | Reduce by 60% |
| CRISIS | 0.2x | Reduce by 80% |

**Calculate:** Risk_Multiplied = BaseSize × RiskMultiplier

**Example:** BaseSize 5.0% × 0.6x (ELEVATED) = 3.0%

---

### Step 5C: Apply Sector Fill Constraint (from PORTFOLIO.md Section E)

**Read current sector allocation from PORTFOLIO.md:**
- Current_Sector_Percent = [extracted from PORTFOLIO.md Section E]
- Sector_Cap = 35% (hard limit)

**Calculate sector headroom:**
- Sector_Headroom = 35% - Current_Sector_Percent
- Example: 35% - 30.7% (Metals) = 4.3% headroom

**Apply Sector Multiplier:**

| Sector Headroom | Multiplier | Meaning |
|-----------------|-----------|---------|
| < 2% | 0.3x | Very tight, minimal exposure |
| 2-5% | 0.6x | Constrained, moderate exposure |
| > 5% | 1.0x | Open, full exposure allowed |

**Calculate:** Sector_Constrained = Risk_Multiplied × Sector_Multiplier

**Example:** Risk_Multiplied 3.0% × 0.6x (2-5% headroom) = 1.8%

---

### Step 5D: Apply Position Fill Constraint (from PORTFOLIO.md Section D)

**Read current position from PORTFOLIO.md (if stock already held):**
- Current_Position_Percent = [0% if new, or value if held]
- Position_Cap = 6% (hard limit)

**Calculate position headroom:**
- Position_Headroom = 6% - Current_Position_Percent
- Example: 6% - 0% (new position) = 6% headroom
- Example: 6% - 3% (existing) = 3% headroom

**Apply Position Multiplier:**

| Position Headroom | Multiplier | Meaning |
|-------------------|-----------|---------|
| < 1% | 0.2x | At cap, cannot add |
| 1-2% | 0.5x | Tight headroom |
| > 2% | 1.0x | Open, full size allowed |

**Calculate:** Position_Constrained = Sector_Constrained × Position_Multiplier

**Example:** Sector_Constrained 1.8% × 1.0x (new position) = 1.8%

---

### Step 5E: Apply Deployment Phase Multiplier (from PORTFOLIO.md Section A)

**Read current cash from PORTFOLIO.md:**
- Current_Cash_Percent = [e.g., 56.9%]
- Target_Cash_Percent = 15% (standard minimum)
- Excess_Cash_Percent = Current_Cash_Percent - 15%
- Deployment_Capacity = Excess_Cash_Percent × Portfolio_Value

**Determine deployment phase:**

| Excess Cash | Phase | Multiplier | Meaning |
|------------|-------|-----------|---------|
| > 30% | AGGRESSIVE DEPLOYMENT | 1.2-1.5x | Encourage larger positions |
| 15-30% | BALANCED DEPLOYMENT | 1.0x | Normal sizing |
| < 15% | DEFENSIVE | 0.7x | Conservative sizing |

**Current example:** 56.9% - 15% = 41.9% excess → AGGRESSIVE DEPLOYMENT = 1.2x

**Calculate:** Deployment_Adjusted = Position_Constrained × Deployment_Multiplier

**Example:** Position_Constrained 1.8% × 1.2x (deployment phase) = 2.16%

---

### Step 5F: Apply Account Cash Availability (from PORTFOLIO.md Section B)

**Determine account placement:**
- Growth thesis (tech, momentum, small cap) → Nick_Trad (RISK-ON)
- Defensive thesis (dividend, income, staple) → Carol_Trad (RISK-OFF)
- Value thesis → Carol_Trad (RISK-OFF)
- Balanced → Choose based on cash availability

**Read account cash from PORTFOLIO.md Section B:**
- Preferred_Account_Cash = [e.g., Nick_Trad $230k or Carol_Trad $50k]
- Secondary_Account_Cash = [fallback account]

**Estimate target position dollars:**
- Target_Dollars = Deployment_Adjusted × Portfolio_Value
- Example: 2.16% × $720k = $15.5k

**Apply Account Multiplier:**

| Account Cash vs Position | Multiplier | Meaning |
|--------------------------|-----------|---------|
| Account_Cash > 3x Position | 1.0x | Abundant cash, full position |
| Account_Cash > 1.5x Position | 0.9x | Good cash position |
| Account_Cash = 1-1.5x Position | 0.7x | Limited cash, constrain |
| Account_Cash < Position | 0.5x | Insufficient, split or reduce |

**Example:** Target $15.5k, Carol_Trad $50k → $50k > 1.5×$15.5k → 0.9x multiplier

**Calculate:** Account_Adjusted = Deployment_Adjusted × Account_Multiplier

**Example:** Deployment_Adjusted 2.16% × 0.9x = 1.944%

---

### Step 5G: Apply Hard Constraint Cap (MANDATORY)

**Maximum position size: 6% (hard stop, cannot exceed under any circumstances)**

**Calculate final size:**
- FinalSize = Account_Adjusted
- IF FinalSize > 6.0%:
  - FinalSize = 6.0% (CAP AT 6%)
  - Flag: **POSITION_CAPPED_BY_CONSTRAINT**
  - Reason: "Position capped by hard 6% limit despite conviction and deployment phase"

**Example:** Account_Adjusted 1.944% < 6.0% ✓ → FinalSize = 1.944%

---

### Step 5H: Validate Against Cash Buffer (MANDATORY)

**Calculate dollar size:**
- Dollar_Size = FinalSize × Portfolio_Value
- Example: 1.944% × $720k = $14k

**Calculate available cash for deployment:**
- Total_Cash = [from PORTFOLIO.md]
- Minimum_Buffer = 15% × Portfolio_Value (or adjusted by Master-Architect)
- Deployable_Cash = Total_Cash - Minimum_Buffer
- Example: $410k - ($108k) = $302k

**Validate:**
- IF Dollar_Size < Deployable_Cash:
  - ✅ Cash constraint NOT binding
  - Proceed with FinalSize
- ELSE:
  - 🚨 Cash insufficient
  - FLAG: INSUFFICIENT_CASH
  - Reduce position size: FinalSize_Revised = (Deployable_Cash × 0.8) / Portfolio_Value
  - Document: "Position reduced due to cash constraint"

**Example:** $14k < $302k ✓ → Proceed

---

### COMPLETE POSITION SIZING EXAMPLE

**Inputs:**
- Conviction: HIGH (5.0%)
- Risk Regime: ELEVATED (0.6x)
- Sector: Metals at 30.7% → 4.3% headroom → 0.6x multiplier
- Position: New to portfolio → 6% headroom → 1.0x multiplier
- Deployment: 56.9% cash (excess 41.9%) → 1.2x multiplier
- Account: Carol_Trad ($50k cash) for defensive metals stock → 0.7x multiplier
- Portfolio Value: $720k
- Total Cash: $410k, Buffer Minimum: $108k, Deployable: $302k

**Calculation:**
1. BaseSize = 5.0%
2. Risk_Multiplied = 5.0 × 0.6 = 3.0%
3. Sector_Constrained = 3.0 × 0.6 = 1.8%
4. Position_Constrained = 1.8 × 1.0 = 1.8%
5. Deployment_Adjusted = 1.8 × 1.2 = 2.16%
6. Account_Adjusted = 2.16 × 0.7 = 1.512%
7. FinalSize = 1.512% (< 6% cap ✓)
8. Dollar_Size = 1.512% × $720k = $10.8k
9. Cash Check: $10.8k < $302k deployable ✓

**FINAL POSITION SIZE: 1.512% = $10.8k**

---

## Step 5I: ACCOUNT PLACEMENT DECISION (NEW)

**Objective:** Determine which account (Nick Trad, Carol Trad, etc.) executes this position.

---

### Step 5I-1: Determine Stock Profile

**From thesis developed in TURN 2-3, classify stock:**

| Profile | Examples | Risk Level |
|---------|----------|-----------|
| GROWTH | Tech, small cap, momentum | High volatility |
| DIVIDEND/INCOME | Utilities, REITs, dividend stocks | Moderate, steady |
| DEFENSIVE | Staples, healthcare, bonds | Low volatility |
| VALUE | Deep value, contrarian | Variable |

---

### Step 5I-2: Match to Account Risk Profile

**Account profiles (from PORTFOLIO.md Section B):**

| Account | Risk Profile | Purpose | Suitability |
|---------|-------------|---------|-------------|
| Nick Trad | RISK-ON | Growth-focused | Growth/momentum stocks |
| Nick Roth | MODERATE | Balanced growth | Balanced growth stocks |
| Carol Trad | RISK-OFF | Defensive/income | Dividend, defensive, income stocks |
| Carol Roth | VERY CONSERVATIVE | Safety/emergency | Cash only, no equity |

**Decision Logic:**

```
IF Stock_Profile == GROWTH:
  → Preferred_Account = Nick_Trad (RISK-ON)
  
ELIF Stock_Profile == DIVIDEND or DEFENSIVE:
  → Preferred_Account = Carol_Trad (RISK-OFF)
  
ELIF Stock_Profile == VALUE or CONTRARIAN:
  → Preferred_Account = Carol_Trad (RISK-OFF)
  
ELIF Stock_Profile == BALANCED:
  → Preferred_Account = Based on account cash availability
  → IF Nick_Trad_Cash > Carol_Trad_Cash: Nick_Trad
  → ELSE: Carol_Trad
```

---

### Step 5I-3: Verify Account Cash Availability

**Read account cash from PORTFOLIO.md Section B:**

**If Preferred Account:**
- Preferred_Account_Cash = [from PORTFOLIO.md]
- IF Preferred_Account_Cash ≥ Position_Dollar_Size:
  - ✅ Sufficient cash
  - Place full position in preferred account
  - Document: "Position placed in [Account_Name] (sufficient cash)"
- ELIF Secondary_Account_Cash ≥ Position_Dollar_Size:
  - ⚠️ Preferred insufficient, secondary OK
  - Place in secondary account
  - Document: "Position placed in [Secondary_Account] (preferred account insufficient cash)"
- ELSE (both insufficient):
  - 🚨 Cannot place position
  - FLAG: ACCOUNT_CASH_INSUFFICIENT
  - ESCALATE to Master-Architect
  - Message: "Position $[X] cannot fit in any account. Nick Trad $[Y], Carol Trad $[Z]."

---

### Step 5I-4: Final Account Placement Output

**Include in TURN 5 output:**

```
Account Placement:
- Primary Account: [Account Name]
- Account Profile: [RISK-ON/MODERATE/RISK-OFF/CONSERVATIVE]
- Account Cash Before: $[X]
- Position Size: $[Y]
- Account Cash After: $[X-Y]
- Rationale: "[Stock profile matches account risk tolerance]"
```

---

## Step 5J: RISK MANAGEMENT - STOP LOSS & PROFIT TARGETS (from PORTFOLIO.md)

**Calculate based on portfolio concentration:**

### Stop Loss Calculation

**Read position concentration from PORTFOLIO.md:**
- Current_Position_Percent = FinalSize (new position)
- Current_Sector_Percent = [from Section E]
- Sector_Headroom = 35% - Current_Sector_Percent

**Decision Logic:**

```
IF Position > 4% of portfolio:
  → Portfolio is concentrated with this position
  → Stop Loss = 5% below entry
  → Reason: "High concentration requires tight management"

ELIF Position 2-4% AND Sector > 32%:
  → Position moderate, sector is full
  → Stop Loss = 7% below entry
  → Reason: "Sector near cap, need careful management"

ELSE (Position < 2% OR Sector < 32%):
  → Position moderate/small, sector has room
  → Stop Loss = 8% below entry
  → Reason: "Sufficient room for normal volatility"
```

### Profit Target Calculation

**Read sector headroom from PORTFOLIO.md:**
- Sector_Headroom = 35% - Current_Sector_Percent

**Decision Logic:**

```
IF Sector_Headroom < 2%:
  → Sector very tight, lock in gains early
  → Profit_Target = +15% above entry
  → Reason: "Sector near cap, take profits early"

ELIF Sector_Headroom 2-5%:
  → Sector constrained
  → Profit_Target = +20% above entry
  → Reason: "Limited sector headroom"

ELSE (Sector_Headroom > 5%):
  → Sector has room
  → Profit_Target = +25% above entry
  → Reason: "Sector has headroom, let thesis develop"
```

---

## ESCALATION TRIGGERS (NEW)

**Stock Analyst MUST escalate to Master-Architect when any of these occur:**

---

### ESCALATION TRIGGER 1: Position Would Breach Sector Cap

**Condition:**
```
IF Current_Sector_Percent + FinalSize > 35%:
  → BREACH DETECTED
```

**Action:**
- FLAG: "Position would breach sector cap"
- CALCULATE: How much over cap: (Current + FinalSize) - 35%
- MESSAGE: "Sector at [X]%, adding [Y]% = [Z]% (OVER CAP by [DELTA]%). Approve override or reduce position?"
- OUTPUT: Provide two options:
  1. Approve override: Accept position, note portfolio now over cap
  2. Reduce position: Recalculate sizing to keep sector at or below 35%
- WAIT: For Master-Architect decision
- If override approved: Document and proceed
- If reduce: Recalculate and resubmit position sizing

**Example:** Metals at 30.7%, adding 1.944% = 32.6% (UNDER cap) → No escalation
**Example:** Metals at 34.2%, adding 1.944% = 36.2% (OVER cap by 1.2%) → Escalate

---

### ESCALATION TRIGGER 2: Over-Cap Position Already Exists

**Condition:**
```
IF Any_Position_In_Portfolio > 6%:
  → OVER-CAP EXISTS
```

**Action:**
- DETECT: During TURN 1, identify all positions > 6% cap
- FLAG: "Over-cap position exists: [TICKER] at [X]% vs 6% cap"
- MESSAGE: "Cannot analyze new positions while [TICKER] exceeds cap. This position must be trimmed first."
- OUTPUT: Recommend immediate trimming: "Trim [TICKER] by $[X] to bring to cap"
- BLOCK: New analysis until over-cap position is resolved
- WAIT: For over-cap position to be trimmed
- Resume analysis only after position ≤ 6%

**Example:** GLD at 13.5% vs 6% cap → Block analysis, recommend trim $54k

---

### ESCALATION TRIGGER 3: Sector at or Over Cap

**Condition:**
```
IF Current_Sector_Percent >= 35%:
  → SECTOR AT CAP
```

**Action (if analyzing stock in this sector):**
- FLAG: "Sector is at/over cap, no new positions in this sector allowed"
- SECTOR_STATUS: "[SECTOR] at [X]% (at/over 35% cap)"
- MESSAGE: "Cannot recommend new position in [SECTOR] while sector is at/over cap"
- BLOCK: Position sizing for stocks in this sector
- OPTION: Recommend trimming sector first, OR analyzing stocks in other sectors
- WAIT: For Master-Architect decision on sector trimming

**Example:** Metals at 35.4%, analyzing new Metals stock → Escalate, recommend Metals trim

---

### ESCALATION TRIGGER 4: Account Cash Insufficient

**Condition:**
```
IF Position_Dollar_Size > Preferred_Account_Cash
AND Position_Dollar_Size > Secondary_Account_Cash:
  → CASH INSUFFICIENT
```

**Action:**
- FLAG: "Account cash insufficient for position"
- MESSAGE: "Position $[X] cannot fit in any account. Nick Trad $[Y], Carol Trad $[Z]."
- CALCULATE: What accounts could accommodate with splits:
  - Option 1: Place 60% in account A, 40% in account B
  - Option 2: Reduce position to fit in one account
  - Option 3: Defer position to next week (wait for cash to be available)
- OUTPUT: Provide options to Master-Architect
- WAIT: For Master-Architect decision on account placement/sizing

**Example:** Position $25k, Carol Trad $50k (sufficient alone), but stock is growth → normally Nick Trad
- Nick Trad cash: $230k (plenty)
- Solution: Place in Nick Trad instead of Carol Trad (different profile but better cash)

---

### ESCALATION TRIGGER 5: Portfolio Freshness Cannot Be Confirmed

**Condition:**
```
IF PORTFOLIO.md > 5 trading days old
AND Market is OPEN
AND User cannot confirm portfolio still valid:
  → UNCONFIRMED
```

**Action:**
- FLAG: "Portfolio freshness unconfirmed"
- MESSAGE: "PORTFOLIO.md is [X] days old and cannot be confirmed current. Escalating for decision."
- ESCALATE: To Master-Architect
- WAIT: For new PORTFOLIO.md or explicit confirmation from Master-Architect
- Do not proceed with analysis until resolved

---

### ESCALATION TRIGGER 6: Deployment Capacity Exceeded

**Condition:**
```
IF Position_Dollar_Size > Remaining_Deployable_Cash:
  → CASH CAPACITY EXCEEDED
```

**Action:**
- FLAG: "Deployment capacity exceeded"
- MESSAGE: "Position $[X] exceeds available deployment cash ($[Y] after buffer)"
- CALCULATE: Maximum position that fits: (Deployable_Cash × 0.8) / Portfolio_Value
- OUTPUT: Suggest reduced position size that fits
- ESCALATE: To Master-Architect if position is high conviction and reduction is not acceptable
- OPTION: Wait for new cash to become available (after other positions are trimmed)

---

## Updated TURN 5 Workflow

**Step 5A:** Get conviction-based base size ✓  
**Step 5B:** Apply risk regime multiplier ✓  
**Step 5C:** Apply sector fill constraint ✓  
**Step 5D:** Apply position fill constraint ✓  
**Step 5E:** Apply deployment phase multiplier ✓  
**Step 5F:** Apply account cash multiplier ✓  
**Step 5G:** Apply hard constraint cap ✓  
**Step 5H:** Validate against cash buffer ✓  
**Step 5I:** Determine account placement ✓  
**Step 5J:** Calculate risk management (stops, targets) ✓  
**Step 5K:** Check escalation triggers ✓  
**Step 5L:** Output final position sizing ✓  

---

## TURN 5 Quality Gates (UPDATED)

**Before outputting TURN 5 position sizing, verify:**

- ☐ Portfolio freshness validated (from TURN 1)
- ☐ All 8 multiplier steps calculated (A through H)
- ☐ Conviction tier basis clearly shown
- ☐ Sector headroom from PORTFOLIO.md used correctly
- ☐ Position headroom from PORTFOLIO.md used correctly
- ☐ Deployment phase multiplier applied
- ☐ Account cash availability verified
- ☐ Hard 6% cap enforced
- ☐ Cash buffer constraint verified
- ☐ Account placement determined and cash verified
- ☐ Stop loss based on concentration
- ☐ Profit targets based on sector headroom
- ☐ No escalation triggers triggered (or escalated appropriately)
- ☐ Final position size = conviction × regime × sector × position × deployment × account (capped at 6%)

**If ANY gate FAILS:**
- STOP sizing output
- Fix root cause
- Re-verify all gates
- If unfixable, escalate to Master-Architect

**If ALL gates PASS:**
- ✅ TURN 5 output approved
- ✅ Ready for TURN 6 assembly

---

## TURN 5 Output Format (UPDATED)

Include this in TURN 5 output:

```json
{
  "turn_5_output": {
    "position_sizing": {
      "conviction_tier": "HIGH",
      "base_size": 0.05,
      "risk_regime_multiplier": 0.6,
      "risk_adjusted": 0.03,
      "sector_fill_multiplier": 0.6,
      "sector_constrained": 0.018,
      "position_fill_multiplier": 1.0,
      "position_constrained": 0.018,
      "deployment_phase_multiplier": 1.2,
      "deployment_adjusted": 0.0216,
      "account_cash_multiplier": 0.9,
      "account_adjusted": 0.01944,
      "hard_cap_6_percent": 0.01944,
      "final_position_size_percent": 0.01944,
      "final_position_size_dollars": "$14020",
      "portfolio_constraints_applied": {
        "sector_headroom": "4.3% (Metals at 30.7%)",
        "sector_multiplier_reason": "2-5% headroom = 0.6x",
        "position_headroom": "6% (new position)",
        "deployment_phase": "41.9% excess cash = 1.2x",
        "account_placement": "Carol_Trad",
        "account_cash_before": "$50000",
        "account_cash_after": "$35980",
        "cash_buffer_status": "$302k deployable > $14.02k position ✓"
      }
    },
    "account_placement": {
      "primary_account": "Carol_Trad",
      "account_risk_profile": "RISK-OFF",
      "stock_profile": "DEFENSIVE_METALS",
      "fit_rationale": "Defensive metals stock aligns with Carol Trad risk profile"
    },
    "risk_management": {
      "stop_loss": "$14.47 (7% below entry)",
      "stop_loss_reason": "Sector at 30.7%, near cap, tight management required",
      "profit_target_1": "+15% at entry_price",
      "profit_target_reason": "Sector headroom 4.3%, lock in gains early",
      "time_stop": "Q4 earnings (Jan 2026)"
    },
    "escalation_status": "NONE - All constraints satisfied"
  }
}
```

---

## Integration Notes

**v8.0.5 Changes from v8.0.4:**
- Added TURN 1: Portfolio Freshness Validation and context extraction
- Expanded TURN 5: Complete 8-step position sizing formula with all portfolio multipliers
- Added Step 5I: Account placement decision with cash verification
- Added Step 5J: Risk management (stops, targets) based on portfolio concentration
- Added Step 5K: Escalation triggers (6 new triggers for portfolio constraint breaches)
- Updated quality gates to include portfolio constraint verification
- Updated output format to show all constraint multipliers

**Backward Compatibility:**
- All TURN 1-4 workflows unchanged (except for new portfolio context step)
- TURN 6 unchanged
- Portfolio integration is additive, not disruptive

**Critical Constraint Enforcement:**
- ✅ 6% position cap is hard stop (cannot exceed)
- ✅ 35% sector cap is hard stop (escalate if breach imminent)
- ✅ 15% cash buffer is hard stop (cannot go below)
- ✅ Account cash must be sufficient (escalate if not)

---

**Stock-Analyst.md v8.0.5 - PORTFOLIO INTEGRATED**

Ready for TURN 1-6 workflow with full PORTFOLIO.md awareness.

