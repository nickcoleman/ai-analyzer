# PROMPT_KICKOFF.md
## Analysis Kickoff & Phase 0 Verification v6.5 (Reframed)

**Version:** v6.5 (Reframed Nov 24, 2025)  
**Purpose:** Analysis kickoff and Phase 0 verification

---

## PHASE 0: FOUNDATION VERIFICATION

### Part 1: Market Verification

**Required Checks:**
- [ ] US exchange (NASDAQ, NYSE, AMEX, or OTC)
- [ ] USD currency
- [ ] SEC reporting requirement met
- [ ] Market data available (real-time or EOD)

**Status:** PASS or STOP (cannot proceed if exchange is non-US or currency non-USD)

---

### Part 2: Data Availability Check

**12-Core Data Items (require ≥95% completeness):**
1. Stock price (current and 5-year history)
2. Market capitalization
3. Shares outstanding
4. Revenue (annual, last 3 years)
5. Net income (annual, last 3 years)
6. Free cash flow (annual, last 3 years)
7. Debt levels
8. Equity levels
9. Operating margins
10. Return on equity
11. Competitive positioning
12. Industry/sector classification

**Status:** PASS if ≥95% of items available; HOLD if gaps identified

---

### Part 3: Market Regime Identification

**Determine current regime:**
- **Growth Regime:** Accommodative policy, positive earnings growth
- **Stagnation Regime:** Limited growth, policy neutral
- **Recovery Regime:** Coming out of recession, growth accelerating
- **Risk-Off Regime:** Defensive positioning, volatility elevated
- **Risk-On Regime:** Aggressive positioning, risk appetite high

**Output:** Use regime in TURN 1 analysis to calibrate expectations

---

### Part 4: Kickoff Approval

**Go/No-Go Decision:**
- ✓ GO: All 3 checks PASS → Proceed to TURN 1
- ⚠️ HOLD: Data gaps <5% but identifiable → Proceed with caution, note limitations
- ✗ STOP: Non-US exchange or non-USD currency → Cannot proceed

---

**END OF PROMPT_KICKOFF.md (REFRAMED)**

**Status:** Tested and Verified  
**Last Updated:** November 24, 2025
