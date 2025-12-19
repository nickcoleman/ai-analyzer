# QUALITY CONTROL CHECKLISTS & GATES - v1.1
## Mandatory Compliance Framework for Stock-Analysis-Methodology v1.4.3

**Document Type:** Consolidated Quality Control & File Operations Reference  
**Version:** 1.1 (Consolidated - Framework-Dependencies integrated into PART 1)  
**Effective:** December 7, 2025  
**Applies To:** All stock analyses (Stocks 6+) + All file operations  
**Mandatory:** YES (all 4 sections must be completed before analysis delivery)

---

## PART 1: FRAMEWORK-DEPENDENCIES VERIFICATION & FILE OPERATIONS GOVERNANCE

### 1.1 Purpose

**Primary:** Before recommending ANY file operations (deletion, archiving, modification), verify that files are not CRITICAL production dependencies.

**Secondary:** Maintain registry of critical file dependencies to prevent framework integrity loss.

### 1.2 When Required

- Before recommending deletion of ANY file
- Before recommending archival of ANY file
- Before recommending modification of ANY framework file
- Before recommending consolidation of files
- Quarterly framework integrity audit (per Section 1.10)

---

### 1.3 FILE TIER CLASSIFICATION - CRITICAL FRAMEWORK

#### Tier 1: CRITICAL PRODUCTION FRAMEWORK (Cannot Delete Without Authorization)

**These files are NON-NEGOTIABLE for framework operation. Deletion requires Master Architect written approval.**

| File | Version | Role | Recovery Difficulty | Status |
|------|---------|------|---|---|
| QUALITY-Guardrails.md | v1.4.7+ | Master guardrails definitions (A-E) | HIGH | CRITICAL |
| Master-Architect.md | v1.0.8+ | Design authority + portfolio governance | HIGH | CRITICAL |
| ANALYZE.md | v1.4.3+ | 6-TURN stock methodology | CRITICAL | CRITICAL |
| Stock-Analyst.md | v1.4.7+ | 5-section report structure + QC gates | CRITICAL | CRITICAL |
| PROMPT_TURN6_ACTIONABLE.md | v1.4.3+ | QC Gate + format conversion (Section 5-6) | CRITICAL | CRITICAL |
| Quality-Control-Checklists.md | v1.1+ | 4-part mandatory verification gates | CRITICAL | CRITICAL |
| STOCK-ANALYSIS-PRODUCTION.md | v1.0+ | Production execution prompt | MEDIUM | CRITICAL |

**RULE: Do not delete CRITICAL files without Master Architect authorization**

---

#### Tier 2: SUPPORTING ANALYSIS FRAMEWORK (Difficult to Delete, May Break Features)

**Deletion reduces framework capability but does not halt core operations. Requires careful replacement planning.**

| File | Version | Role | Impact if Deleted |
|------|---------|------|---|
| PROMPT_POSITION_MANAGEMENT.md | v1.1+ | Conviction-linked position sizing | Position sizing loses conviction-tier integration; template breaks |
| PROMPT_ACTIONABLE_FUNDAMENTALS.md | v1.1+ | Assumption taxonomy + penalties | Fundamental analysis loses structured assumption framework |
| PROMPT-MARKET-ANALYSTS.md | v7.6+ | FMA/IRM/MSPA/QMA analyst definitions | Multi-analyst consensus logic breaks; analyst outputs unvalidated |
| PROMPT_SYNTHESIS_INTEGRATION.md | v1.1+ | Consensus integration + data freshness | Data freshness audit breaks; GUARDRAIL D integration fails |
| PROMPT_MARKET_DECISION_ENGINE.md | v2.0+ | Position sizing formulas + rebalancing | Portfolio decision logic breaks; tactical execution disabled |
| STARTUP-REQUIREMENTS.md | v2.1+ | Data freshness validation + startup gates | Initial validation gates skip; analyses may execute with stale data |
| AGENT-OUTPUT-SCHEMA.json | v1.1+ | Unified analyst output schema | Analyst output validation breaks |

**RULE: Do not delete SUPPORTING files without impact assessment and replacement plan**

---

#### Tier 3: CONFIGURATION & REFERENCE (Can Archive, Lower Risk)

**These files support operations but are less critical to core framework. Can be archived if needs met by replacement.**

| File | Role | Can Delete If | Status |
|------|------|---|---|
| PROMPT_CORE_META.md | Framework overview reference | Equivalent documentation exists | May archive after Dec 31, 2025 |
| PROMPT_EXECUTIVE_SUMMARY.md | Output template reference | Output templates migrated to Stock-Analyst.md | May archive after Jan 15, 2026 |
| PROMPT_ACTIONABLE_TECHNICALS.md | Technical analysis template | Included in Stock-Analyst.md Section 4 | May archive after Jan 1, 2026 |
| Portfolio_Persona.md | Portfolio analysis logic | Logic migrated to portfolio agents | May archive after Jan 31, 2026 |
| PORTFOLIO_CASCADE_MODEL.md | Risk cascade reference | Included in PROMPT_MARKET_DECISION_ENGINE.md | May archive after Jan 31, 2026 |
| PROMPT_KICKOFF.md | Instructional (Phase 0 startup) | Training documentation updated | Safe to delete (instructional only) |

**RULE: Can delete REFERENCE files after equivalent content confirmed elsewhere**

---

### 1.4 CRITICAL DEPENDENCY MATRIX

**Understanding dependency chains prevents framework breakage.**

#### Dependency Chain 1: QUALITY-GUARDRAILS Foundation
```
QUALITY-Guardrails.md (MASTER)
  ├─→ ANALYZE.md (references Guardrail C conviction-cap tier system)
  ├─→ PROMPT_POSITION_MANAGEMENT.md (uses conviction-cap tier table)
  ├─→ PROMPT_ACTIONABLE_FUNDAMENTALS.md (uses assumption penalties)
  ├─→ Quality-Control-Checklists.md (Part 1 references QUALITY-Guardrails.md)
  └─→ STOCK-ANALYSIS-PRODUCTION.md (applies Guardrail C + D)
```
**Status:** CRITICAL - If QUALITY-Guardrails.md deleted, all dependent files break

---

#### Dependency Chain 2: Master-Architect Authority
```
Master-Architect.md (AUTHORITY)
  ├─→ GOVERNANCE-FRAMEWORK.md (authority references)
  ├─→ Portfolio Constraint Enforcement
  ├─→ Event Horizon Management (CEV Protocol)
  ├─→ Risk Regime Classification
  └─→ Framework Update Approvals
```
**Status:** CRITICAL - If Master-Architect.md deleted, governance authority undefined

---

#### Dependency Chain 3: Stock Analysis Methodology
```
ANALYZE.md (v1.4.3 - 6-TURN Framework)
  ├─→ STOCK-ANALYSIS-PRODUCTION.md (operational execution)
  ├─→ Stock-Analyst.md (5-section report structure)
  ├─→ PROMPT_TURN6_ACTIONABLE.md (QC gates)
  ├─→ Quality-Control-Checklists.md (mandatory verification)
  └─→ All portfolio analyses (depends on ANALYZE.md methodology)
```
**Status:** CRITICAL - If ANALYZE.md deleted, methodology breaks

---

#### Dependency Chain 4: Quality Control Gate Verification
```
Quality-Control-Checklists.md (4-part verification)
  ├─→ Part 1: Framework-Dependencies (file operation verification)
  ├─→ Part 2: Deletion-Approval Gate (references guardrails)
  ├─→ Part 3: Backtesting-Claims Audit (references PROMPTQUANTBACKTEST.md)
  ├─→ Part 4: Self-Consistency Check (internal analysis validation)
  └─→ PROMPT_TURN6_ACTIONABLE.md (Section 5 QC Gate)
```
**Status:** CRITICAL - If Quality-Control-Checklists.md deleted, QC gate breaks

---

#### Dependency Chain 5: Position Sizing & Risk Integration
```
PROMPT_POSITION_MANAGEMENT.md (Conviction-Tier Allocation)
  ├─→ ANALYZE.md (produces conviction %)
  ├─→ PROMPT_MARKET_DECISION_ENGINE.md (rebalancing logic)
  ├─→ PROMPT-MARKET-ANALYSTS.md (analyst outputs feed position sizing)
  └─→ All portfolio decisions (conviction % drives position size)
```
**Status:** HIGH - If PROMPT_POSITION_MANAGEMENT.md deleted, position sizing loses conviction link

---

### 1.5 CRITICAL FILES (DO NOT DELETE / ARCHIVE)

**Production Framework Files:**
- ✅ QUALITY-Guardrails.md (v1.4.7+) - CRITICAL
- ✅ Master-Architect.md (v1.0.8+) - CRITICAL
- ✅ ANALYZE.md (v1.4.3+) - CRITICAL
- ✅ Stock-Analyst.md (v1.4.7+) - CRITICAL
- ✅ PROMPT_TURN6_ACTIONABLE.md (v1.4.3+) - CRITICAL

**Analysis Methodology Files:**
- ✅ PROMPT_ACTIONABLE_FUNDAMENTALS.md - CRITICAL
- ✅ PROMPT_ACTIONABLE_TECHNICALS.md - CRITICAL
- ✅ PROMPT_POSITION_MANAGEMENT.md - CRITICAL
- ✅ PROMPT_MARKET_DECISION_ENGINE.md - CRITICAL

**Supporting Framework Files:**
- ✅ PROMPT-MARKET-ANALYSTS.md - CRITICAL
- ✅ PROMPT_SYNTHESIS_INTEGRATION.md - CRITICAL
- ✅ STARTUP-REQUIREMENTS.md - CRITICAL
- ✅ STOCK-ANALYSIS-PRODUCTION.md - CRITICAL

**Non-Critical Files (OK to Delete/Archive):**
- ⚪ Sample analyses (SAMPLE_ANALYSES_ARCHIVE.md)
- ⚪ Phase handoff packages (PHASE_HANDOFF_PACKAGE.md)
- ⚪ Old audit logs (if superseded by newer versions)
- ⚪ Incident reports (after lessons incorporated into frameworks)

---

### 1.6 6-QUESTION FILE OPERATIONS VERIFICATION CHECKLIST

**Mandatory Before ANY File Deletion**

**Answer ALL 6 questions. If ANY answer triggers escalation, stop and escalate for approval.**

#### QUESTION 1: Is this file in CRITICAL production list?
- Check: File Tier Classification above (Tier 1 list)
- If YES (file is CRITICAL) → Cannot delete without Master Architect written approval
- If NO → Proceed to Question 2

#### QUESTION 2: Do 3+ other files depend on this file?
- Check: Critical Dependency Matrix above
- Count files that reference or import this file
- If YES (3+ dependencies) → Classified as CRITICAL dependency
- If NO → Proceed to Question 3

#### QUESTION 3: Does deletion break Quality Control Gate?
- Check: Quality-Control-Checklists.md (all 4 parts)
- Test: Can QC gate complete without this file?
- If YES (deletion breaks QC gate) → Cannot delete without QC gate redesign
- If NO → Proceed to Question 4

#### QUESTION 4: Is this file referenced in versioning or governance?
- Check: Master-Architect.md authority matrix
- Check: GOVERNANCE-FRAMEWORK.md
- Check: Framework version history
- If YES (referenced in governance) → Cannot delete without governance review
- If NO → Proceed to Question 5

#### QUESTION 5: Does this file contain live operational data?
- Examples: PORTFOLIO.md, generated analyses, live trade records
- If YES (contains live data) → Archive, do not delete
- If NO → Proceed to Question 6

#### QUESTION 6: Has Master Architect approved deletion?
- Obtain written approval from Master-Architect.md authority
- Document approval reason and replacement plan (if applicable)
- If YES (Master Architect approved) → Proceed with deletion and archival
- If NO → Cannot delete

---

### 1.7 DECISION LOGIC

```
IF any question triggers escalation:
  → DO NOT recommend deletion
  → Escalate to Master Architect
  → Proceed only after explicit approval

IF all questions PASS (correct answers):
  → Safe to recommend deletion/archival
  → Document reason for audit trail
  → Notify affected users
  → Proceed with archival protocol
```

---

### 1.8 FILE OPERATIONS OUTPUT FORMAT

**Before recommending file operation, document:**

```
FILE OPERATION VERIFICATION
=============================
File name: [______]
Recommended action: DELETE / ARCHIVE / MODIFY
Reason: [______]

Question 1 (CRITICAL?): YES / NO → [Action]
Question 2 (3+ dependencies?): YES / NO → [Action]
Question 3 (Breaks QC gate?): YES / NO → [Action]
Question 4 (Governance reference?): YES / NO → [Action]
Question 5 (Live data?): YES / NO → [Action]
Question 6 (MA approval?): YES / NO → [Action]

Checklist status: PASS / FAIL
If FAIL: Explanation and escalation path
```

---

### 1.9 FILE ARCHIVAL PROTOCOL

**Before deleting ANY file, implement archival:**

#### Step 1: Create Backup Copy
- Naming convention: `[FILENAME]-ARCHIVED-[YYYY-MM-DD].md`
- Example: `PROMPT_KICKOFF-ARCHIVED-2025-12-07.md`
- Location: `/archive/` folder or designated cloud storage

#### Step 2: Document Archival
- File name: `[FILENAME]`
- Archive timestamp: `[YYYY-MM-DD]`
- Storage location: `[FOLDER/PATH]`
- Deletion reason: `[DOCUMENTED]`

#### Step 3: Update Change Log
- Add entry: "Deleted [FILENAME] v[X.X], archived at [LOCATION], reason: [____]"
- Date: [YYYY-MM-DD]
- Authority: [APPROVER NAME]

#### Step 4: Execute Deletion
- Delete file from active space
- Verify deletion completed
- Update dependent files (if necessary)
- Document completion in change log

#### Step 5: Post-Deletion Verification
- ☐ All dependent files still function
- ☐ QC gates still pass
- ☐ Framework integrity maintained
- ☐ Change log documented

---

### 1.10 FILE MODIFICATION GOVERNANCE

**Before modifying CRITICAL files:**

#### Standard Checklist (no approval needed for minor updates)
- ☐ Change is not breaking (backward compatible)
- ☐ Version number incremented (same major version if minor update)
- ☐ Change log updated with summary
- ☐ Dependent files notified of changes

#### Escalation Required (requires Master Architect review)
- Breaking changes (changes how other files use this file)
- Major version changes (v1.0 → v2.0 suggests architecture shift)
- Changes to guardrails or compliance standards
- Changes affecting QC gates or validation logic
- Changes to framework authority or governance

#### Version Numbering Scheme

**Format:** `vMAJOR.MINOR` (e.g., v1.0.8)

| Change Type | Version Increment | Example | Approval |
|---|---|---|---|
| Bug fix, clarification | Patch (e.g., v1.0 → v1.0.1) | Grammar fixes, examples | No approval needed |
| New feature, backwards-compatible | Minor (e.g., v1.0 → v1.1) | New section, new penalty rule | Master Architect review |
| Breaking change, new architecture | Major (e.g., v1.0 → v2.0) | New 6-TURN framework | Master Architect authorization |

---

### 1.11 MONTHLY FRAMEWORK INTEGRITY AUDIT

**First Monday of each month, verify framework integrity:**

- ☐ All CRITICAL files present (no placeholders)
- ☐ All CRITICAL files at minimum version (v1.4.7 QUALITY-Guardrails, v1.0.8 Master-Architect, etc.)
- ☐ All dependent files still aligned (ANALYZE.md still references current QUALITY-Guardrails version)
- ☐ No circular dependencies detected
- ☐ Version history accurate and updated
- ☐ QC gates all functional
- ☐ This file (Quality-Control-Checklists.md) current (v1.1+)
- ☐ All archived files documented with location

**If any issues found:** Escalate to Master Architect for resolution

---

### 1.12 FILE OPERATION EXAMPLES

#### Example 1: Safe to Archive (REFERENCE File)

**Proposal:** Archive PROMPT_KICKOFF.md (instructional file)

**6-Question Verification:**
1. Is this CRITICAL? NO (Tier 3 reference)
2. Do 3+ files depend? NO (instructional only; referenced by training docs)
3. Breaks QC gate? NO (Phase 0 startup, not part of QC)
4. Referenced in governance? NO (instructional)
5. Contains live data? NO (static instructional content)
6. Master Architect approval? N/A (not needed for reference file)

**Result:** ✅ SAFE TO ARCHIVE
- Create backup: PROMPT_KICKOFF-ARCHIVED-2025-12-07.md
- Document archival location
- Delete from active files
- Update this checklist

---

#### Example 2: Cannot Delete (CRITICAL File)

**Proposal:** Delete QUALITY-Guardrails.md to reduce file clutter

**6-Question Verification:**
1. Is this CRITICAL? **YES** (Tier 1 - master guardrails)
2. Do 3+ files depend? **YES** (ANALYZE.md, PROMPT_POSITION_MANAGEMENT.md, PROMPT_ACTIONABLE_FUNDAMENTALS.md, Quality-Control-Checklists.md)
3. Breaks QC gate? **YES** (Part 1 references QUALITY-Guardrails.md)
4. Referenced in governance? **YES** (Master-Architect.md, GOVERNANCE-FRAMEWORK.md)
5. Contains live data? NO
6. Master Architect approval? NO

**Result:** ❌ CANNOT DELETE
- STOP: This file is CRITICAL
- Escalate to Master Architect with justification for deletion
- If deletion approved: Implement replacement guardrails framework
- Document change in version history

---

#### Example 3: Modification with Approval (Framework Update)

**Proposal:** Update ANALYZE.md from v1.4.3 to v1.5.0 (new 7-TURN framework)

**Modification Checklist:**
- Change type: Breaking (changes 6-TURN to 7-TURN)
- Version change: v1.4.3 → v1.5.0 (major version change)
- Approval required: Master Architect authorization
- Impact: All dependent files need updating (STOCK-ANALYSIS-PRODUCTION.md, Stock-Analyst.md, Quality-Control-Checklists.md)

**Process:**
1. Submit proposal to Master Architect with change justification
2. Await authorization
3. Upon approval: Update ANALYZE.md v1.4.3 → v1.5.0
4. Update all dependent files to reference v1.5.0
5. Document change in version history
6. Notify all portfolio agents of framework change
7. Re-test all analyses against new framework

---

## PART 2: DELETION-APPROVAL-GATE (6-Question Verification)

### Purpose
Standard 6-question checklist to be completed before recommending ANY file deletion or archival.

### When Required
- Before ANY file deletion recommendation
- Before ANY file archival recommendation
- Required for analyst, QA, and Master-Architect review

### The Six Questions (MUST ALL ANSWER YES)

#### Question 1: Is This File Actually Non-Essential?
**What to check:**
- Is file used by any current or planned analysis?
- Does file contain irreplaceable data?
- Is file referenced by other production files?

**YES means:** File is confirmed non-essential  
**NO means:** STOP. Do not proceed with deletion.

---

#### Question 2: Have Users Been Notified?
**What to check:**
- Have all affected stakeholders been informed of deletion plan?
- Have users had time to back up critical data (if needed)?
- Is there consensus on deletion timing?

**YES means:** No surprise deletions; stakeholders ready  
**NO means:** STOP. Notify users first; wait for acknowledgment.

---

#### Question 3: Is There a Backup or Archive Copy?
**What to check:**
- For non-trivial files: Is a backup maintained?
- Can the file be recovered if needed?
- Is archive location documented?

**YES means:** Safe to delete; backup exists  
**NO means:** CONDITIONAL. If file is >10KB or contains useful historical data, create backup first.

---

#### Question 4: Does This Conflict with Any Live Analysis?
**What to check:**
- Is file currently referenced in any active stock analysis?
- Are there any pending analyses that use this file?
- Will deletion break any running processes?

**YES means:** STOP. Resolve conflicts first.  
**NO means:** Safe to delete; no live dependencies.

---

#### Question 5: Is Deletion Consistent with Framework Claims?
**What to check:**
- Does analysis claim to use features/frameworks that depend on this file?
- Would deletion undermine validity of any recommendations?
- Would deletion break consistency between claims and evidence?

**YES means:** STOP. Resolve inconsistencies; see SELF-CONSISTENCY-CHECK (Part 4) below.  
**NO means:** Deletion is consistent with analysis.

---

#### Question 6: Has Master-Architect Approved?
**What to check:**
- If critical file: Has Master-Architect explicitly approved deletion?
- If production framework: Has authority approved change?
- If governance file: Has executive sign-off been obtained?

**YES means:** Authorized deletion  
**NO means:** STOP. Escalate for approval.

---

### Decision Matrix

```
Score (# of YES answers):
  6/6 YES  → ✅ SAFE TO DELETE
  5/6 YES  → ⚠️  CONDITIONAL (fix the NO first)
  <5 YES   → ❌ DO NOT DELETE (escalate)
```

---

### Output Format

**Before recommending deletion, complete this:**

```
FILE DELETION APPROVAL GATE
===========================
File Name: [_______]
Deletion Reason: [_______]
Date Evaluated: [_______]

Q1: Non-essential? YES / NO
Q2: Users notified? YES / NO
Q3: Backup exists? YES / NO
Q4: No live conflicts? YES / NO
Q5: Consistent with framework claims? YES / NO
Q6: Master-Architect approved? YES / NO

DECISION:  APPROVE / CONDITIONAL / REJECT
If CONDITIONAL or REJECT: Explain and escalate.
```

---

## PART 3: BACKTESTING-CLAIMS-AUDIT (5-Step Process)

### Purpose
Verify that all backtesting claims are valid, traceable, and supported by actual backtesting framework output.

### When Required
**MANDATORY BEFORE making ANY of these claims:**
- ✅ "Backtest shows X% win rate"
- ✅ "Strategy has Sharpe ratio of X"
- ✅ "Backtest validates entry at $X.XX"
- ✅ "Historical strategy returned X%"
- ✅ "Entry signal validated by backtesting"
- ✅ "Backtest Winner: [Strategy Name]"

### Red Flags (DO NOT MAKE BACKTESTING CLAIMS IF ANY ARE TRUE)

❌ PROMPTQUANTBACKTEST.md does not exist in Space  
❌ File recommended for deletion in same analysis  
❌ Backtesting framework is inaccessible  
❌ Backtesting claims cannot be traced to source output  
❌ Win rates / Sharpe ratios / returns lack supporting documentation

### 5-Step Verification Process

#### Step 1: Verify Framework Exists
**Action:** Check that PROMPTQUANTBACKTEST.md exists in Space files

**Verification:**
- [ ] File exists in Space (not deleted)
- [ ] File is current version (not outdated)
- [ ] File is accessible (not archived)

**If ANY fail:** DO NOT MAKE BACKTESTING CLAIMS

---

#### Step 2: Verify Claims Are Traceable
**Action:** For each backtesting claim, trace to supporting output

**Verification:**
- [ ] Claim: "X% win rate" → Traceable to PROMPTQUANTBACKTEST.md output
- [ ] Claim: "Sharpe ratio Y.Y" → Traceable to quantitative model results
- [ ] Claim: "Strategy returned Z%" → Traceable to historical backtest
- [ ] Claim: "Entry at $A.AA validated" → Traceable to signal validation

**Documentation Required:**
```
Claim: [_______]
Source: PROMPTQUANTBACKTEST.md Section [_]
Supporting Output: [_______]
Date Verified: [_____]
Analyst: [_____]
```

**If ANY claim cannot be traced:** DO NOT INCLUDE IN ANALYSIS

---

#### Step 3: Verify No Contradictions
**Action:** Check that backtesting claims don't contradict file operations

**Verification:**
- [ ] If making backtesting claims, is PROMPTQUANTBACKTEST.md MARKED CRITICAL (not for deletion)?
- [ ] If deleting backtesting infrastructure, are NO backtesting claims being made?
- [ ] Recommendation consistency: Does analysis align with file deletion recommendations?

**If contradiction found:** HALT. See SELF-CONSISTENCY-CHECK (Part 4).

---

#### Step 4: Verify Win Rates & Metrics
**Action:** For quantitative claims, verify numbers match framework output

**Verification:**
- [ ] Win rate claim: [X%] matches PROMPTQUANTBACKTEST.md output [Y%]?
- [ ] Sharpe ratio claim: [A.A] matches output [B.B]?
- [ ] Return claim: [Z%] matches output [W%]?
- [ ] Confidence: All numbers within ±2% of source?

**Tolerance:**
- ✅ ±2% variance acceptable (rounding)
- ❌ >±2% variance STOP (requires investigation)

**If metrics don't align:** Revise claims or investigate discrepancy.

---

#### Step 5: Final Audit Sign-Off
**Action:** Analyst confirms all backtesting claims have been verified

**Checklist:**
- [ ] Step 1 PASS: Framework exists and is accessible
- [ ] Step 2 PASS: All claims traceable to source
- [ ] Step 3 PASS: No contradictions with file operations
- [ ] Step 4 PASS: All metrics verified (within ±2%)
- [ ] Step 5: Ready to deliver analysis with backtesting claims

**Output:**
```
BACKTESTING-CLAIMS AUDIT: PASS ✅
Audited By: [_____]
Date: [_____]
All backtesting claims verified and traceable.
Analysis ready for delivery.
```

**If ANY step fails:**
```
BACKTESTING-CLAIMS AUDIT: FAIL ❌
Failed Steps: [_____]
Action Required: Revise claims OR remove backtesting assertions
Analysis NOT ready for delivery until resolved.
```

---

## PART 4: SELF-CONSISTENCY-CHECK (4-Point Internal Logic Audit)

### Purpose
Verify that analysis contains no internal contradictions between claims, recommendations, and framework operations.

### When Required
**MANDATORY BEFORE finalizing ANY analysis or recommendation**

### Red Flags (STOP if ANY are found)

❌ Claim: "Framework is production-ready" + Recommendation: "Delete critical framework files"  
❌ Claim: "Backtesting analysis included" + Recommendation: "Archive backtesting infrastructure"  
❌ Claim: "Technical analysis comprehensive" + Recommendation: "Remove technical analysis tools"  
❌ Claim: "100% guardrails compliance" + Action: "Circumvent guardrail gating"  

### 4-Point Consistency Audit

#### Point 1: Framework Readiness Claims
**Question:** Are framework readiness claims consistent with file operations recommended?

**Check:**
- If analysis claims "Framework is production-ready for Phase 4":
  - ✅ CONSISTENT: All critical files marked for RETENTION
  - ❌ INCONSISTENT: Critical framework files recommended for deletion
  
- If analysis claims "Framework has institutional-grade quality controls":
  - ✅ CONSISTENT: Quality control infrastructure maintained
  - ❌ INCONSISTENT: QC files recommended for archival

**Verification Checklist:**
- [ ] No contradiction between readiness claims and framework deletions?
- [ ] Framework features preserved (not undermined by deletions)?
- [ ] Production status accurate (if claiming ready, is it ready)?

---

#### Point 2: Feature Claims vs. Deletions
**Question:** Do feature/capability claims contradict deletion/archival recommendations?

**Check:**
- If analysis claims "Backtesting analysis included":
  - ✅ CONSISTENT: Backtesting files remain (PROMPTQUANTBACKTEST.md, supporting templates)
  - ❌ INCONSISTENT: Backtesting infrastructure recommended for deletion

- If analysis claims "Technical analysis comprehensive":
  - ✅ CONSISTENT: PROMPT_ACTIONABLE_TECHNICALS.md retained
  - ❌ INCONSISTENT: Technical frameworks recommended for removal

- If analysis claims "Multi-agent synthesis operational":
  - ✅ CONSISTENT: All agent prompts retained (Fundamentals, Technicals, Macro, Sentiment)
  - ❌ INCONSISTENT: Any agent framework recommended for deletion

**Verification Checklist:**
- [ ] No features undermined by file deletions?
- [ ] All claimed capabilities are supported by retained files?
- [ ] Deliverables match framework capabilities?

---

#### Point 3: Recommendation Internal Logic
**Question:** Do recommendations contradict each other or the stated thesis?

**Check Examples:**
- If thesis is "BUY" but position sizing is "HOLD-like" (0-1% allocation):
  - ❌ INCONSISTENT (fix recommendation or allocation)
- If conviction is 65% but stop-loss is placed at -50% drawdown:
  - ❌ INCONSISTENT (stop loss doesn't match conviction; should be -20-30%)
- If analysis says "avoid macro risks" but position is max-sized:
  - ❌ INCONSISTENT (fix position or risk assessment)

**Verification Checklist:**
- [ ] Recommendation (BUY/HOLD/SELL) aligns with conviction %?
- [ ] Conviction % aligns with position size?
- [ ] Position size aligns with stated risk tolerance?
- [ ] Price targets align with recommendation strength?
- [ ] Decision triggers align with thesis drivers?

---

#### Point 4: Analyst Accountability
**Question:** Does analysis maintain analyst accountability (transparent reasoning)?

**Check:**
- [ ] Is recommendation rationale clear and traceable?
- [ ] Are assumptions disclosed (not hidden)?
- [ ] Are conviction penalties explained (not arbitrary)?
- [ ] Are risks acknowledged (not minimized)?
- [ ] Is analyst willing to stand behind this analysis?

**Red Flag Language to Avoid:**
- ❌ "Probably will work" (non-committal)
- ❌ "Might consider" (hedged)
- ❌ "Seems reasonable" (not decisive)
- ❌ "Hopefully the thesis holds" (wishful thinking)

**Better Language:**
- ✅ "Entry at $X; target $Y; conviction 65%"
- ✅ "Key risks: A, B, C with X% probability each"
- ✅ "If catalyst X occurs, conviction increases to 80%; if not, reduce to 40%"
- ✅ "I take accountability for this recommendation; here's why I'm confident"

---

### Consistency Audit Template

**Complete BEFORE finalizing analysis:**

```
SELF-CONSISTENCY CHECK
=======================

Analysis: [Stock name / Theme]
Analyst: [_____]
Date: [_____]

POINT 1: Framework Readiness Claims
  Claim: [_______]
  File Operations: [_______]
  Consistent? YES / NO
  If NO, explain: [_____]

POINT 2: Feature Claims vs. Deletions
  Claimed Features: [_______]
  Files Recommended for Deletion: [_______]
  Consistent? YES / NO
  If NO, explain: [_____]

POINT 3: Recommendation Internal Logic
  Thesis: [BUY/HOLD/SELL]
  Conviction: [X%]
  Position Size: [Y%]
  Stop Loss: [Z%]
  Consistent? YES / NO
  If NO, explain: [_____]

POINT 4: Analyst Accountability
  Is reasoning transparent? YES / NO
  Are assumptions disclosed? YES / NO
  Are risks acknowledged? YES / NO
  Would you defend this analysis publicly? YES / NO
  Overall accountability score: [__/4]

FINAL VERDICT:
  [ ] PASS - No contradictions; analysis ready for delivery
  [ ] CONDITIONAL - Fix [specific item] then re-check
  [ ] FAIL - Major contradictions; revise analysis before delivery
```

**If FAIL or CONDITIONAL:**
- Revise analysis to resolve contradictions
- Re-run self-consistency check
- Do NOT deliver until PASS

---

## MANDATORY COMPLIANCE REQUIREMENT (SUMMARY)

### Before Delivery: Complete ALL Four Sections

| Section | Purpose | Required? | Status |
|---------|---------|-----------|--------|
| **Part 1: Framework-Dependencies** | Verify files safe to delete + dependency matrix | ✅ YES | PASS / FAIL |
| **Part 2: Deletion-Approval-Gate** | 6-question deletion checklist | ✅ YES | PASS / FAIL |
| **Part 3: Backtesting-Claims-Audit** | Verify backtesting claims are valid | ✅ YES | PASS / FAIL |
| **Part 4: Self-Consistency-Check** | Verify no internal contradictions | ✅ YES | PASS / FAIL |

### Delivery Gate

**Analysis can only be delivered when:**
- ✅ Part 1: Framework-Dependencies = PASS (or N/A if no file operations)
- ✅ Part 2: Deletion-Approval-Gate = PASS (or N/A if no deletions recommended)
- ✅ Part 3: Backtesting-Claims-Audit = PASS (or N/A if no backtesting claims)
- ✅ Part 4: Self-Consistency-Check = PASS (always required)

**ANY FAIL → Analysis NOT delivered until resolved**

---

## AUDIT TRAIL & DOCUMENTATION

### Document All Completions

For each stock analysis, maintain record:

```
STOCK [N] - QC CHECKLIST COMPLETION
====================================
Stock Name: [_____]
Analysis Date: [_____]
Analyst: [_____]

Part 1 (Framework-Dependencies): PASS / FAIL / N/A
Part 2 (Deletion-Approval-Gate): PASS / FAIL / N/A
Part 3 (Backtesting-Claims): PASS / FAIL / N/A
Part 4 (Self-Consistency): PASS / FAIL / N/A

Issues Identified: [_____]
Resolutions Applied: [_____]
Final Status: APPROVED FOR DELIVERY / REQUIRES REVISION

Signed By: [_____] (Analyst)
Reviewed By: [_____] (QA)
Approved By: [_____] (Master-Architect)
```

---

## VERSION HISTORY

| Version | Date | Change Summary | Status |
|---------|------|---|---|
| 1.0 | Nov 28, 2025 | Initial four-part quality control checklists | Superseded |
| **1.1** | **Dec 7, 2025** | **CONSOLIDATED - Framework-Dependencies integrated into Part 1; added dependency matrix, tier classification, archival protocol** | **Active** |

---

**Document Prepared By:** Master Architect + Framework Recovery Phase 1  
**Date:** December 7, 2025, 2:30 PM MST  
**Status:** Ready for Stock Analysis v1.4.3 Deployment  
**Reference:** Stock-Analysis-Methodology-v1.4.3 + Framework-Dependencies Consolidation  

---

**END OF QUALITY-CONTROL-CHECKLISTS.md v1.1**