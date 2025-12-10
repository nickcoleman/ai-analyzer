Version 1.1
Phase 2B Update – Assumption Types & Conviction Penalties Integrated
Date December 1, 2025
Status Production Ready – Assumption Validation Enhanced
Framework Stock Analysis Methodology v1.4.3 with Guardrails v1.4.4

---

TITLE PROMPT_ACTIONABLE_FUNDAMENTALS.md – STANDARDIZED FUNDAMENTAL TEMPLATE WITH 5-SECTION REPORT CONFORMANCE

## 1. Purpose and Role in the Framework

This file defines the **actionable fundamental analysis template** used to generate the **Fundamental Analysis** and supporting risk content in the standardized TURN 6 report.

It is responsible for:
- Structuring the **1-page fundamental section** of the report.
- Applying the **assumption-type taxonomy** (Binary, Durability, Macro, Execution).
- Translating assumption validation into **conviction penalties** that feed into overall conviction and position sizing.

It does **not**:
- Orchestrate the full 6-TURN process (handled by **ANALYZE.md v1.4.3**).
- Define the entire report skeleton (handled by **PROMPT_TURN6_ACTIONABLE.md v1.4.2** and **PROMPT_REPORT_OUTPUT_ACTIONABLE.md v1.4.1**).

All outputs derived from this file must be embedded into the **5-section structure** defined in PROMPT_TURN6_ACTIONABLE.md.

---

## 2. Mandatory Alignment with TURN 6 5-Section Structure

All reports that use this fundamental template **must conform** to the TURN 6 5-section structure:

1. **Section 1 – Executive Summary**
   - High-level thesis, recommendation, price targets, conviction, and event timing (if event-driven).
   - Fundamental content from this file appears here only in **condensed summary form** (e.g., top 3 drivers and headline metrics).

2. **Section 2 – Fundamental Analysis (Primary Consumer of this File)**
   - This file defines the full structure and required elements of Section 2.
   - Content must match the **1-page fundamental template** described below.

3. **Section 3 – Risk Analysis**
   - Uses the **assumption-type taxonomy** and conviction penalties defined here, in combination with **ANALYZE.md**.
   - Summarizes how unvalidated assumptions cap conviction and constrain position sizing.

4. **Section 4 – Technicals & Catalysts**
   - Primarily driven by technical frameworks, but must remain consistent with the catalysts and fundamental narrative defined here.

5. **Section 5 – Quality Control Gate**
   - Uses this file’s outputs to confirm:
     - Assumptions are disclosed and typed.
     - Conviction penalties are applied correctly.
     - Fundamental claims are consistent with data and valuation logic.

**Explicit Instruction:**
> All reports generated from this guidance must conform to the 5-section structure defined in PROMPT_TURN6_ACTIONABLE.md. This file only defines the content for the Fundamental and supporting risk/assumption portions of that structure, not an alternative layout.

---

## 3. 1-Page Fundamental Analysis Template (Section 2 Content)

Section 2 of the TURN 6 report must follow this structure:

### 3.1 Top 3 Investment Thesis Drivers with Assumption Types

For each driver:
- **Driver Name** – concise, specific statement.
- **Narrative (1–2 sentences)** – explain *why* this driver matters.
- **Supporting Facts (2+ metrics)** – with explicit numbers (e.g., FCF, growth, margins).
- **Confidence Level** – HIGH / MEDIUM / LOW.
- **Underlying Assumption Types** – choose from Binary / Durability / Macro / Execution.
- **Validation Status** – VALIDATED / PARTIALLY-VALIDATED / UNVALIDATED.
- **Conviction Impact** – penalty points when unvalidated (see Section 4).

Template (for each driver):

- DRIVER X – [Name]
  - Narrative: [1–2 sentences, specific and quantified].
  - Supporting Fact 1: [Metric with number].
  - Supporting Fact 2: [Metric with number].
  - Confidence Level: HIGH / MEDIUM / LOW.
  - Underlying Assumption Type(s): BINARY / DURABILITY / MACRO / EXECUTION.
  - Validation Status: VALIDATED / PARTIALLY-VALIDATED / UNVALIDATED.
  - Conviction Impact if UNVALIDATED: –15 / –10 / –12 / –8 points (per type).

**Vulnerability Rule:**
- Do not embed ticker-specific constants or rules here. All numbers must be treated as **inputs from live data**, not static rules.

### 3.2 Business Quality Assessment

Required:
- **Quality Score**: XX / 100.
- Profitability metrics (net margin, operating margin, ROIC).
- Growth metrics (revenue YoY, EBITDA YoY).
- Interpretive label: STRONG / ADEQUATE / WEAK or ACCELERATING / STABLE / DECELERATING.

### 3.3 Financial Health and Capital Efficiency

Required:
- Leverage metrics: Debt/EBITDA, interest coverage, leverage trend.
- Liquidity metrics: Current ratio, days cash on hand.
- Free cash flow: Level and growth, FCF conversion.
- Capital efficiency: ROE, asset turnover, cash return ratio.
- Interpretive labels: FORTRESS / ADEQUATE / STRESSED, EXCELLENT / GOOD / ADEQUATE.

### 3.4 Near-Term Catalysts (Must Align with CEV)

For each of at least 3 catalysts (6–12 month horizon):
- Event name.
- Date or date window.
- Impact range (e.g., +10% if positive, –5% if negative).
- **Date label** must be consistent with CEV output:
  - “Confirmed” vs. “Estimated” with source.

### 3.5 Key Risks to Thesis

At least 3 specific risk scenarios:
- Scenario description.
- Probability (HIGH / MEDIUM / LOW).
- Impact if occurs (dollar or % downside).
- Monitoring / mitigation plan.

### 3.6 Valuation Roadmap

Required elements:
- Current valuation: P/E, EV/EBITDA, FCF yield vs. history and peers.
- Target valuation for Base, Bull, Bear cases.
- Implied fair values for each scenario with rationale.

This structure must fit into **one report page** when combined, per PROMPT_REPORT_OUTPUT_ACTIONABLE.md.

---

## 4. Assumption Type Taxonomy and Conviction Penalties

This file provides the **canonical mapping** from assumption type to conviction penalty. ANALYZE.md uses this to compute overall conviction and apply conviction-cap tiers.

### 4.1 Binary Assumptions – Deal / Approval / On–Off

- Definition: Either true or false, no middle ground (e.g., deal closes, regulatory approval granted).
- Validation Requirement: **Definitive evidence** (binding agreements, court rulings, regulatory decisions).
- Conviction Impact if UNVALIDATED: **–15 points** (highest penalty).

### 4.2 Durability Assumptions – Cycle / Performance Persistence

- Definition: Assumes current favorable conditions persist (e.g., DOCSIS cycle remains strong, margins stay elevated).
- Validation Requirement: multi-quarter track record plus supportive external indicators.
- Conviction Impact if UNVALIDATED: **–10 points**.

### 4.3 Macro Assumptions – Rates / Credit / Commodities / FX

- Definition: Assumes macro environment remains within a range (e.g., rates stable, credit functional, commodity bands).
- Validation Requirement: cannot be fully validated; must be evaluated via **scenario analysis**.
- Conviction Impact if UNVALIDATED: **–12 points**.

### 4.4 Execution Assumptions – Company Hits Targets

- Definition: Company achieves operational or financial goals (revenue, margin, FCF targets).
- Validation Requirement: management track record and recent performance.
- Conviction Impact if UNVALIDATED: **–8 points** (lowest penalty).

### 4.5 Penalty Table Summary

- Binary: 0 / –7 / –15.
- Durability: 0 / –5 / –10.
- Macro: 0 / –6 / –12.
- Execution: 0 / –4 / –8.

These values must be used **only as methodology**, not as embedded example calculations. Any concrete conviction example must be labeled **[EXAMPLE – Not Operational]** in future revisions.

---

## 5. Integration with ANALYZE.md and Conviction-Cap Tiers

This file **supplies inputs** to the conviction framework in **ANALYZE.md v1.4.3**:

- Each driver’s assumption types and validation status feed into a cumulative penalty.
- ANALYZE.md applies these penalties to a baseline conviction to produce the final conviction score (0–100).
- The resulting score maps to a **conviction-cap tier** (e.g., 70–75 HIGH, 55–60 SPECULATIVE) with associated position sizing ranges from **PROMPT_POSITION_MANAGEMENT.md**.

Requirements:
- Fundamental section must expose enough detail (assumption types, status, penalties) for ANALYZE.md to reconstruct the conviction logic.
- Risk section (Section 3 of the report) must summarize the net effect: “Baseline conviction X, net penalty –Y, resulting conviction Z, maps to [tier] and position size range [A–B% of portfolio].”

---

## 6. Cross-File Consistency and Non-Duplication Rules

To avoid duplication and divergence:

- **Do not restate full methodology** already in ANALYZE.md (e.g., 6-TURN flow, Gate matrices).
- **Do not redefine** the 5-section TURN 6 structure.
  - Always refer to **PROMPT_TURN6_ACTIONABLE.md v1.4.2** as the **single source of truth** for report layout.
- Use this file only for:
  - Section 2 content template.
  - Assumption-type taxonomy.
  - Conviction penalty mapping.

Where additional detail is needed (e.g., detailed gate scoring, VaR methodology), delegate to:
- **ANALYZE.md v1.4.3** – methodology and compliance standards.
- **TURN-PROMPTS-v1.4.1.md** – execution prompts.
- **Quality-Control-Checklists.md v1.0** – pre-delivery checks.

---

## 7. Vulnerability Sweep Requirements

To ensure this file cannot be misread as containing hardcoded operational rules:

1. **No ticker-specific rules**
   - Remove any instruction that implies permanent behavior for a specific name.

2. **No embedded production examples without labeling**
   - Any worked examples **must** be clearly labeled **[EXAMPLE – Not Operational]**.
   - This version intentionally removes inline examples from the core template description. Example fragments in historical text should be treated as **illustrative only**, not binding.

3. **No conflicting report structures**
   - Any legacy references to alternative layouts (e.g., multi-section fundamental-only reports) are deprecated; TURN 6 5-section structure is authoritative.

---

## 8. Quality and Compliance Checklist for Fundamental Section

Before finalizing the report, verify that Section 2 satisfies:

- 3 thesis drivers, each with:
  - Quantified facts.
  - Confidence level.
  - Assumption types and validation status.
- Quality score and key profitability / growth metrics.
- Financial health and capital efficiency metrics with clear labels.
- At least 3 catalysts with CEV-aligned dates and impact ranges.
- At least 3 risks with probability and impact, plus monitoring plan.
- Valuation roadmap with current vs. Bull / Base / Bear fair values.
- All data is **current** per data-freshness rules in the broader framework.
- No examples masquerade as operational rules.

If any item fails, Section 2 is **non-compliant** and must be revised before the report passes the TURN 6 Quality Control Gate.

---

## 9. Version History (Footer Rule – All History at Bottom)

Version | Date       | Change Summary                                                                    | Status
--------|------------|------------------------------------------------------------------------------------|--------
1.0     | Nov 2025   | Initial actionable fundamentals template                                          | Superseded
1.1     | Dec 1 2025 | Assumption-type taxonomy and conviction penalties integrated                     | Active
1.1r1   | Dec 6 2025 | Explicit TURN 6 5-section alignment; Footer Rule compliance; vulnerability sweep; clarified cross-file roles with ANALYZE.md and PROMPT_TURN6_ACTIONABLE.md | Active

---

END OF PROMPT_ACTIONABLE_FUNDAMENTALS.md – STANDARDIZED FUNDAMENTAL TEMPLATE