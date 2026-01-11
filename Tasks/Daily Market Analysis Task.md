DAILY MARKET ANALYSIS 

**Frequency:** Weekdays**Output:** Inline Markdown report (copy and save manually) 
---

## TASK EXECUTION FLOW

```
Task Start → Auto-fetch Phase A data → Auto-fetch Phase B data → Auto-fetch static data (ISM, FOMC) → Evaluate gates → Output report
```

---

## PHASE A: PRIMARY MARKET METRICS (5 Checks)

### CHECK 1: Breadth Analysis

**Metric:** S&P 500 stocks above 50-day moving average

**Data Source:**
- Search for: "S&P 500 breadth percentage today" OR "stocks above 50-day MA"
- Check Seeking Alpha, StreetStats, or market data sites
- Record: Current % and trend vs yesterday

**Threshold:** <40% = TRIGGER
- ✅ CLEAR if ≥40%
- ⚠️ ELEVATED if 30-40%
- 🚨 TRIGGER if <30%

**Interpretation:**
- <30%: Breadth very weak, sell-off risk high
- 30-40%: Deteriorating breadth, caution warranted
- ≥40%: Breadth healthy, supports bull case

---

### CHECK 2: Retail/Consumer Indicators

**Metrics:** 
- XRT (Retail ETF) daily performance
- Consumer confidence (if released today)
- BFCM sales data (if released today)

**Data Source:**
- Finance tool: Get XRT today's % change
- Search: "consumer confidence index released today" (Conference Board)
- Search: "BFCM sales data released" (only if public announcement)

**Thresholds:**
- XRT <-2% = ELEVATED CONCERN
- XRT <-4% = TRIGGER
- Consumer confidence: Note if released, compare to prior month
- BFCM: Note if released, compare to expectations

**Interpretation:**
- XRT strong: Retail holding up, consumer confidence intact
- XRT weak: Consumer pullback, recession concern
- Consumer confidence declining: Leading indicator of slowdown

**Today's Check:**
```
XRT: [get current daily %]
Is consumer confidence released today? [Yes/No]
Is BFCM data released today? [Yes/No]
Status: [CLEAR / ELEVATED / TRIGGER]
```

---

### CHECK 3: Volatility Check

**Metric:** VIX (Volatility Index)

**Data Source:**
- Finance tool: Get VIX current level
- Record: Level, trend vs yesterday, 15-day baseline

**Thresholds:**
- <15: Market complacency (low stress)
- 15-20: Normal range (baseline)
- 20-25: Elevated volatility (caution)
- >25: High volatility (stress signal)
- >30: Panic level (immediate circuit breaker)

**Interpretation:**
- VIX <15: No stress, risk assets favored
- VIX 15-20: Normal market function
- VIX 20-25: Investors reducing risk, watch for trigger
- VIX >25: Risk-off environment, caution warranted
- VIX >30: 🚨 TIER 1 CIRCUIT BREAKER (act same-day)

**Today's Check:**
```
VIX Level: [current value]
Trend vs yesterday: [up/down/flat]
Status: [CLEAR / ELEVATED / TRIGGER]
```

---

### CHECK 4: Technical Indicators

**Metrics:**
- S&P 500 vs 50-day moving average (trend direction)
- Support/resistance levels (key technical levels)
- Market structure (higher highs/lows, or deteriorating)

**Data Source:**
- Finance tool: Get S&P 500 OHLC, plot 50-day MA
- Search: "S&P 500 technical analysis [today]" for support/resistance
- Interpret: Is market above/below 50-day MA? Trend up or down?

**Thresholds:**
- Above 50-day MA: ✅ Supports bullish view
- Below 50-day MA: ⚠️ Technical deterioration
- Breaking below key support: 🚨 Could trigger circuit breaker

**Interpretation:**
- Strong uptrend with higher highs/lows: Technically healthy
- Above 50-day MA: Short-term momentum intact
- Below 50-day MA: Momentum shifted to downside
- Breaking major support: Risk of sharp selloff

**Today's Check:**
```
S&P 500: [current level]
vs 50-day MA: [above / below / at]
Trend: [strong up / up / neutral / down / strong down]
Key Support: [level]
Key Resistance: [level]
Status: [CLEAR / ELEVATED / TRIGGER]
```

---

### CHECK 5: Sentiment Snapshot

**Metrics:**
- Fear & Greed Index (0-100 scale)
- Market sentiment direction
- Insider activity (buying or selling)

**Data Source:**
- Search for: "Fear and Greed Index today" (CNNMoney, CNBC, financial sites)
- Record: Current value (0-100), prior day value, 5-day average
- Search: "Market sentiment" or "investor sentiment today"
- Search: "insider trading activity" (optional, if available)

**Scale Interpretation:**
- 0-20: Extreme Fear (contrarian buy setup)
- 20-40: Fear (caution warranted)
- 40-60: Neutral (balanced)
- 60-80: Greed (frothy, watch for pullback)
- 80-100: Extreme Greed (peak risk, consider taking profits)

**Today's Check:**
```
Fear & Greed Index: [0-100 value]
Trend: [rising / flat / falling]
Market Sentiment: [describe in 1-2 sentences]
Status: [CLEAR / ELEVATED / TRIGGER]
```

---

## PHASE B: CRITICAL GATES (4 Gates) — AUTO-FETCH VERSION

### GATE 6: Manufacturing Recession Gate (AUTO-FETCH ISM)

**Purpose:** Detect if manufacturing recession is confirmed (ISM <50 for 9+ consecutive months)

**Release Schedule:** First business day of month, 10:00 AM ET

**Automatic Data Fetch (Run Every Task Execution):**

```
STEP 1: Fetch ISM Historical Data
  Search for: "ISM Manufacturing PMI history 2024 2025 latest"
  Sources: Trading Economics, ISM official, Investing.com
  Retrieve: Last 24 months of monthly PMI values
  Parse: Extract month/year and PMI reading for each month
  
STEP 2: Calculate Consecutive Months <50
  Algorithm:
    - Start with most recent PMI value
    - Count backwards through months
    - Count += 1 for each consecutive month with PMI <50
    - Stop counting when you find a month with PMI ≥50
    - Record final count
  
  Example output:
    "Last 12 months ISM:
     Dec 2025: pending (1st business day today?)
     Nov 2025: 48.2 (<50) ✓
     Oct 2025: 48.7 (<50) ✓
     Sep 2025: 49.1 (<50) ✓
     ...
     [Continue back]
     Mar 2025: 49.0 (<50) ✓ ← Start of current streak
     Feb 2025: 50.3 (≥50) ✗ ← Streak broke here
     
     Consecutive months <50: 9"

STEP 3: Is ISM Released Today?
  - Get today's date
  - Is today the 1st business day of the month?
    (1st business day = 1st weekday, or if 1st is weekend, first Monday)
  - If YES and time >9:30 AM ET: ISM likely released
  - If YES and time <9:30 AM ET: Check if released yet, or scheduled for 10 AM ET
  - Display: "ISM released [time]" or "ISM releases at 10:00 AM ET today"

STEP 4: Display Current Status
  Most Recent Month: [Month Year]
  Most Recent PMI: [value]
  Is it <50? [Yes/No]
  Consecutive months <50: [count]
  Gate Status: [see logic below]
```

**Today's Check Logic:**

```
[Auto-fetch all ISM data]

Current Streak: [X] consecutive months <50
Last Reading: [Month Year] = [PMI value]

IF today is 1st business day of month AND ISM released today:
  Today's New PMI: [value]
  Is today's PMI <50? [Yes/No]
  
  IF PMI <50:
    Streak updated: [X] → [X+1] consecutive months
    
    IF new streak ≥9 AND PMI <48:
      STATUS: 🚨 MANUFACTURING RECESSION CONFIRMED
      Message: "ISM PMI [value] released today. Manufacturing recession confirmed: [X+1] consecutive months <50"
      Gate Trigger: TRIGGERED
      Action: HOLD synthesis pending employment trend confirmation
      Recommendation: "Wait for Friday jobs data + FOMC decision before deploying"
      
    ELSE IF PMI <50 but streak <9:
      STATUS: ⚠️ MANUFACTURING WEAKNESS
      Message: "ISM [value] remains in contraction. Current streak: [X+1] months <50 ([9-X-1] months to recession threshold)"
      Gate Trigger: MONITOR
      Action: Continue monitoring, proceed with caution
      
  ELSE IF PMI ≥50:
    Streak Reset: 0 (streak broken)
    STATUS: ✅ Gate cleared
    Message: "ISM [value] above 50 - manufacturing stabilized, streak broken"
    Gate Trigger: CLEARED
    Action: Proceed with normal evaluation
    
ELSE IF today is NOT 1st business day (no new ISM release):
  Previous Streak: [X] consecutive months <50
  Last Reading: [Month Year] = [PMI value]
  Days until next ISM: [N days]
  
  IF streak ≥9:
    STATUS: 🚨 MANUFACTURING RECESSION CONFIRMED
    Message: "Manufacturing recession remains active ([X] consecutive months <50). Awaiting next ISM release [DATE]"
    Gate Trigger: TRIGGERED
    Action: HOLD synthesis
    
  ELSE IF streak 6-8:
    STATUS: ⚠️ MANUFACTURING WEAKNESS APPROACHING THRESHOLD
    Message: "Manufacturing contraction ongoing ([X] months <50, [9-X] months until recession confirmed)"
    Gate Trigger: MONITOR
    Action: Proceed with caution, watch next ISM release
    
  ELSE:
    STATUS: ✅ No recession signal
    Message: "Manufacturing weakness limited ([X] months <50)"
    Gate Trigger: CLEARED
    Action: Proceed normally
```

---

### GATE 7: Federal Reserve Catalyst Gate (AUTO-FETCH FOMC SCHEDULE)

**Purpose:** Determine if Fed rate cut catalyst is intact or degraded

**Automatic FOMC Schedule Fetch (Run Every Task Execution):**

```
STEP 1: Fetch Official FOMC Meeting Schedule
  Search for: "FOMC meeting schedule 2026" OR "Federal Reserve meeting calendar"
  Sources: Federal Reserve official website, Fed Press Calendar
  Retrieve: All scheduled FOMC meetings for current year
  Parse: Extract dates, times (typically Wed @ 2:00 PM ET for decisions)
  
STEP 2: Identify Upcoming Meetings
  - Today's date: [DATE]
  - Calculate days until next FOMC decision
  - Meetings within 14 days? [Yes/No]
  - Next 3 FOMC dates: [List with decision times]
  
  Example:
    "Today: Dec 30, 2025
     Next FOMC: Jan 27-28, 2026 (Decision Wed Jan 28 @ 2:00 PM ET)
     Days until decision: 29 days
     
     FOMC within 10 days? No
     FOMC within 14 days? No"

STEP 3: Check Recent Economic Data Weakness
  - ISM Manufacturing: [from Gate 6 auto-fetch]
    Is streak ≥9 and PMI <48? [Yes/No]
  - Search for: "unemployment claims latest" or "jobless claims trend"
  - Search for: "nonfarm payroll forecast next Friday"
  - Determine: Is employment showing weakness?
```

**Simplified Gate Logic (no CME FedWatch dependency):**

```
[Auto-fetch FOMC schedule + recent economic weakness data]

STEP 1: Is FOMC meeting within 10 calendar days?

IF NO FOMC within 10 days:
  STATUS: ✅ Gate cleared
  Next FOMC: [DATE] ([N] days away)
  Message: "No imminent FOMC decision. Normal market conditions."
  Gate Trigger: CLEARED
  Action: Proceed normally

IF YES, FOMC within 10 days:
  STEP 2: Check Recent Economic Data Weakness
  
  Weakness signals:
  - ISM <50 for 9+ months? [Yes/No] — from Gate 6
  - Unemployment claims rising trend? [Yes/No] — search data
  - Jobs forecast weak? [Yes/No] — search expectations
  
  Count weakness signals: [0, 1, 2, or 3]
  
  IF 2+ weakness signals active:
    STATUS: ⚠️ CATALYST UNCERTAINTY GATE TRIGGERED
    Message: "Fed rate cut catalyst at risk (FOMC [DATE], economic weakness in ISM/employment)"
    Weakness signals: [List which ones]
    Risk: "If FOMC holds rates steady (vs cut expected), recent rally may unwind"
    Gate Trigger: TRIGGERED
    Action: HOLD synthesis pending FOMC decision
    Recommendation: "Defer market synthesis until FOMC announces decision [DATE @ 2:00 PM ET]"
    
  ELSE IF 0-1 weakness signals:
    STATUS: ✅ Gate cleared
    Message: "FOMC [DATE] with mixed economic data, insufficient weakness to trigger hold"
    Gate Trigger: CLEARED
    Action: Proceed with normal evaluation
```

---

### GATE 8: International Carry-Trade & Systemic Risk Gate

**Purpose:** Detect if Japanese carry-trade unwinding is creating global deleveraging pressure

**Automatic Data Fetch (Run Every Task Execution):**

```
STEP 1: Fetch Japan 10-Year Yield
  Search for: "Japan 10-year yield today" OR "Japanese JGB 10Y"
  Sources: Bloomberg, Reuters, Financial Times, investing.com
  Record: Current level, prior close
  Risk zone: >1.75%

STEP 2: Fetch Bitcoin Price & Daily Change
  Search for: "Bitcoin price today" OR use finance tool
  Record: Current price, % change today, % change from open
  Calculate: Is down >5%? Is down >3%?
  Risk: Bitcoin down >5% = carry-trade unwind signal

STEP 3: Fetch Crypto Sector Performance
  Get: MSTR (MicroStrategy), COIN (Coinbase), HOOD (Robinhood)
  Record: Today's % change for each
  Calculate: How many down >3%?
  Risk: 2+ of 3 down >3% = crypto weakness spreading

STEP 4: Get VIX Level
  (Already fetched in Check 3)
  Use: Current VIX from earlier Phase A check
```

**Gate Trigger Logic:**

```
[Auto-fetch all carry-trade data]

Metrics collected:
- Japan 10Y: [value]
- Bitcoin: [price, % change]
- Crypto stocks (MSTR, COIN, HOOD): [% changes]
- VIX: [from Check 3]

IF (Japan 10Y >1.75%) AND (Bitcoin down >5%) AND (2+ crypto stocks down 3%+):
  STATUS: 🚨 CARRY-TRADE UNWIND GATE TRIGGERED
  Message: "Yen carry-trade unwinding detected"
  Data: "Japan 10Y [value], Bitcoin [% down], Crypto: MSTR [%], COIN [%], HOOD [%]"
  Risk: "Japan holds $1.1T US Treasuries. Deleveraging could spread globally."
  Gate Trigger: TRIGGERED
  Action: Increase monitoring to hourly frequency
  Recommendation: "Reduce planned synthesis deployment size by 50%, or defer until risk passes"

ELSE IF (VIX spikes >25) AND (Bitcoin down >3%):
  STATUS: 🚨 CONTAGION RISK GATE TRIGGERED
  Message: "Crypto weakness spreading to broader equity market"
  Data: "VIX [value], Bitcoin down [%]"
  Risk: "Systematic deleveraging could accelerate from crypto → equities"
  Gate Trigger: TRIGGERED
  Action: Halt synthesis pending contagion assessment
  Recommendation: "Defer synthesis until broader market stabilizes"

ELSE:
  STATUS: ✅ Gate cleared
  Data: "Japan 10Y [value], Bitcoin stable [% change], Crypto stable"
  Gate Trigger: CLEARED
  Action: Proceed normally
```

---

### GATE 9: Sentiment Rationality Gate

**Purpose:** Determine if market fear/greed is RATIONAL (matching deteriorating fundamentals) or IRRATIONAL (diverging from fundamentals)

**Logic:**

```
STEP 1: Note Current Sentiment
Fear & Greed Index: [from Check 5]
Direction: Rising? Falling? High? Low?

STEP 2: Note Current Fundamentals
Breadth: [from Check 1]
Manufacturing: [from Gate 6 — streak count and PMI]
Employment: [from Gate 7 — weakness signals]
Consumer: [from Check 2 — XRT, confidence]
Credit Risk: [from Gate 8 — carry-trade signals]

STEP 3: Assess Alignment

IF Fear/Greed is RISING (or already elevated 0-40):
  Compare to fundamentals:
  
  IF fundamentals are DETERIORATING (breadth <40%, ISM <50, employment rising):
    ASSESSMENT: RATIONAL FEAR
    Message: "Fear spike matches fundamental weakness (manufacturing, breadth, employment deteriorating)"
    Verdict: Fear is an appropriate warning signal, NOT a contrarian buy setup
    Gate Trigger: RATIONAL (no false signal)
    Recommendation: "Do NOT treat spike as automatic 'buy' opportunity. Hold synthesis pending stability."
    
  ELSE IF fundamentals are IMPROVING (breadth >50%, ISM stable, employment strong):
    ASSESSMENT: IRRATIONAL CAPITULATION
    Message: "Fear spike diverges from improving fundamentals (breadth, consumer strength)"
    Verdict: Market is over-panicking. Contrarian buying opportunity likely.
    Gate Trigger: IRRATIONAL (false signal)
    Recommendation: "Can proceed with measured 5-10% deployment (Playbook 1 scale)"
    
  ELSE IF fundamentals are MIXED:
    ASSESSMENT: INVESTIGATE
    Message: "Fear/Greed mixed vs mixed fundamentals - deeper analysis needed"
    Verdict: Sentiment is partially rational. Which fundamental factors deteriorated?"
    Gate Trigger: INVESTIGATE
    Recommendation: "WAIT for next critical data point to clarify direction"

ELSE IF Fear/Greed is FALLING (or already low, >60):
  Message: "Greed spike detected. Verify fundamentals justify optimism."
  Action: Ensure breadth, manufacturing, employment all confirm bullish case
  If they do: Greed is rational, proceed normally
  If they don't: Greed may be unjustified, caution warranted
```

---

## TRIGGER HIERARCHY & ACTION MATRIX

### TIER 1: IMMEDIATE CIRCUIT BREAKERS
Execute same-day, no synthesis required

```
Trigger: VIX >30 (panic level)
Action: IMMEDIATE - Can execute Playbook 2 (defensive rebalance) same-day
Recommendation: "Market panic detected. Consider de-risking 10-20%."

Trigger: Breadth <30% (severe deterioration)
Action: IMMEDIATE - Flag for Master-Architect review same-day
Recommendation: "Breadth collapse signals potential capitulation. Monitor for reversal."

Trigger: Crypto crash >5% + Bitcoin <$80K
Action: IMMEDIATE - Increase monitoring, assess contagion
Recommendation: "Carry-trade unwind evident. Reduce deployment by 50% or defer."
```

### TIER 2: GATE TRIGGERS
Stop synthesis, investigate before proceeding

```
Trigger: Gate 6 (Manufacturing Recession) TRIGGERED
Hold Until: Friday jobs data + FOMC decision
Recommendation: "Do NOT deploy until employment trend + Fed decision provide clarity"

Trigger: Gate 7 (Fed Catalyst) TRIGGERED
Hold Until: FOMC decision (auto-fetched date/time)
Recommendation: "Do NOT deploy until Fed announces decision. Catalyst degraded."

Trigger: Gate 8 (Carry-Trade Unwind) TRIGGERED
Hold Until: Global deleveraging risk passes
Recommendation: "Reduce deployment by 50% or defer until contagion risk passes"

Trigger: Gate 9 (Sentiment Rationality) = RATIONAL FEAR
Hold Until: Fundamentals stabilize or improve
Recommendation: "Fear is warranted. Hold deployment until signals improve."
```

### TIER 3: MONITORING TRIGGERS
Track, flag for awareness

```
Trigger: Breadth declining (40-50% range)
Action: Monitor daily, flag if drops below 40%
Recommendation: "Breadth softening. Watch for further deterioration."

Trigger: VIX 20-25 (elevated)
Action: Monitor daily, flag if breaches 25%
Recommendation: "Volatility elevated. Caution warranted."

Trigger: Gate 9 = INVESTIGATE (mixed fundamentals)
Action: Await next critical data
Recommendation: "Wait for next critical data to clarify direction."
```

---

## REPORT GENERATION

Generate Markdown report with sections in order:

### SECTION 1: DASHBOARD STATUS
```markdown
## Dashboard Status - [TODAY'S DATE]

**Market Metrics:**
| Metric | Value | Status | Threshold |
|--------|-------|--------|-----------|
| Breadth | XX% | [✅/⚠️/🚨] | <40% trigger |
| XRT | X% | [✅/⚠️/🚨] | <-2% concern |
| VIX | XX | [✅/⚠️/🚨] | >25 trigger |
| S&P 500 vs 50-day MA | [above/below] | [✅/⚠️/🚨] | Trend indicator |
| Sentiment (Fear/Greed) | X/100 | [✅/⚠️/🚨] | Rising/stable/falling |

**Interpretation:**
[1-2 sentence summary of overall market technicals]
```

### SECTION 2: CRITICAL GATES STATUS
```markdown
## Critical Gates Analysis

### Gate 6: Manufacturing Recession (Auto-Fetched ISM)
**Last ISM Reading:** [Month Year] PMI [value]
**Consecutive months <50:** [X] months
**Next ISM Release:** [DATE @ 10:00 AM ET]
**Status:** [✅ CLEARED / ⚠️ ALERT / 🚨 TRIGGERED]
**Message:** [Explanation]
**Action:** [PROCEED / HOLD UNTIL / MONITOR]

### Gate 7: Federal Reserve Catalyst (Auto-Fetched FOMC)
**Next FOMC Decision:** [DATE] at [TIME]
**Days until decision:** [N days]
**Economic weakness signals:** [0/1/2/3]
**Status:** [✅ CLEARED / ⚠️ ALERT / 🚨 TRIGGERED]
**Message:** [Explanation]
**Action:** [PROCEED / HOLD UNTIL [DATE] / MONITOR]

### Gate 8: Carry-Trade & Systemic Risk (Auto-Fetched)
**Japan 10Y:** [Value] [Risk zone >1.75%? Yes/No]
**Bitcoin:** [Price] [% change] [Risk <-5%? Yes/No]
**Crypto Sector:** [MSTR, COIN, HOOD performance]
**Status:** [✅ CLEARED / ⚠️ ALERT / 🚨 TRIGGERED]
**Message:** [Explanation]
**Action:** [PROCEED / REDUCE 50% / DEFER]

### Gate 9: Sentiment Rationality
**Fear & Greed:** [Value/100]
**Fundamental Alignment:** [Deteriorating/Mixed/Improving]
**Assessment:** [RATIONAL FEAR / IRRATIONAL CAPITULATION / INVESTIGATE]
**Status:** [✅ CLEARED / ⚠️ ALERT / 🚨 TRIGGERED]
**Message:** [Explanation]
**Action:** [HOLD / CAN PROCEED / WAIT FOR DATA]
```

### SECTION 3: SYNTHESIS RECOMMENDATION
```markdown
## Overall Synthesis Recommendation

**Master-Architect Action:** [ALL CLEAR / TIER 3 MONITORING / TIER 2 HOLD / TIER 1 IMMEDIATE]

**Summary:**
[1-2 paragraph explanation of which gates triggered, why, and what it means]

**Specific Recommendation:**
- **IF All Gates Clear:** "✅ Proceed with synthesis as scheduled"
- **IF Tier 3 Monitoring:** "📊 Proceed with synthesis, flag [specific risk] for tracking"
- **IF Tier 2 Gate Triggered:** "⚠️ HOLD synthesis until [specific event/date]. Gates triggered: [list]"
- **IF Tier 1 Circuit Breaker:** "🚨 EXECUTE Playbook [1 or 2] same-day. Market conditions warrant immediate action."

**Next Critical Data Point:**
- 📅 [Date]: [Event] (e.g., "Friday Dec 31: End-of-year positioning" OR "Jan 2: ISM Manufacturing")
- Impact: [Why this matters]
- Hold Status: [Synthesis remains on hold / Synthesis can proceed after this]
```

### SECTION 4: AUTO-FETCH DATA SUMMARY (NEW)
```markdown
## Auto-Fetched Data Verification

**ISM Manufacturing (Auto-fetched):**
- Source: ISM official reports, Trading Economics
- Last reading: [Month Year] PMI [value]
- Consecutive months <50: [X]
- Streak started: [Month Year]
- Days until next release: [N]

**FOMC Schedule (Auto-fetched):**
- Source: Federal Reserve official calendar
- Next meeting: [DATE] ([N] days away)
- Decision time: [TIME]
- Following meetings: [+1 and +2]

**Market Data (Auto-fetched):**
- Breadth: [X]% (fetched from [source])
- VIX: [X] (fetched from [source])
- Bitcoin: [price] (fetched from [source])
- Japan 10Y: [value]% (fetched from [source])
```

### SECTION 5: WHAT CHANGED SINCE YESTERDAY (Optional, if tracking daily)
```markdown
## Changes Since Yesterday

**Metrics that moved:**
- VIX: 19 → 21 (+2, now elevated)
- Breadth: 58% → 54% (declining trend, watch)
- Fear & Greed: 22 → 28 (rising, rational response to weakness)

**Metrics unchanged:**
- S&P 500: Above 50-day MA (unchanged)
- ISM: Last reading [value] (unchanged, next release [date])

**Gates that changed status:**
- [List any gate that changed from prior day]

**Gates that remained unchanged:**
- Manufacturing Recession: Still TRIGGERED (awaiting employment confirmation)
- [Other unchanged gates]
```

### SECTION 6: MONITORING WATCH LIST (If applicable)
```markdown
## Active Monitoring

**If running twice/day (9:30 AM + 12:00 PM):**

Items to monitor for intraday changes:
- VIX (if >20, monitor hourly)
- Bitcoin (if carry-trade gate triggered, monitor intraday moves)
- Breadth (if declining, watch for capitulation)
- Fed speakers (if any scheduled today)

**If Carry-Trade Gate triggered:**
Hourly monitoring: Japan 10Y yield, Bitcoin price, crypto sector performance

**If Sentiment Rationality = INVESTIGATE:**
Awaiting [next critical data date/event] for clarification
```

---

## EXECUTION INSTRUCTIONS

### Option A: Single Daily Run (12:00 PM ET)
- Run once per day at market midday
- Captures: Morning volatility settled, all Phase A data available, all auto-fetches current
- Output: Single report per day

### Option B: Twice Daily Runs (9:30 AM ET + 12:00 PM ET) — RECOMMENDED
- Run 1 (9:30 AM ET): Market open snapshot
  - Captures: Pre-market sentiment + overnight data + market open action
  - Auto-fetches: Latest ISM history, FOMC schedule, Japan 10Y, Bitcoin, crypto, Fear/Greed
  - Output: Early morning report, identifies urgent gates/triggers
  - Use for: Quick decision on portfolio action at market open
  
- Run 2 (12:00 PM ET): Market midday snapshot
  - Captures: Morning trading action, any surprise news, intraday volatility
  - Auto-fetches: All data again (captures intraday changes to Bitcoin, Japan 10Y, Fear/Greed, etc.)
  - Output: Midday update, confirms or refines morning assessment
  - Use for: Final synthesis decision, confirms or changes morning recommendation

**Recommendation:** Use Option B (twice daily). Morning run catches pre-market + open volatility. Midday run confirms/refines. All data auto-fetches each run, no manual updates.

---

## TASK PROMPT FOR PERPLEXITY

```
You are a Market Trigger Analysis system. Your job is to analyze market conditions 
daily and determine if synthesis (deployment of market intelligence for portfolio decisions) 
should proceed or be held pending critical events.

IMPORTANT: Auto-fetch all static data on each run. No manual updates required.

AUTO-FETCH SEQUENCE (run at start of each task execution):

1. ISM Manufacturing PMI History
   Search: "ISM Manufacturing PMI history 2024 2025 latest"
   Extract: Last 24 months of PMI values with dates
   Calculate: Consecutive months <50 (count backwards from most recent)
   Output: "Last ISM: [Month Year] = [value], Consecutive months <50: [X]"

2. FOMC Meeting Schedule
   Search: "FOMC meeting schedule 2026"
   Extract: All upcoming FOMC decision dates, times
   Calculate: Days until next decision, meetings within 10 days?
   Output: "Next FOMC: [DATE] @ [TIME], [N] days away"

3. Japan 10-Year Yield
   Search: "Japan 10-year yield today"
   Extract: Current level
   Output: "Japan 10Y: [value]%"

4. Bitcoin Price & Crypto Sector
   Fetch: Bitcoin current price, daily % change
   Fetch: MSTR, COIN, HOOD daily % changes
   Output: "Bitcoin: [price] [% change]. Crypto: MSTR [%], COIN [%], HOOD [%]"

5. Fear & Greed Index
   Search: "Fear and Greed Index today"
   Extract: Current value (0-100)
   Output: "Fear & Greed: [X]/100"

THEN RUN ANALYSIS:

PHASE A: Fetch breadth %, XRT daily return, VIX, S&P 500 vs 50-day MA, Fear & Greed Index

PHASE B: Evaluate 4 gates using AUTO-FETCHED data:
- Gate 6: Manufacturing recession? (use auto-fetched ISM history, consecutive months <50)
- Gate 7: Fed catalyst intact? (use auto-fetched FOMC dates + employment weakness check)
- Gate 8: Carry-trade unwind? (use auto-fetched Japan 10Y, Bitcoin, crypto)
- Gate 9: Sentiment rational? (use auto-fetched Fear/Greed vs fundamentals)

Apply Trigger Hierarchy:
- TIER 1 (VIX >30, breadth <30%): Immediate action required
- TIER 2 (Gates triggered): Hold synthesis pending event
- TIER 3 (Monitoring triggers): Track, proceed with caution

Generate report with:
1. Dashboard (Phase A metrics)
2. Gates Status (all 4 gates with auto-fetched data shown)
3. Master-Architect Recommendation (ALL CLEAR / TIER 3 / TIER 2 / TIER 1)
4. Auto-Fetch Data Summary (show what was fetched)
5. What Changed Since Yesterday (if tracking daily runs)
6. Next Critical Data Point (date, event, impact)

Output as formatted Markdown, suitable for analyst review and manual save.

NO MANUAL DATA ENTRY REQUIRED. All static data (ISM, FOMC, Japan 10Y, Bitcoin, Fear/Greed) 
auto-fetched on every execution.
```