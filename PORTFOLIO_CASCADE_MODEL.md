# PORTFOLIO_CASCADE_MODEL.md
## Portfolio-Level Risk Management v6.5 (Reframed)

**Version:** v6.5 (Reframed Nov 24, 2025)  
**Purpose:** Portfolio-level risk management and position sizing

---

## PORTFOLIO CASCADE MODEL

### Part 1: Concentration Risk Assessment

**Methodology:**
- Calculate Herfindahl Index: Sum of (weight^2) for all positions
- Target: ≤0.10 (no single position >10%, no extreme concentration)
- Red flag: >0.15 (excessive concentration risk)

**Example Portfolio (4 positions):**
- Position A: 4% (4^2 = 16)
- Position B: 3% (3^2 = 9)
- Position C: 2% (2^2 = 4)
- Position D: 2% (2^2 = 4)
- Herfindahl: (16+9+4+4)/10000 = 0.0033 ✓ (low concentration)

---

### Part 2: Correlation Risk Assessment

**Methodology:**
- Calculate 5-year correlation matrix between all holdings
- Flag pairs with correlation >0.80 (redundant exposure)
- Reduce correlated positions if concentration risk identified

**Output:** Avoid two positions with same risk drivers

---

### Part 3: Subsector Cascade Allocation

**Dynamic allocation based on regime:**

**Growth Regime (Accommodative):**
- Growth subsector: 70% allocation
- Value subsector: 20% allocation
- Defensive subsector: 10% allocation

**Risk-Off Regime (Defensive):**
- Growth subsector: 20% allocation
- Value subsector: 30% allocation
- Defensive subsector: 50% allocation

**Rationale:** Match allocation to market regime for optimal risk-adjusted returns

---

### Part 4: Scenario Cascade Testing

**Portfolio-level scenarios:**
- **Market Crash (-35%):** How does portfolio perform? (target: -20% or better)
- **Sector Collapse (-50%):** Sector concentration impact? (target: -15% or better)
- **Single-name Failure (-75%):** Impact to portfolio? (target: -3% or better)

**Output:** Ensure portfolio positioned defensively enough for stress scenarios

---

**END OF PORTFOLIO_CASCADE_MODEL.md (REFRAMED)**

**Status:** Tested and Verified  
**Last Updated:** November 24, 2025
