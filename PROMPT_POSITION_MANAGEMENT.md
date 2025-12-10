# PROMPT_POSITION_MANAGEMENT.md - PHASE 2 UPDATE v1.1

## Position Management: Conviction % Drives Position Size

**Version:** 1.1 (Phase 2 Update - Conviction-Tier Allocation Integrated)  
**Status:** Production Ready  
**Date:** December 1, 2025  
**PURPOSE:** Template for position sizing, entry, exit, and decision triggers driven by conviction tier

---

## CRITICAL UPDATE: CONVICTION % → POSITION SIZE % (Primary Driving Logic)

**NEW PRINCIPLE:** Position size is **directly derived** from conviction percentage from TURN 4 risk assessment.

**Old Approach:** Generic position sizing table (not conviction-driven)  
**New Approach:** Conviction %= → Look up allocation table → Position size follows

This creates systematic link: **Conviction % flows through to position sizing, risk management, and delivery gates.**

---

## CONVICTION-TIER ALLOCATION TABLE (Primary Sizing Framework)

**Use this table as PRIMARY DRIVER for position sizing:**

| Conviction % | Conviction Tier | Position Size | Entry Strategy | Profit-Taking | Risk Tolerance | Allocation Rationale |
|---|---|---|---|---|---|---|
| 70-75% | HIGH (0-1 unvalidated assumptions) | 3-5% portfolio | Aggressive build (2-3 phases, 1-2 weeks) | Scale out on strength (50% at +15-20%) | Full risk capital | High confidence; strong evidence base |
| 55-60% | MEDIUM (2-3 unvalidated assumptions) | 1.5-3% portfolio | Conservative build (3-4 phases, 2-4 weeks) | Moderate scale (50% at +10%, 25% at +20%) | Reduced risk; event-dependent | Speculative; manageable unknowns |
| <55% | LOW (4+ unvalidated assumptions) | 0.5-1.5% portfolio or SKIP | Exploratory pilot (minimal size, watch mode) | Early exit (take profit at +5-10%) | Minimal risk; thesis unproven | Too many unknowns; monitor before deciding |

**APPLICATION LOGIC:**
1. Extract conviction % from TURN 4 risk assessment
2. Find conviction % in table above
3. Use position size from table as RECOMMENDATION range
4. Apply entry strategy from table (how aggressive to build)
5. Apply profit-taking from table (when to take gains)

**EXAMPLE:**
- Thesis conviction: 68% (from TURN 4 gate results)
- Conviction tier: HIGH (68% = 70-75% range, close to ceiling)
- Recommended position size: 3-5% portfolio (use 4% as standard)
- Entry strategy: Aggressive (full position in 2-3 phases over 1-2 weeks)
- Profit-taking: Scale out 50% at +18% (mid-range of 15-20%)

**EXAMPLE (CONSERVATIVE):**
- Thesis conviction: 58% (from TURN 4: event-driven deal thesis)
- Conviction tier: MEDIUM (55-60%)
- Recommended position size: 1.5-3% portfolio (use 2% as standard)
- Entry strategy: Conservative (1/3 position now, 1/3 on pullback, 1/3 if validates further)
- Profit-taking: Scale out 50% at +10% (early exit if thesis begins to fail)

---

## TEMPLATE: POSITION MANAGEMENT (½-1 Page)

```
═══════════════════════════════════════════════════════════════════════════════
ALLOCATION & SIZING DECISION (CONVICTION % DRIVEN)
═══════════════════════════════════════════════════════════════════════════════

CONVICTION SCORE (from TURN 4): [XX]%
CONVICTION TIER: [HIGH / MEDIUM / LOW]

CURRENT POSITION: [X]% of portfolio (or 0% if new)
RECOMMENDATION: [INITIATE / ADD / HOLD / REDUCE / EXIT] [Z]% total allocation

SIZING RATIONALE:
  • Conviction Score: [XX]% → Conviction tier: [HIGH/MEDIUM/LOW]
  • Allocation from table: [3-5% / 1.5-3% / 0.5-1.5%]
  • Risk/Reward Ratio: [X]:[1] → Position size justifies risk exposure
  • Portfolio Beta Impact: [Low / Moderate / High] → Position can be [X]% max

═══════════════════════════════════════════════════════════════════════════════
ENTRY STRATEGY: How to Build the Position (Conviction-Scaled)
═══════════════════════════════════════════════════════════════════════════════

HIGH CONVICTION ENTRY (70-75%, 3-5% position, 2-3 phases):
  PHASE 1 (Immediate, 60% of allocation):
    Amount: [2.4-3%] of portfolio (if 4% total)
    Price Range: $[A]-$[B]
    Timing: THIS WEEK (high conviction justifies quick build)
    Condition: [Market condition confirmation]
    Rationale: High conviction = build main position immediately
  
  PHASE 2 (Pullback, 40% of allocation):
    Amount: [1.6-2%] of portfolio (if 4% total)
    Price: -3% to -5% pullback from Phase 1
    Timing: If pullback occurs within 2 weeks
    Rationale: Capitalize on any weakness; add if technical confirms
  
  Total Position Target: [3-5%] built in 1-2 weeks

MEDIUM CONVICTION ENTRY (55-60%, 1.5-3% position, 3-4 phases):
  PHASE 1 (Immediate, 40% of allocation):
    Amount: [0.8-1.2%] of portfolio (if 2.5% total)
    Price Range: $[A]-$[B]
    Timing: THIS WEEK
    Rationale: Medium conviction = smaller initial tranche
  
  PHASE 2 (First Pullback, 35% of allocation):
    Amount: [0.8-1%] of portfolio
    Price: -5% to -8% pullback
    Timing: If pullback occurs within 4 weeks
    Rationale: Wait for confirmation; thesis event-dependent
  
  PHASE 3 (Second Pullback/Further Confirmation, 25% of allocation):
    Amount: [0.6-0.75%] of portfolio
    Price: -8% to -12% pullback OR catalyst confirmation
    Timing: If second pullback or positive catalyst confirms, within 6 weeks
    Rationale: Conservative approach; only complete build if thesis validates
  
  Total Position Target: [1.5-3%] built over 4-6 weeks

LOW CONVICTION ENTRY (<55%, 0.5-1.5% position, exploratory only):
  PHASE 1 (Pilot Position, 100% of allocation):
    Amount: [0.5-1%] of portfolio
    Price: Market entry or small pullback -3%
    Timing: WATCH thesis; only enter when conditions align
    Rationale: Exploratory position; too many unknowns to build larger
  
  Decision Point (2-4 weeks in):
    Does thesis begin to validate? → If YES, consider adding in Phase 2
    Do assumptions remain unvalidated? → If YES, hold pilot; don't add
  
  Total Position Target: [0.5-1%] pilot position; decide on expansion after validation

**TOTAL POSITION TARGET: [XX]% per conviction tier**

═══════════════════════════════════════════════════════════════════════════════
PROFIT-TAKING STRATEGY: When to Scale Out
═══════════════════════════════════════════════════════════════════════════════

HIGH CONVICTION SCALE-OUT (70-75%, aggressive profit-taking):
  SCALE-OUT 1 (First Profit Target):
    Sell Amount: 50% of total position
    Target Price: [+15-20]% from entry
    Condition: [Technical signal confirming move, e.g., RSI >75]
    Remaining Position: 50%
  
  SCALE-OUT 2 (Second Target):
    Sell Amount: 25% of remaining position
    Target Price: [+25-30]% from entry
    Condition: [Further confirmation or bull thesis validates]
    Remaining Position: 25-30%
  
  FINAL POSITION: Hold 25-30% until bull case targets or thesis breaks

MEDIUM CONVICTION SCALE-OUT (55-60%, moderate profit-taking):
  SCALE-OUT 1:
    Sell Amount: 50% of total position
    Target Price: [+10-15]% from entry
    Condition: [Thesis validates OR technical confirmation]
    Remaining Position: 50%
  
  SCALE-OUT 2:
    Sell Amount: 25% of remaining position
    Target Price: [+20-25]% from entry
    Condition: [Event confirms (deal closes, catalyst hits)]
    Remaining Position: 25-30%

LOW CONVICTION SCALE-OUT (<55%, early exit):
  SCALE-OUT 1:
    Sell Amount: 50% of pilot position
    Target Price: [+5-10]% from entry
    Condition: [Any initial profit taken; keep powder dry]
    Remaining Position: 50%
  
  SCALE-OUT 2:
    Sell Amount: 100% of remaining position
    Target Price: [+10-15]% from entry or thesis breaks
    Condition: [Exit by this point; exploratory complete]
    Remaining Position: 0%

═══════════════════════════════════════════════════════════════════════════════
RISK MANAGEMENT: Stop Loss & Hard Limits
═══════════════════════════════════════════════════════════════════════════════

HARD STOP LOSS: $[X]
  • Represents: [XX]% loss from average entry price
  • Trigger: [Specific condition that invalidates thesis]
  • Action: EXIT ENTIRE POSITION - no exceptions
  • Rationale: If price drops to $X, thesis is broken

TIME-BASED STOP (if applicable):
  • Hold Period: [X] months/weeks
  • If not working by [DATE]: REASSESS and potentially exit
  • Rationale: [Why thesis has time limit]

THESIS-BREAKING EVENTS (Hard Stops - Exit Immediately):
  • Event 1: [e.g., "Dividend cut announced"] → EXIT 100%
  • Event 2: [e.g., "Earnings miss >20%"] → EXIT 100%
  • Event 3: [e.g., "Key executive resigns"] → EXIT 100% or reassess

═══════════════════════════════════════════════════════════════════════════════
DECISION TRIGGERS: When to Reassess Conviction & Position Size
═══════════════════════════════════════════════════════════════════════════════

🚨 TRIGGER 1: Key Catalyst Confirms (e.g., deal agreement signed)
   ├─ Original Conviction: [XX]% (event-dependent)
   ├─ Update: +8 to +12 points (validates unvalidated assumption)
   ├─ New Conviction: [XX-XX]% (may move to higher tier)
   └─ Action: ADD to position (if not at full size) OR hold

🚨 TRIGGER 2: Execution Slips (e.g., company misses quarterly guidance)
   ├─ Original Conviction: [XX]% (execution assumption validated)
   ├─ Update: -10 to -15 points (execution assumption now questionable)
   ├─ New Conviction: [XX-XX]% (may move to lower tier)
   └─ Action: REDUCE position size from [X]% to [Y]%; tighten stop loss

🚨 TRIGGER 3: Macro Headwind Appears (e.g., interest rates spike 200bps)
   ├─ Original Conviction: [XX]% (with stable rate assumption)
   ├─ Update: -12 to -18 points (macro assumption broken)
   ├─ New Conviction: [XX-XX]% (may move to exploratory tier)
   └─ Action: REDUCE position or EXIT; reassess macro environment

🚨 TRIGGER 4: Thesis Validates Beyond Expectation (multiple catalysts confirm)
   ├─ Original Conviction: [XX]%
   ├─ Update: +8 to +15 points (unknowns resolve faster)
   ├─ New Conviction: [XX-XX]% (may reach ceiling)
   └─ Action: HOLD; profits already locked from scaling out

🚨 TRIGGER 5: [Custom event specific to thesis]
   └─ [Specific metric/event to monitor]

═══════════════════════════════════════════════════════════════════════════════
PORTFOLIO INTEGRATION & MONITORING
═══════════════════════════════════════════════════════════════════════════════

DIVERSIFICATION CHECK:
  • Sector: [Sector name] - current portfolio exposure [X]%
  • New position adds: [+X]% sector exposure, total becomes [X]% (acceptable: YES/NO)
  • Correlation to existing positions: [HIGH / MEDIUM / LOW]

CONVICTION-LINKED REBALANCING:
  • Review Frequency: [Weekly / Monthly]
  • Conviction Drop Trigger: If conviction drops >10 points, reassess position size
  • Conviction Rise Opportunity: If conviction rises >10 points, consider adding (if not at full size)
  • Adjustment Rule: Position size should match current conviction tier

MONITORING CHECKLIST:
  ☐ Watch entry price levels daily (ready to buy)
  ☐ Set price alerts at entry points
  ☐ Monitor thesis drivers [driver 1, driver 2, driver 3] weekly
  ☐ Track decision triggers daily (catalyst status, macro indicators)
  ☐ Check earnings/catalyst calendar for upcoming events
  ☐ WEEKLY: Check if conviction % has changed; if so, reassess position size
  ☐ Reassess quarterly or if any catalyst occurs

```

---

## WRITING GUIDELINES FOR POSITION MANAGEMENT

### Conviction-Tier Sizing Framework (Critical Update)

**NEW:** Position size is PRIMARY OUTPUT of conviction tier, not secondary consideration.

| Conviction % | Conviction Tier | Position Size | Entry Strategy | Profit-Taking |
|---|---|---|---|---|
| 70-75% | HIGH | 3-5% | Aggressive (2-3 phases, 1-2 weeks) | Scale out on strength |
| 55-60% | MEDIUM | 1.5-3% | Conservative (3-4 phases, 2-4 weeks) | Moderate scale; wait for validation |
| <55% | LOW | 0.5-1.5% or skip | Exploratory pilot | Early exit; watch mode |

**Application:** Conviction % from TURN 4 → Look up tier → Use position size from table

### Entry Strategy Best Practices (Conviction-Scaled)

**HIGH CONVICTION (70-75%):**
- Build aggressively (2-3 phases, 1-2 weeks)
- Main position first (60% in Phase 1)
- Pullback adds secondary (40% in Phase 2)
- DO: Scale in fast; thesis is validated
- DON'T: Wait for perfection; conviction is already high

**MEDIUM CONVICTION (55-60%):**
- Build conservatively (3-4 phases, 2-4 weeks)
- Smaller initial tranche (40% in Phase 1)
- Multiple confirmation stages required
- DO: Wait for pullbacks; thesis event-dependent
- DON'T: Rush build; unknowns require validation

**LOW CONVICTION (<55%):**
- Pilot position only (0.5-1.5%)
- Watch mode for 2-4 weeks
- Only expand if thesis validates
- DO: Minimize initial risk; keep powder dry
- DON'T: Build position; too many unknowns

### Scale-Out Strategy Best Practices

**HIGH CONVICTION:** Aggressive profit-taking (take 50% at +15-20%)
- Thesis is strong; lock in gains while running
- Hold remainder for home run upside

**MEDIUM CONVICTION:** Moderate profit-taking (take 50% at +10%)
- Don't be greedy; event could fail
- Lock in gains; reduce risk as thesis uncertainty persists

**LOW CONVICTION:** Early exit (take profits at +5-10%)
- Get out on any strength
- Risk is high; don't let pilot position become portfolio drag

### Stop Loss Discipline

**GOLDEN RULE:** Every position must have a hard stop loss at entry. No exceptions.

- **Hard Stop:** Physical price point - if hit, exit 100% no questions
- **Thesis Stop:** Event that invalidates thesis (dividend cut, earnings miss)
- **Time Stop:** If thesis hasn't worked in [X] months, reassess and exit
- **Conviction Stop (NEW):** If conviction drops >10 points, reassess position size

**Communication:** Stop loss must be specific dollar amount, with rationale for why thesis breaks if hit.

### Decision Trigger Specificity

**REQUIREMENT:** Each trigger must be something you can identify in real-time, not retrospective.

**CONVICTION-LINKED TRIGGERS (NEW):**
- "If conviction drops from 70% to 55% (catalyst fails), reassess position size downward"
- "If conviction rises from 55% to 70% (event confirmed), consider adding to position"
- Track conviction % weekly; link position sizing to conviction tier

---

## QUALITY CHECKLIST FOR POSITION MANAGEMENT

### Must Pass (Hard Requirements)

- ✅ Conviction % stated clearly (from TURN 4)
- ✅ Conviction tier identified (HIGH/MEDIUM/LOW)
- ✅ Position size matches conviction tier table (no exceptions)
- ✅ Sizing rationale shows conviction → allocation link
- ✅ Entry strategy matches conviction tier (aggressive/conservative/pilot)
- ✅ Scale-out targets match conviction tier (15-20% / 10% / 5-10%)
- ✅ Hard stop loss specific dollar amount
- ✅ Thesis-breaking events specific and observable
- ✅ Decision triggers include conviction updates
- ✅ Trigger actions linked to conviction changes
- ✅ Diversification check quantified
- ✅ Monitoring checklist includes weekly conviction review

---

## INTEGRATION NOTE

This Position Management is **SECTION 4** of the Actionable Reporting output.

**Complete Report Sections:**
1. Executive Summary (1 page)
2. Technical Setup (1 page)
3. Fundamental Case (1 page)
4. Position Management (THIS SECTION - ½-1 page, conviction-driven)

**Critical Flow:**
- TURN 4 produces conviction % → Section 4 looks up position size from table → Position sizing is systematic and transparent

---

**END OF PROMPT_POSITION_MANAGEMENT.md v1.1 (Conviction-Tier Integrated)**

**Status:** Production Ready - Conviction % drives position sizing  
**Date:** December 1, 2025  
**Approval:** Master-Architect Phase 2 authorization  
**Next:** PROMPT_TURN6_ACTIONABLE.md (add Quality Control Gate Section 5)