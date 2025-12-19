# Analyst Technical Specification v8.0.4

**Status:** APPROVED BUILD-TO SPECIFICATION  
**Date:** December 7, 2025  
**Version:** 8.0.4  
**Unified v8.0 Standard**  
**Scope:** Comprehensive technical definition for the v8.0.0 Analyst System

---

## EXECUTIVE SUMMARY

The **Analyst System v8.0.0** is a hierarchical, event-driven investment decision framework.

### Core Hierarchy

1. **Portfolio Orchestrator** (Root): Controls allocation
2. **Market Analyst** (Governor): Controls riskregime
3. **Stock Analyst** (Worker): Generates conviction
4. **Expert Pool** (Service): Provides domain data
5. **QA Engineer** (Validator): Enforces compliance

---

## 1. SYSTEM OVERVIEW

All agents must output valid JSON matching the interface definitions in Section 2 (Interface Contracts).

### Architecture Principles

- **Hierarchical:** Clear parent-child authority (PO → MA, SA; MA → experts)
- **Event-driven:** Workflows triggered by user requests, watchlist cycles, or scheduled reviews
- **Stateless agents:** Each agent receives inputs, performs analysis, produces outputs
- **Strict contracts:** JSON schemas enforce type safety and enum conformance
- **Independent QA:** External validation separate from analysis authority

---

## 2. INTERFACE CONTRACTS - JSON SCHEMAS

### 2.1 Common Types

```json
{
  "RiskRegime": "NORMAL | ELEVATED | HIGH | CRISIS",
  "MarketRegime": "GROWTH | VALUE | DEFENSIVE | TRANSITION",
  "ConvictionTier": "HIGH | MEDIUM-HIGH | MEDIUM | MEDIUM-LOW | LOW",
  "Confidence": 0.0 to 1.0
}
```

### 2.2 Stock Analyst Output (SAOutput)

**Required Fields:**
```json
{
  "meta": {
    "symbol": "AAPL",
    "timestamp": "ISO8601",
    "version": "v8.0.0"
  },
  "thesis": {
    "convictiontier": "HIGH",
    "convictionscore": 0.85,
    "strategytype": "GROWTH | CONTRARIAN | VALUE",
    "targetprice": 250.00,
    "timehorizon": "3MONTHS | 6MONTHS | 9MONTHS | 12MONTHS | 18MONTHS | 24MONTHS"
  },
  "expertinputs": {
    "FMA": { "assessment": "...", "confidence": 0.8 },
    "IRM": { "assessment": "...", "confidence": 0.9 },
    "MSPA": { "assessment": "...", "confidence": 0.7 },
    "QMA": { "assessment": "...", "confidence": 0.8 }
  },
  "reasoning": {
    "weightingcontext": "BASELINE | EVENT-DRIVEN | MACRO-DRIVEN | CRISIS | TECHNICAL | SENTIMENT | MOMENTUMFADE | MACROSUBORDINATE",
    "deconflictinglogic": "...(min 50 chars)...",
    "escalationcheck": "PASS | FAILESCALATION | REQUIRESARCHITECTREVIEW"
  }
}
```

### 2.3 Market Analyst Output (MAOutput)

**Required Fields:**
```json
{
  "meta": {
    "timestamp": "ISO8601",
    "version": "v8.0.0"
  },
  "regime": {
    "marketregime": "GROWTH | VALUE | DEFENSIVE | TRANSITION",
    "riskregime": "NORMAL | ELEVATED | HIGH | CRISIS",
    "riskscore": 0.65
  },
  "sectors": {
    "leading": ["TECH", "INDUSTRIALS"],
    "lagging": ["UTILITIES"],
    "avoid": ["REALESTATE"]
  },
  "constraints": {
    "maxpositionsize": 0.03,
    "cashbuffermin": 0.10
  }
}
```

---

## 3. WORKFLOW DEFINITIONS

### 3.1 Stock Analyst Loop - The 6 Turns

**Trigger:** User Request or Watchlist Cycle

1. **TURN 1 - Context Ingestion:** Validate symbol, ingest MAOutput, initialize 6-expert pool (FMA, IRM, MSPA, QMA, MMA, MQAA)
2. **TURN 2 - Fundamentals Deep Dive:** FMA analysis on growth, margins, valuation; IRM risk outlook
3. **TURN 3 - Sentiment/Technical Deep Dive:** MSPA sentiment vs price; QMA momentum and technicals
4. **TURN 4 - De-Conflicting Conviction:** Apply weighting matrix, run de-conflicting protocol, calculate blended conviction
5. **TURN 5 - Sizing & Escalation:** Apply position sizing framework, check escalation thresholds
6. **TURN 6 - Report Generation:** Generate SAOutput JSON and formatted text report

### 3.2 Portfolio Orchestration Loop

**Trigger:** Receipt of valid SAOutput

1. **Ingest Inputs:** SAOutput Alpha and MAOutput Gates
2. **Apply Regime Gates:** Check if CONTRARIAN; apply standard gate else
3. **Check Constraints:** Cash available? Concentration limits?
4. **Execute:** Output final TradeInstruction

---

## 4. DECISION LOGIC CORE

### 4.1 Expert Weighting Matrix - 8 Contexts

| Context | FMA | IRM | MSPA | QMA |
|---------|-----|-----|------|-----|
| Baseline | 70 | 15 | 10 | 5 |
| Event-Driven | 55 | 25 | 10 | 10 |
| Macro-Driven | 55 | 30 | 10 | 5 |
| Crisis | 40 | 30 | 15 | 15 |
| Technical | 50 | 10 | 10 | 30 |
| Sentiment | 40 | 20 | 35 | 5 |
| Momentum Fade | 35 | 15 | 25 | 25 |
| Macro Subord. | 30 | 50 | 15 | 5 |

**Logic:** Select row based on Context, verify sum = 100.

### 4.2 De-Conflicting Protocol

**Steps:**
1. Gather conviction and confidence from all 4 experts
2. Weight score: `Σ(Expert.Conviction × Expert.Confidence × ContextWeight)`
3. Quality checks:
   - If AvgConfidence < 0.50 → Cap at MEDIUM tier
   - If StdDev(Confidence) > 0.30 → Flag DISPERSEDVIEWS
4. Map blended score to ConvictionTier

### 4.3 Position Sizing Framework

| Conviction Tier | Base Size |
|-----------------|-----------|
| HIGH | 5.0 |
| MEDIUM-HIGH | 3.0 |
| MEDIUM | 2.0 |
| MEDIUM-LOW | 1.0 |
| LOW | 0.5 |

**Risk Multiplier:**

| Risk Regime | Multiplier |
|-------------|-----------|
| NORMAL | 1.0x |
| ELEVATED | 0.6x |
| HIGH | 0.4x |
| CRISIS | 0.2x |

**Formula:** `Theoretical Size = Base Size × Risk Multiplier`

### 4.4 Escalation Thresholds - Mandatory

**Action:** Stop Workflow, Alert Master Architect if:
- IRM = CRISIS AND FMA conviction ≥ 0.70
- Valuation divergence ≥ 30%
- ≤2 experts agree on bullish conviction
- Portfolio VaR violation detected

---

## 5. QA COMPLIANCE RULES

### 5.1 Data Freshness Rules

- Market data (price/vol): Must be ≤1 hour old during session or Close of prior day
- News/sentiment: Must be ≤24 hours old
- Macro data: Must be latest release (e.g., last CPI print)
- Enforcement: QA checks `meta.timestamp` of inputs

### 5.2 Schema Validation

- QA Agent rejects any output with missing required fields (Section 2)
- QA Agent rejects any reasoning string < 50 chars
- Strict enum conformance: No typos, exact spelling match
- `additionalProperties: false` enforced at all levels

---

## Version History

| Version | Date | Status | Notes |
|---------|------|--------|-------|
| v8.0.4 | 2025-12-07 | APPROVED BUILD-TO SPECIFICATION | Current authoritative specification. Comprehensive technical definition for v8.0.0 Analyst System. Contains all logic, workflows, interfaces, and schemas required for implementation. All v8.0.0 implementations must reference this version. |
| v8.0.3-beta | Earlier | SUPERSEDED | Beta specification. Contained draft definitions later refined in v8.0.4. |
| v8.0.1-v8.0.2 | Earlier | SUPERSEDED | Earlier iterations. See v8.0.4 for unified authoritative specification. |

---

## Supersession Statement

This document (v8.0.4) is the **authoritative BUILD-TO SPECIFICATION** and supersedes:
- Analyst-Architectural-Design-v8.0.3-BETA.md
- All earlier versions (v8.0.1, v8.0.2, v8.0.3-beta)

**All agents and framework files must reference v8.0.4 as the source of truth.**

---

## Authority and Scope

### Source of Truth For:

- All v8.0.0 Analyst System interfaces (SAOutput, MAOutput, TradeInstruction schemas)
- All v8.0.0 decision logic and workflows (6-turn Stock Analyst, regime-gated Portfolio Orchestrator, etc.)
- All v8.0.0 expert weighting matrices, escalation rules, and constraints
- Quality control rules and data freshness standards

### Referenced By:

- Stock-Analyst.md v8.0.3
- Market-Analyst.md v8.0.0
- Portfolio-Orchestrator.md v8.0.0
- QUALITY-ASSURANCE-ENGINEER.md v8.0.3
- PROMPT-MMA.md v8.0.3
- PROMPT-MQAA.md v8.0.3
- All agents and supporting files

---
