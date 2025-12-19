# AGENT-OUTPUT-SCHEMA.md v8.0.8

**Version:** 8.0.8  
**Framework:** Stock-Analyst v8  
**Phase:** 5 (Visualization)  
**Engineer:** Engineer 5  
**Status:** FINAL  
**Date:** December 14, 2025, 3:15 PM MST  
**Previous Version:** v8.0.7 (Phase 4)

---

## EXECUTIVE SUMMARY

AGENT-OUTPUT-SCHEMA v8.0.8 defines complete output structure for Stock-Analyst v8.13 Phase 5 analysis. Extends v8.0.7 with new charts section containing three optional chart URLs (Golden Cross, Conviction Decomposition, Assumption Heatmap) and rendering status field. All v8.0.7 fields preserved; Phase 5 adds visualization layer without breaking changes.

**Schema Status:** FINAL (fully backward compatible)  
**New Additions:** charts section with 4 fields  
**Removed Fields:** None  
**Modified Fields:** None  

---

## SCHEMA STRUCTURE

### Top-Level Output Object (SAOutput)

```json
{
  "analysismetadata": { ... },
  "convictionscores": { ... },
  "assumptions": [ ... ],
  "positionrecommendation": { ... },
  "qualitymetadata": { ... },
  "charts": { ... }
}
```

### Peer Sections (All At Root Level)

- **analysismetadata:** Ticker, date, analyst, analysis context
- **convictionscores:** Conviction calculations and ranges
- **assumptions:** Array of analyst assumptions
- **positionrecommendation:** Trade recommendation and sizing
- **qualitymetadata:** Quality assurance, QA gates, execution state
- **charts:** NEW - Optional visualization URLs and rendering status

---

## NEW: Charts Section (Phase 5)

**Status:** NEW in v8.0.8 (Phase 5)  
**Required:** No (optional enhancement)  
**Non-blocking:** Yes (rendering failures do NOT halt analysis)

### charts Object Structure

```json
{
  "charts": {
    "goldencrossurl": "https://charts.example.com/golden-cross-AAPL-20251214.png",
    "convictiondecompositionurl": "https://charts.example.com/conviction-AAPL-20251214.png",
    "assumptionheatmapurl": "https://charts.example.com/heatmap-AAPL-20251214.png",
    "renderingstatus": "COMPLETE"
  }
}
```

### Field Definitions

#### goldencrossurl (string | null)

**Type:** String (URL) or null  
**Required:** No  
**Nullable:** Yes  

**Description:** URL pointing to rendered Golden Cross Technical Chart visualization. Contains 250-day price history with EMA 3/21/50/200 overlays, candlestick OHLC data, and Golden Cross detection marker.

**Value Examples:**
- Success: `"https://charts.example.com/golden-cross-AAPL-20251214.png"`
- Failure: `null`

**When Null:**
- Insufficient price history (< 200 candlesticks)
- Invalid OHLC data
- Chart rendering engine unavailable
- CDN upload failure

**Data Source:** SAOutput.technicalfindings + SAOutput.pricehistory

---

#### convictiondecompositionurl (string | null)

**Type:** String (URL) or null  
**Required:** No  
**Nullable:** Yes  

**Description:** URL pointing to rendered Conviction Decomposition Chart visualization. Shows sequential component breakdown of conviction calculation: base conviction → penalties → boosts → final range.

**Value Examples:**
- Success: `"https://charts.example.com/conviction-AAPL-20251214.png"`
- Failure: `null`

**When Null:**
- Conviction calculation incomplete or invalid
- Missing conviction range bounds
- Penalties not properly calculated
- Chart rendering engine unavailable

**Data Source:** SAOutput.conviction + SAOutput.qualitymetadata.convictioncalculation

---

#### assumptionheatmapurl (string | null)

**Type:** String (URL) or null  
**Required:** No  
**Nullable:** Yes  

**Description:** URL pointing to rendered Assumption Risk Heatmap visualization. Color-coded matrix showing assumptions vs. risk metrics (type, validation status, confidence, penalty, sensitivity) with composite risk scoring.

**Value Examples:**
- Success: `"https://charts.example.com/heatmap-AAPL-20251214.png"`
- Failure: `null`

**When Null:**
- No assumptions present in analysis
- Invalid assumption data (missing required fields)
- Risk score calculation failed
- Chart rendering engine unavailable

**Data Source:** SAOutput.assumptions array

---

#### renderingstatus (string)

**Type:** Enum (string)  
**Required:** Yes (always present, even if all charts failed)  
**Nullable:** No  

**Valid Values:**
- `"COMPLETE"` - All 3 charts rendered successfully
- `"PARTIAL_FAILURE"` - 1-2 charts rendered, 1-2 failed
- `"UNAVAILABLE"` - All 3 charts failed or no data to chart

**Description:** Status indicator summarizing chart rendering results. Allows consumers to understand which visualization data is available.

**Value Selection Logic:**
```
IF charts_succeeded == 3:
  renderingstatus = "COMPLETE"
ELSE IF charts_succeeded > 0:
  renderingstatus = "PARTIAL_FAILURE"
ELSE:
  renderingstatus = "UNAVAILABLE"

WHERE charts_succeeded = count of non-null URLs from 3 chart fields
```

---

### charts Section Examples

#### Example 1: Complete Success (All Charts Rendered)

```json
{
  "charts": {
    "goldencrossurl": "https://cdn.charts.io/gc-NVDA-20251214.png",
    "convictiondecompositionurl": "https://cdn.charts.io/cd-NVDA-20251214.png",
    "assumptionheatmapurl": "https://cdn.charts.io/hm-NVDA-20251214.png",
    "renderingstatus": "COMPLETE"
  }
}
```

**Interpretation:** All three visualization charts available. Analyst can view full visual breakdown of conviction and assumptions.

---

#### Example 2: Partial Failure (1 Chart Failed)

```json
{
  "charts": {
    "goldencrossurl": "https://cdn.charts.io/gc-MSFT-20251214.png",
    "convictiondecompositionurl": null,
    "assumptionheatmapurl": "https://cdn.charts.io/hm-MSFT-20251214.png",
    "renderingstatus": "PARTIAL_FAILURE"
  }
}
```

**Interpretation:** Golden Cross and Assumption Heatmap available; Conviction Decomposition rendering failed (e.g., incomplete conviction calculation). Analysis proceeds; analyst has partial visual context.

---

#### Example 3: Unavailable (No Charts Rendered)

```json
{
  "charts": {
    "goldencrossurl": null,
    "convictiondecompositionurl": null,
    "assumptionheatmapurl": null,
    "renderingstatus": "UNAVAILABLE"
  }
}
```

**Interpretation:** No charts available (chart rendering engine offline, or insufficient data for all charts). Analysis complete with full SAOutput data; charts simply not rendered. No impact to core analysis quality.

---

### Non-Blocking Guarantee

**Critical Principle:** Chart rendering failures NEVER impact analysis:

- ✅ Analysis always completes (charts don't halt workflow)
- ✅ SAOutput always fully populated (all non-chart data present)
- ✅ Conviction, assumptions, recommendations unchanged (charts are visualization only)
- ✅ QA gates pass/fail independent of charts (charts don't trigger escalations)
- ✅ Master-Architect decisions unaffected (charts are informational)

**Implementation Pattern:**
```javascript
// In Stock-Analyst TURN 6
TRY {
  render_golden_cross_chart()
} CATCH error {
  LOG_ERROR(error)
  goldencrossurl = null
  // CONTINUE - don't halt, don't escalate
}

// Similar pattern for other 2 charts
// Then determine renderingstatus and return SAOutput
```

**Result:** Charts are true optional enhancement; analysis robust without them.

---

## BACKWARD COMPATIBILITY (v8.0.7 → v8.0.8)

### Preserved Fields (All Unchanged)

All v8.0.7 fields remain exactly as-is:

- ✅ analysismetadata (unchanged)
- ✅ convictionscores (unchanged)
- ✅ assumptions (unchanged)
- ✅ positionrecommendation (unchanged)
- ✅ qualitymetadata (unchanged)

### New Fields (Phase 5 Only)

Only additions (no removals, no modifications):

- ✅ charts.goldencrossurl (NEW)
- ✅ charts.convictiondecompositionurl (NEW)
- ✅ charts.assumptionheatmapurl (NEW)
- ✅ charts.renderingstatus (NEW)

### Impact on Phase 1-4 Systems

**All existing systems backward compatible:**

| System | v8.0.7 Behavior | v8.0.8 Behavior | Impact |
|--------|-----------------|-----------------|--------|
| Portfolio-Orchestrator | Reads SAOutput (ignores unknown fields) | Reads SAOutput + charts section | Safe (reads charts if present, ignores if absent) |
| Quality-Assurance-Engineer | Validates SAOutput fields | Validates SAOutput + charts section | Safe (charts optional, don't affect validation) |
| Master-Architect | Makes decisions (ignores charts) | Makes decisions (charts informational only) | No impact (decision logic unchanged) |
| Reporting Engine | Renders reports | Renders reports + embeds chart URLs | Safe (embeds charts if available) |
| Analytics Tracker | Tracks metrics | Tracks metrics + chart rendering status | Safe (new metric, doesn't affect existing) |

**Conclusion:** v8.0.8 is 100% backward compatible with v8.0.7. Phase 1-4 systems unaffected.

---

## INTEGRATION WITH STOCK-ANALYST v8.13 FINAL

### Chart Generation Workflow (TURN 6)

**Timing:** After all QA gates pass and state determination complete

**Pattern:**
1. Validate conviction, assumptions, technical data
2. IF valid, trigger chart generation (non-blocking)
3. Catch any rendering failures, log, continue
4. Populate SAOutput.charts fields (URLs or nulls)
5. Set renderingstatus based on success count
6. Return SAOutput with or without charts

**Code Flow:**
```javascript
// TURN 6 QA Gates → State Determination complete

// Initialize charts section
SAOutput.charts = {
  goldencrossurl: null,
  convictiondecompositionurl: null,
  assumptionheatmapurl: null,
  renderingstatus: "PENDING"
}

// Generate charts (try-catch-continue for each)
TRY { SAOutput.charts.goldencrossurl = generate_golden_cross_chart(...) }
CATCH { SAOutput.charts.goldencrossurl = null; LOG }

TRY { SAOutput.charts.convictiondecompositionurl = generate_conviction_decomposition_chart(...) }
CATCH { SAOutput.charts.convictiondecompositionurl = null; LOG }

TRY { SAOutput.charts.assumptionheatmapurl = generate_assumption_heatmap(...) }
CATCH { SAOutput.charts.assumptionheatmapurl = null; LOG }

// Determine renderingstatus
success_count = (goldencrossurl != null ? 1 : 0) + (convictiondecompositionurl != null ? 1 : 0) + (assumptionheatmapurl != null ? 1 : 0)
SAOutput.charts.renderingstatus = 
  success_count == 3 ? "COMPLETE" :
  success_count > 0 ? "PARTIAL_FAILURE" :
  "UNAVAILABLE"

// Return SAOutput (always complete, with or without charts)
RETURN SAOutput
```

---

## SCHEMA VALIDATION RULES

### charts Section Validation

**Validation Timing:** After chart generation in TURN 6

**Rules:**

```javascript
VALIDATE charts object:

  // goldencrossurl
  IF goldencrossurl != null:
    goldencrossurl must be string starting with "https://"
    goldencrossurl must reference valid image format (.png, .svg, .jpg)
  END IF

  // convictiondecompositionurl
  IF convictiondecompositionurl != null:
    convictiondecompositionurl must be string starting with "https://"
    convictiondecompositionurl must reference valid image format
  END IF

  // assumptionheatmapurl
  IF assumptionheatmapurl != null:
    assumptionheatmapurl must be string starting with "https://"
    assumptionheatmapurl must reference valid image format
  END IF

  // renderingstatus
  renderingstatus must be in ["COMPLETE", "PARTIAL_FAILURE", "UNAVAILABLE"]
  
  // Cross-field validation
  success_count = (goldencrossurl != null ? 1 : 0) + 
                  (convictiondecompositionurl != null ? 1 : 0) + 
                  (assumptionheatmapurl != null ? 1 : 0)
  
  IF renderingstatus == "COMPLETE":
    VALIDATE success_count == 3
  ELSE IF renderingstatus == "PARTIAL_FAILURE":
    VALIDATE success_count > 0 AND success_count < 3
  ELSE IF renderingstatus == "UNAVAILABLE":
    VALIDATE success_count == 0
```

---

## CONSUMER GUIDANCE

### Using charts Section in Downstream Systems

#### Quality-Assurance-Engineer (Phase 1 v8.0.7+)

**Action:** Ignore charts section; QA gates unaffected

```javascript
// QA validation continues as before
VALIDATE analysismetadata, convictionscores, assumptions, etc.
// Charts section optional, doesn't affect QA decision
IF charts.renderingstatus != "COMPLETE":
  LOG_INFO("Charts unavailable, but analysis valid")
```

---

#### Reporting Engine (Phase 2)

**Action:** Embed chart URLs if available

```javascript
// Generate report
IF charts.goldencrossurl != null:
  <img src="{charts.goldencrossurl}" alt="Golden Cross Chart">
END IF

IF charts.convictiondecompositionurl != null:
  <img src="{charts.convictiondecompositionurl}" alt="Conviction Decomposition">
END IF

IF charts.assumptionheatmapurl != null:
  <img src="{charts.assumptionheatmapurl}" alt="Assumption Risk Heatmap">
END IF

// If renderingstatus == "UNAVAILABLE", include note:
// "Visualizations unavailable; see data tables for metrics."
```

---

#### Portfolio-Orchestrator (Phase 3)

**Action:** Ignore charts; charts don't affect trade execution

```javascript
// Execute trade (conviction, position sizing, constraints unchanged)
EXECUTE(
  ticker: positionrecommendation.ticker,
  side: positionrecommendation.side,
  quantity: positionrecommendation.quantity,
  conviction: convictionscores.finalpointestimate
)
// Charts are presentation layer only; execution unaffected
```

---

#### Master-Architect (Governance)

**Action:** Use charts for analyst education; don't affect decisions

```javascript
// Review analysis for escalation
IF conviction > guidance:
  ESCALATE APPROVAL decision
  // Charts informational; don't change approval logic
END IF
```

---

## DEPLOYMENT & MIGRATION

### From v8.0.7 to v8.0.8

**Compatibility:** No breaking changes

**Deployment Steps:**

1. **Update Schema Definition** → Deploy AGENT-OUTPUT-SCHEMA.md v8.0.8
2. **Update Stock-Analyst** → Deploy Stock-Analyst.md v8.13 FINAL with chart generation
3. **Deploy Chart Specs** → Deploy 3 chart generators (Golden Cross, Conviction, Heatmap)
4. **Test Integration** → Run sample analysis, verify charts section populated
5. **Monitor** → Track chart rendering success rate (target: 95%+)

**Rollback Plan:**

If chart rendering issues occur:
1. Set all chart URLs to null (graceful degradation)
2. Set renderingstatus = "UNAVAILABLE"
3. Analysis proceeds unaffected
4. Fix chart rendering engine asynchronously

---

## QUALITY CHECKLIST

- ✅ charts section properly defined (peer to analysismetadata, qualitymetadata)
- ✅ Three chart URL fields specified (golden cross, conviction, heatmap)
- ✅ renderingstatus field documented (enum values clear)
- ✅ Rendering workflow documented (TURN 6 post-analysis)
- ✅ Non-blocking guarantee clear (failures don't halt)
- ✅ Backward compatibility verified (all v8.0.7 fields preserved)
- ✅ Integration with Stock-Analyst documented
- ✅ Examples for all renderingstatus values provided
- ✅ Consumer guidance documented (QA, Reporting, Orchestrator, MA)
- ✅ Validation rules specified
- ✅ No TODOs or placeholders

---

## VERSION HISTORY

**v8.0.8 (December 14, 2025 - Phase 5):**
- Added charts section (new peer section to analysismetadata, qualitymetadata)
- Three chart URL fields: goldencrossurl, convictiondecompositionurl, assumptionheatmapurl
- Added renderingstatus field: COMPLETE | PARTIAL_FAILURE | UNAVAILABLE
- All chart URLs optional (nullable)
- Non-blocking chart rendering guarantee documented
- Full backward compatibility with v8.0.7

**v8.0.7 (December 13, 2025 - Phase 4):**
- Integrated executionstate tracking with state machine
- Auto-rerun history and summary fields
- Escalation tracking and summary

**v8.0.6 (December 12, 2025 - Phase 3):**
- Initial qualitymetadata structure
- QA gates and degradation guidance

---

**End of AGENT-OUTPUT-SCHEMA.md v8.0.8**

Status: **FINAL**  
Quality Gates: **ALL PASS** ✅  
Backward Compatible: **YES** ✅  
Ready for Production: **YES** ✅
