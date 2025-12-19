# Quality-Assurance-Engineer.md v8.0.7
## Stock-Analyst Phase 1: Enhanced with Degradation Guidance & Check Classification

**Version:** v8.0.7 (Phase 1 Enhancement)  
**Previous:** v8.0.6-FIXED  
**Status:** Production Ready  
**Framework:** Stock-Analyst v8.13, Phase 1  
**Last Updated:** December 13, 2025, 1:10 PM MST

---

## PHASE 1 CHANGES (v8.0.6 → v8.0.7)

**Added:** 
- Degradation Guidance section (5 degradation paths for missing/stale inputs)
- Check vs. Alert classification for all 8 gates
- Gate pass/fail decision tree with auto-rerun integration
- Integration with quality_metadata alerts structure

**Preserved:** 
- All 8 original QA gate definitions, logic, escalation paths
- QA Report structure
- Framework invariants

---

## THE 8 QA GATES (v8.0.7)

### HARD CHECKS (Block Analysis on Failure)

**Gate 1: Portfolio Freshness**
- **Check:** Is PORTFOLIO.md ≤1 day old?
- **If FAIL:** Escalate to Master-Architect or block analysis
- **Reason:** Portfolio constraints depend on current allocation data
- **Classification:** **CHECK** (hard failure)

**Gate 2: Stock Existence & Uniqueness**
- **Check:** Is this a real, tradeable stock (no duplicates in recent analyses)?
- **If FAIL:** Block analysis (invalid stock)
- **Reason:** Cannot issue recommendation for non-existent security
- **Classification:** **CHECK** (hard failure)

**Gate 3: Sector Portfolio Constraints**
- **Check:** Is target sector below 35% hard cap (including new position)?
- **If FAIL:** Reduce position size or recommend HOLD
- **Reason:** Portfolio concentration limits
- **Classification:** **CHECK** (hard failure)

**Gate 4: Conviction Position Sizing**
- **Check:** Does position size match conviction tier?
- **If FAIL:** Adjust sizing or recommendation
- **Reason:** High conviction gets 3-5%, low conviction gets 0-1%
- **Classification:** **CHECK** (hard failure)

**Gate 5: Account Portfolio Cash Validation**
- **Check:** Does account have sufficient cash for position?
- **If FAIL:** Reduce position size or defer
- **Reason:** Cannot issue position bigger than available cash
- **Classification:** **CHECK** (hard failure)

**Gate 6: Thesis Quality & Schema Validation**
- **Check:** Are all thesis components present and valid?
  - Thesis statement ✓
  - 3+ key drivers ✓
  - 3+ key risks ✓
  - Conviction level ✓
- **If FAIL:** Return to TURN 2 for thesis completion
- **Reason:** Complete thesis required before sizing
- **Classification:** **CHECK** (hard failure)

**Gate 8: Execution Ready**
- **Check:** Are there any unresolved questions or ambiguities?
- **If FAIL:** Flag and resolve before delivery
- **Reason:** Analyst must have high confidence
- **Classification:** **CHECK** (hard failure)

### SOFT ALERT (Informs but Doesn't Block)

**Gate 7: Master-Architect Guidance Compliance**
- **Check:** Does position comply with all standing MA directives?
- **If MISMATCH:** Log alert, proceed with note
- **Severity:** WARNING
- **Reason:** MA directives are advisory constraints, not absolute blockers
- **Classification:** **ALERT** (non-blocking)

---

## DEGRADATION GUIDANCE (Phase 1 Addition)

When inputs missing or stale, analysis proceeds with penalty + disclosure in alerts.

### Degradation Rule 1: Portfolio Data Stale (>1 day)

**Condition:** PORTFOLIO.md >1 day old at analysis time

**Action:** Proceed with analysis; apply constraint validation penalty

**Penalty:** -1 to -2 conviction points (analyst discretion based on market volatility)

**Alert Structure:**
```json
{
  "alert_id": "PORTFOLIODATASTALE",
  "severity": "WARNING",
  "message": "Portfolio data [X hours] old (>1 day threshold)",
  "penalty_points": -1,
  "disclosure": "Sector caps and position sizing based on stale allocation; rebalance at market open recommended"
}
```

**Conviction Example:**
```
Base 75 × EMA 1.05 × GC 1.15 - 1 (stale portfolio) = 90.56
```

---

### Degradation Rule 2: MA Output Missing/Stale (>4 hours or unavailable)

**Condition:** MA context >4 hours old or unavailable

**Action:** Use NEUTRAL regime (default safe position)

**Penalty:** -2 conviction points

**Alert Structure:**
```json
{
  "alert_id": "DATAFRESHNESSMA",
  "severity": "WARNING",
  "message": "Market-Analyst output [X hours] old (cache age)",
  "penalty_points": -2,
  "disclosure": "Analysis used cached/default MA regime; fresh output will be loaded async"
}
```

**Conviction Example:**
```
Base 75 × EMA 1.05 × GC 1.15 - 2 (MA stale penalty) = 91.46
```

---

### Degradation Rule 3: Alpha Picks Context Missing (>24 hours or unavailable)

**Condition:** Alpha context >24 hours old or unavailable

**Action:** Use NONE (no context provided)

**Penalty:** 0 points (no boost, not a penalty)

**Alert Structure:**
```json
{
  "alert_id": "ALPHACONTEXTMISSING",
  "severity": "INFO",
  "message": "Alpha Picks context >24 hours old; using NONE",
  "penalty_points": 0,
  "disclosure": "No Alpha Picks conviction boost applied; conviction from fundamental + technical"
}
```

**Conviction Example:**
```
No Alpha boost would add +2-3 points if available
Base 75 × EMA 1.05 × GC 1.15 = 89.46
```

---

### Degradation Rule 4: EMA Cache Incomplete (<200 days available)

**Condition:** Less than 200 days of EMA history available (new stocks, IPOs)

**Action:** Skip Golden Cross technical analysis; proceed with EMA 3/21/200 only

**Penalty:** -3 conviction points

**Alert Structure:**
```json
{
  "alert_id": "EMADATAINCOMPLETE",
  "severity": "WARNING",
  "message": "Only [X days] of EMA history available (need 200 for Golden Cross)",
  "penalty_points": -3,
  "disclosure": "Golden Cross analysis skipped; 3/21/200 EMA gating applied"
}
```

**Conviction Example:**
```
Base 75 × EMA 1.05 (3/21/200 only) × GC 1.0 (no signal) - 3 (incomplete data) = 79.88
```

---

### Degradation Rule 5: Analyst Revisions Unavailable (>5 days or missing)

**Condition:** Analyst estimate revisions >5 days old or missing

**Action:** Skip sentiment validation; proceed without sentiment input

**Penalty:** -1 conviction point

**Alert Structure:**
```json
{
  "alert_id": "ANALYSTREVISIONSUNAVAILABLE",
  "severity": "INFO",
  "message": "Analyst revisions not available or >5 days old",
  "penalty_points": -1,
  "disclosure": "Sentiment validation skipped; conviction from fundamental + technical"
}
```

**Conviction Example:**
```
Base 75 × EMA 1.05 × GC 1.15 - 1 (no sentiment) = 90.56
```

---

## DEGRADATION SUMMARY TABLE

| Input | Stale/Missing | Proceed? | Penalty | Disclosure | Quality_Metadata |
|-------|---|---|---|---|---|
| Portfolio Data | >1 day | YES | -1 to -2 pts | Alert WARNING | portfolio_stale |
| MA Output | >4 hrs | YES | -2 pts | Alert WARNING | ma_stale_penalty |
| Alpha Context | >24 hrs | YES | 0 pts | Alert INFO | alpha_missing |
| EMA Cache | <200 days | YES | -3 pts | Alert WARNING | ema_incomplete |
| Analyst Revisions | >5 days | YES | -1 pt | Alert INFO | analyst_revisions_missing |

**All degradations visible in `quality_metadata.alerts` and `quality_metadata.conviction_calculation.penalties_applied`**

---

## GATE PASS/FAIL DECISION TREE

```
FOR each Gate (1-8, plus Gates 9-10 in Stock-Analyst v8.12.1):
  
  Run Gate check
  
  IF Gate is HARD CHECK:
    IF Check fails:
      Add to quality_metadata.checks_executed.failed
      IF error is recoverable:
        Auto-rerun (max 3 attempts)
      ELSE:
        ESCALATE to analyst
    ELSE:
      Add to quality_metadata.checks_executed.passed
  
  IF Gate is SOFT ALERT:
    IF Check shows concern:
      Add alert to quality_metadata.alerts
      Apply penalty if specified
      Log disclosure
    Proceed with analysis (non-blocking)

END FOR each Gate

IF all HARD CHECKS pass:
  SAOutput ready for delivery
ELSE:
  Block delivery, escalate unresolvable checks
```

---

## AUTO-RERUN INTEGRATION (Phase 1)

When a gate fails with a recoverable error, auto-rerun system triggers:

```
Gate 9 Fails: TURN 3 EMA calculation error
→ Error is recoverable (calculation incomplete)
→ Auto-rerun TURN 3 (EMA calculation only)
→ Gate 9 re-executes
→ If now passes: Log recovery in quality_metadata.rerun_summary
→ If still fails after 3 attempts: Escalate to analyst
```

Recovery tracking visible in:
```json
{
  "rerun_summary": {
    "failed_checks_recovered": ["Gate_9_EMA_TECHNICAL"],
    "recovery_details": [...]
  }
}
```

---

## VERSION HISTORY

**v8.0.7 (December 13, 2025) – Phase 1 Enhancement**
- Added Degradation Guidance section (5 degradation paths)
- Added Check vs. Alert classification for all gates
- Integrated with quality_metadata alert structure
- All original 8 gates preserved (no changes to gate definitions)
- Auto-rerun integration documented
- Consolidated QA-Classification and Degradation-Rules content into single file

**v8.0.6-FIXED (December 9, 2025)**
- Enhanced GATE 6 with mandatory Schema Validation
- Added Escalation 6 for Thesis-Schema failure

---

## INTEGRATION WITH STOCK-ANALYST v8.12.1

Stock-Analyst v8.12.1 expands the QA protocol to 10 gates:
- **Gates 1-8:** Original QA gates (this file, Quality-Assurance-Engineer.md v8.0.7)
- **Gate 9:** EMA Technical Analysis (3/21/200 completion check) – TURN 3 validation
- **Gate 10:** Golden Cross Detection (local computation completion check) – TURN 3B validation

Quality-Assurance-Engineer.md v8.0.7 provides the foundational 8 gates. Gates 9-10 are validated within Stock-Analyst.md v8.12.1 TURN 3/TURN 6 workflow.

---

## NEXT PHASE (Phase 2)

Phase 2 will enhance degradation penalties with:
- Evidence-based penalty formula (analyst confidence % → calculated penalty)
- Scenario-testing tiers for penalties
- Conviction range softening with evidence

---

## VERSION CERTIFICATION

**Quality-Assurance-Engineer.md v8.0.7:**
- ✓ All 8 gates preserved from v8.0.6
- ✓ Check vs. Alert classification applied
- ✓ Degradation guidance added (5 paths)
- ✓ Auto-rerun integration documented
- ✓ Alert structure aligned with AGENT-OUTPUT-SCHEMA v8.0.4
- ✓ Framework invariants maintained
- ✓ Zero breaking changes (backward compatible)
- ✓ Content consolidated (no separate QA-Classification or Degradation-Rules files)

---

**End of Quality-Assurance-Engineer.md v8.0.7**
