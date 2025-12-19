# V8 ANALYST SYSTEM HANDBOOK
**Version:** 8.0.0-Production  
**Date:** December 8, 2025  
**Status:** VALIDATED & APPROVED  

---

## 1. SYSTEM ARCHITECTURE

The **V8 Analyst System** is an autonomous, hierarchically governed investment decision engine. It replaces flat consensus models with a rigorous **Authority-Based Logic Chain**:

`Market Data` → **Market Analyst** (Risk Regime) → **Stock Analyst** (Conviction) → **QA Engineer** (Validation) → **Portfolio Orchestrator** (Execution)

### 1.1 The Core Directives
1.  **Regime Sovereignty:** The *Market Analyst* defines the playing field. If the regime is HOSTILE, alpha signals are blocked (unless Contrarian).
2.  **Conviction-Based Sizing:** Position size is a function of conviction, throttled by risk.
3.  **Gatekeeper Validation:** No signal reaches the portfolio without passing the *QA Engineer's* strict schema and freshness checks.
4.  **Orchestrated Execution:** The *Portfolio Orchestrator* is the only agent authorized to write trade instructions.

---

## 2. AGENT SPECIFICATIONS

### 2.1 MARKET ANALYST (The Governor)
**Role:** Define the Market and Risk Regimes.
**Output:** `MA_Output`
**Key Logic:**
-   **Risk Regime:** NORMAL | ELEVATED | HIGH | CRISIS.
-   **Market Regime:** GROWTH | VALUE | DEFENSIVE | TRANSITION.
-   **Authority:** Sets global `max_position_size` and risk multipliers (e.g., Crisis = 0.2x).

### 2.2 STOCK ANALYST (The Alpha Generator)
**Role:** Analyze specific equities and generate high-conviction theses.
**Output:** `SA_Output`
**Key Logic:**
-   **Expert Council:** Synthesizes inputs from FMA (Fundamentals), IRM (Risk), MSPA (Sentiment), and QMA (Technicals).
-   **Conviction Tier:** HIGH (5%) | MEDIUM-HIGH (3%) | MEDIUM (2%) | LOW (0%).
-   **Weighting:** Dynamic weighting based on context (e.g., "Event-Driven" weights FMA/MSPA higher).

### 2.3 QA ENGINEER (The Gatekeeper)
**Role:** Validate data integrity and logic consistency.
**Output:** `QA_Report`
**Key Logic:**
-   **Schema Check:** Enforces strict JSON compliance.
-   **Freshness Check:** Rejects stale data (>1hr market data, >24hr news).
-   **Constraint Check:** Ensures position sizes match conviction/risk logic.

### 2.4 PORTFOLIO ORCHESTRATOR (The Executive)
**Role:** Synthesize inputs into final trade instructions.
**Output:** `Trade_Instruction`
**Key Logic:**
-   **Regime-Gated Decision Tree:**
    -   *Supportive Regime:* Full Size.
    -   *Negative Regime:* Scaled Down (0.6x).
    -   *Hostile Regime:* Blocked (0.0x) UNLESS Strategy is CONTRARIAN (Pilot 0.2x).
-   **Sizing Engine:** `Final_Size = Base_Conviction * Risk_Mult * Regime_Mult`.

---

## 3. DATA CONTRACTS (SCHEMAS)

### 3.1 SA_Output / MA_Output Schema
*Ref: `AGENT-OUTPUT-SCHEMA-v8.0.0-alpha.md`*
Defines the rigorous JSON structure for Analyst-to-Orchestrator communication, enforcing Enums for all regimes and tiers.

### 3.2 Trade Instruction Schema
*Ref: `Trade-Instruction-Schema.json`*
Defines the exact output format for the execution layer, including:
-   `action`: BUY | SELL | HOLD | BLOCKED
-   `quantity_pct`: Precise allocation (0.000 to 0.500)
-   `reasoning`: Mandatory "Rationale" field (>100 chars)

---

## 4. OPERATIONAL WORKFLOW (The Loop)

1.  **Initialization:**
    -   Market Analyst scans macro environment → Generates `MA_Output`.
2.  **Analysis:**
    -   Stock Analyst ingests `MA_Output` + Ticker Data → Generates `SA_Output`.
3.  **Validation:**
    -   QA Engineer ingests `SA` + `MA` outputs → Generates `QA_Report`.
    -   *If QA Fail:* Stop & Regenerate.
4.  **Orchestration:**
    -   Portfolio Orchestrator ingests `SA` + `MA` + `QA` + `Portfolio_State`.
    -   Applies Regime-Gated Tree.
    -   Generates `Trade_Instruction`.
5.  **Execution:**
    -   Trade Instruction sent to Order Management System (OMS).

---

## 5. VALIDATION HISTORY

The V8 System has been validated against the following scenarios:
1.  **Baseline Growth (AAPL):** Correctly sized defensively (1.8%) in an ELEVATED risk regime.
2.  **Fundamental Trap (PARR):** Correctly identified earnings conflict and held (0.0%) despite cheap valuation.
3.  **High-Vol Pivot (HUT):** Correctly authorized maximum allowable risk (3.0%) for a high-conviction pivot story, verifying the "Risk-Managed Aggression" capability.

---

**APPROVED BY:** Master Architect
**DEPLOYMENT STATUS:** READY
