# INTEGRATION-GUIDE-Portfolio-Analyst-Perplexity.md
**Version:** 1.0  
**Status:** IMPLEMENTATION GUIDE  
**Date:** December 25, 2025  
**Purpose:** How to integrate Perplexity Adapter into Portfolio-Analyst.md execution

---

## QUICK START

### Files Needed
1. **Portfolio-Analyst.md** (unchanged - remains generic)
2. **Portfolio-Analyst-PERPLEXITY-ADAPTER.md** (new - this is the adapter)
3. **Portfolio-Analyst-TOOL-MAPPING.md** (new - reference guide for analysts)

### Three-Layer Architecture

```
Layer 1: Generic Framework (Portfolio-Analyst.md)
         ↓
Layer 2: Perplexity Adapter (Portfolio-Analyst-PERPLEXITY-ADAPTER.md)
         ↓
Layer 3: Perplexity Tools (finance_tickers_lookup, search_web, etc.)
```

---

## INTEGRATION STEPS

### Step 1: Load the Adapter in Your Script

**Before executing Portfolio-Analyst.md TIER 0A, initialize the adapter:**

```python
# At the top of your analysis script
from portfolio_analyst_perplexity_adapter import PortfolioAnalystAdapter

# Initialize adapter (once per analysis session)
adapter = PortfolioAnalystAdapter()

print(f"✅ Perplexity Adapter Initialized")
print(f"   Framework: {adapter.config.FRAMEWORK}")
print(f"   Environment: {adapter.config.ENVIRONMENT}")
print(f"   Adapter Version: {adapter.config.ADAPTER_VERSION}")
```

---

### Step 2: Replace Portfolio-Analyst.md Function Calls

**In Portfolio-Analyst.md TIER 0A, instead of:**
```python
# ❌ OLD (Abstract - doesn't work in any environment)
result = financetickerlookup(ticker="NEM", company_name="Newmont")
```

**Use the adapter:**
```python
# ✅ NEW (Perplexity implementation)
result = adapter.financetickerlookup(ticker="NEM", company_name="Newmont")
```

---

### Step 3: Execute Each Tier with Adapter

#### **TIER 0A: Company Identity Verification**

**Generic framework says:**
```python
# From Portfolio-Analyst.md TIER 0A
Call financetickerlookup(ticker, company_name)
```

**Perplexity execution:**
```python
# Perplexity adapter call
company_data = adapter.financetickerlookup(
    ticker="NEM",
    company_name="Newmont Corporation"
)

# Result structure:
# {
#     'status': 'SUCCESS',
#     'ticker': 'NEM',
#     'company': 'Newmont Corporation',
#     'exchange': 'NYSE',
#     'source': 'finance_tickers_lookup',
#     'risk_signals': ['NONE'],
#     'timestamp': '2025-12-25T...'
# }

# Check for risk signals
if company_data['risk_signals']:
    print(f"⚠️  Risk signals detected: {company_data['risk_signals']}")
```

---

#### **TIER 1: Trading Status Verification**

**Generic framework says:**
```python
# From Portfolio-Analyst.md TIER 1
searchweb([TICKER] trading halt delisted inactive)
```

**Perplexity execution:**
```python
# Perplexity adapter call
trading_status = adapter.searchweb_ticker_status(ticker="NEM")

# Result structure:
# {
#     'status': 'ACTIVE',
#     'details': 'Trading normally on NYSE',
#     'source': 'search_web',
#     'risk_signal': None,
#     'timestamp': '2025-12-25T...'
# }

# Handle status
if trading_status['status'] != 'ACTIVE':
    print(f"🛑 Trading Status Issue: {trading_status['details']}")
```

---

#### **TIER 2: Sector Classification**

**Generic framework says:**
```python
# From Portfolio-Analyst.md TIER 2
searchweb([COMPANY_NAME] GICS sector classification industry)
```

**Perplexity execution:**
```python
# Perplexity adapter call
sector_info = adapter.searchweb_gics_sector(
    company_name="Newmont Corporation"
)

# Result structure:
# {
#     'sector': 'Materials',
#     'sub_sector': 'Metals & Mining',
#     'industry': 'Gold',
#     'source': 'search_web',
#     'confidence': 0.95,
#     'timestamp': '2025-12-25T...'
# }

# Validate sector
if sector_info['confidence'] < 0.8:
    print(f"⚠️  Low confidence sector classification: {sector_info['confidence']}")
```

---

#### **TIER 3.5: Historical Price Data**

**Generic framework says:**
```python
# From Portfolio-Analyst.md TIER 3.5
Finnhub GET /stock/candle for 250-day OHLC data
```

**Perplexity execution:**
```python
# Perplexity adapter call
ohlc_data = adapter.finnhub_stock_candle(
    ticker="NEM",
    company_name="Newmont Corporation",
    days=250
)

# Result structure:
# {
#     'ticker': 'NEM',
#     'dates': ['2025-12-24', '2025-12-23', ...],
#     'opens': [47.5, 47.3, ...],
#     'highs': [48.2, 47.8, ...],
#     'lows': [46.8, 46.9, ...],
#     'closes': [47.9, 47.4, ...],
#     'volumes': [3500000, 3200000, ...],
#     'source': 'finance_price_histories',
#     'record_count': 250,
#     'timestamp': '2025-12-25T...'
# }

# Validate data
if ohlc_data['record_count'] < 200:
    print(f"⚠️  Insufficient price history: {ohlc_data['record_count']} days")
else:
    print(f"✅ Price history loaded: {ohlc_data['record_count']} trading days")
```

---

### Step 4: Review Audit Trail

**After all tiers complete, print the audit trail:**

```python
# Print complete audit trail of all tool calls
adapter.print_audit_trail()

# Output example:
# ======================================================================
# TOOL CALL AUDIT TRAIL - Perplexity Adapter
# ======================================================================
#
# Call #1
#   Timestamp: 2025-12-25T20:15:45.123456
#   Tool: finance_tickers_lookup
#   Purpose: TIER 0A - Company Identity Verification
#   Parameters: {
#       "ticker_symbols": ["NEM"],
#       "ticker_names": ["Newmont Corporation"]
#   }
#
# Call #2
#   Timestamp: 2025-12-25T20:15:46.234567
#   Tool: search_web
#   Purpose: TIER 1 - Trading Status Verification
#   Parameters: {
#       "queries": ["NEM trading halt", "NEM delisted", "NEM inactive status"]
#   }
# ...
# ======================================================================
# Total Tool Calls: 5
# ======================================================================
```

---

## COMPLETE EXAMPLE: Analyze NEM

### Full Workflow

```python
#!/usr/bin/env python3
"""
Complete Portfolio-Analyst.md execution with Perplexity Adapter
Analyzing single ticker: NEM (Newmont Corporation)
"""

from portfolio_analyst_perplexity_adapter import PortfolioAnalystAdapter
from datetime import datetime

# ============================================================
# INITIALIZATION
# ============================================================

print("\n" + "="*70)
print("PORTFOLIO-ANALYST v8.4.0 - PERPLEXITY EXECUTION")
print("="*70)
print(f"Start Time: {datetime.now().isoformat()}\n")

# Initialize adapter (bridges Portfolio-Analyst.md to Perplexity tools)
adapter = PortfolioAnalystAdapter()
print(f"✅ Adapter initialized: {adapter.config.ENVIRONMENT}")

# ============================================================
# INPUT DATA (from portfolio CSV or manual entry)
# ============================================================

ticker = "NEM"
company_name_provided = "Newmont Mining"  # From portfolio data
quantity = 100
price = 47.90
value = 4790.00

print(f"\n📊 Position to Validate:")
print(f"   Ticker: {ticker}")
print(f"   Company (provided): {company_name_provided}")
print(f"   Quantity: {quantity} shares")
print(f"   Price: ${price}")
print(f"   Value: ${value}\n")

# ============================================================
# TIER 0A: COMPANY IDENTITY VERIFICATION
# ============================================================

print("▶️  TIER 0A: Company Identity Verification")
print("-" * 70)

company_data = adapter.financetickerlookup(
    ticker=ticker,
    company_name=company_name_provided
)

print(f"Tool Called: finance_tickers_lookup")
print(f"Result Status: {company_data['status']}")

if company_data['status'] == 'SUCCESS':
    print(f"  ✅ Ticker: {company_data['ticker']}")
    print(f"  ✅ Company: {company_data['company']}")
    print(f"  ✅ Exchange: {company_data['exchange']}")
    
    if company_data['risk_signals']:
        print(f"  ⚠️  Risk Signals: {', '.join(company_data['risk_signals'])}")
    else:
        print(f"  ✅ Risk Level: LOW (no signals)")
else:
    print(f"  ❌ FAILED to verify ticker")
    exit(1)

# ============================================================
# TIER 1: TRADING STATUS VERIFICATION
# ============================================================

print("\n▶️  TIER 1: Trading Status Verification")
print("-" * 70)

trading_status = adapter.searchweb_ticker_status(ticker=ticker)

print(f"Tool Called: search_web")
print(f"Result Status: {trading_status['status']}")
print(f"Details: {trading_status['details']}")

if trading_status['status'] != 'ACTIVE':
    print(f"❌ ISSUE: {trading_status['details']}")
    exit(1)
else:
    print(f"✅ Ticker is actively trading")

# ============================================================
# TIER 2: SECTOR CLASSIFICATION
# ============================================================

print("\n▶️  TIER 2: Sector Classification")
print("-" * 70)

sector_info = adapter.searchweb_gics_sector(
    company_name=company_data['company']
)

print(f"Tool Called: search_web")
print(f"Official Sector: {sector_info['sector']}")
print(f"Sub-sector: {sector_info['sub_sector']}")
print(f"Industry: {sector_info['industry']}")
print(f"Confidence: {sector_info['confidence']:.1%}")

# ============================================================
# TIER 3.5: HISTORICAL PRICE DATA
# ============================================================

print("\n▶️  TIER 3.5: Historical Price Data (For Technical Analysis)")
print("-" * 70)

ohlc_data = adapter.finnhub_stock_candle(
    ticker=ticker,
    company_name=company_data['company'],
    days=250
)

print(f"Tool Called: finance_price_histories")
print(f"Data Range: {ohlc_data['dates'][0]} to {ohlc_data['dates'][-1]}")
print(f"Records: {ohlc_data['record_count']} trading days")

# Example: Calculate simple 50-day SMA for technical analysis
if len(ohlc_data['closes']) >= 50:
    sma_50 = sum(ohlc_data['closes'][-50:]) / 50
    current_price = ohlc_data['closes'][-1]
    print(f"Current Price: ${current_price:.2f}")
    print(f"50-Day SMA: ${sma_50:.2f}")
    print(f"Signal: {'Bullish' if current_price > sma_50 else 'Bearish'} (price vs SMA)")

# ============================================================
# AUDIT TRAIL
# ============================================================

print("\n▶️  AUDIT TRAIL")
print("-" * 70)

audit_trail = adapter.get_audit_trail()
print(f"Total Tool Calls Made: {len(audit_trail)}\n")

for i, call in enumerate(audit_trail, 1):
    print(f"Call #{i}: {call['tool']}")
    print(f"  Purpose: {call['purpose']}")
    print(f"  Timestamp: {call['timestamp']}")

# ============================================================
# CACHE STATUS
# ============================================================

print("\n▶️  CACHE STATUS")
print("-" * 70)

cache_status = adapter.get_cache_status()
print(f"Cached Items: {cache_status['cached_items']}")
print(f"Items: {', '.join(cache_status['items'])}\n")

# ============================================================
# SUMMARY
# ============================================================

print("="*70)
print("ANALYSIS COMPLETE - SUMMARY")
print("="*70)

print(f"""
Position: {company_data['company']} ({ticker})
Status: ✅ VALIDATED

Company Identity:
  - Ticker: {company_data['ticker']}
  - Official Name: {company_data['company']}
  - Exchange: {company_data['exchange']}

Trading:
  - Status: {trading_status['status']}
  
Classification:
  - Sector (GICS): {sector_info['sector']}
  - Sub-sector: {sector_info['sub_sector']}
  - Industry: {sector_info['industry']}

Technical:
  - Data Points: {ohlc_data['record_count']} trading days
  - Current Price: ${ohlc_data['closes'][-1]:.2f}

Risk Signals: {len(company_data['risk_signals'])} {'detected' if company_data['risk_signals'] else 'none detected'}
Tool Calls: {len(audit_trail)}

Next Steps:
✅ Position can proceed to Portfolio Sector Allocation
✅ Risk profile is LOW (no delisting, no ticker reuse)
✅ Technical analysis data ready for Tier 3.5 indicators
""")

print(f"End Time: {datetime.now().isoformat()}")
print("="*70 + "\n")
```

---

## ERROR HANDLING

### Adapter Error Recovery

```python
# If a tool call fails, adapter returns error structure:
result = adapter.financetickerlookup("BADTICKER", "Unknown Company")

# Result:
# {
#     'status': 'FAIL',
#     'error': 'Ticker not found',
#     'ticker': 'BADTICKER',
#     'source': 'finance_tickers_lookup'
# }

# Your code should check status:
if result['status'] != 'SUCCESS':
    print(f"Error: {result.get('error', 'Unknown error')}")
    # Handle error: skip ticker, escalate, etc.
```

---

## AVOIDING THE DEFAULT search_web FALLBACK

### ❌ What NOT to do:

```python
# DON'T do this - it bypasses anti-hallucination guardrails
result = search_web("NEM ticker company Newmont")
# This returns unstructured training data, misses delisting detection
```

### ✅ What TO do:

```python
# DO use the adapter
result = adapter.financetickerlookup("NEM", "Newmont Corporation")
# This uses finance_tickers_lookup for LIVE company identity verification
```

**Why this matters:**
- `search_web` might return "Newmont Mining" (old training data)
- `finance_tickers_lookup` returns current official name
- No risk flags generated without proper tool
- Delisting detection disabled

---

## CHECKLIST FOR ANALYSTS

Before executing Portfolio-Analyst.md in Perplexity:

- [ ] Portfolio-Analyst.md is available (unchanged)
- [ ] Portfolio-Analyst-PERPLEXITY-ADAPTER.md is imported
- [ ] Adapter is initialized: `adapter = PortfolioAnalystAdapter()`
- [ ] All tier 0A calls use `adapter.financetickerlookup()`
- [ ] All tier 1 calls use `adapter.searchweb_ticker_status()`
- [ ] All tier 2 calls use `adapter.searchweb_gics_sector()`
- [ ] All tier 3.5 calls use `adapter.finnhub_stock_candle()`
- [ ] Audit trail is reviewed after execution: `adapter.print_audit_trail()`
- [ ] No direct `search_web()` calls for ticker validation
- [ ] Cache status is checked: `adapter.get_cache_status()`

---

## DEPLOYMENT STEPS

### Step 1: Copy Files
```bash
# Copy adapter file to your project
cp Portfolio-Analyst-PERPLEXITY-ADAPTER.md ./portfolio_analyst_perplexity_adapter.py
cp Portfolio-Analyst-TOOL-MAPPING.md ./
cp INTEGRATION-GUIDE-Portfolio-Analyst-Perplexity.md ./
```

### Step 2: Import in Your Script
```python
from portfolio_analyst_perplexity_adapter import PortfolioAnalystAdapter
```

### Step 3: Initialize Before Execution
```python
adapter = PortfolioAnalystAdapter()
```

### Step 4: Use in Place of Abstract Functions
```python
# All tier operations now go through adapter
company_data = adapter.financetickerlookup(ticker, name)
trading_status = adapter.searchweb_ticker_status(ticker)
sector_info = adapter.searchweb_gics_sector(company_name)
ohlc_data = adapter.finnhub_stock_candle(ticker, company_name)
```

### Step 5: Review Results
```python
adapter.print_audit_trail()
```

---

## TROUBLESHOOTING

### "I'm getting search_web results instead of structured data"

**Cause:** You're calling `search_web()` directly instead of using adapter  
**Solution:** Use `adapter.searchweb_gics_sector()` instead

### "Adapter returns 'status': 'FAIL'"

**Cause:** Tool call failed (bad ticker, network issue, etc.)  
**Solution:** Check error message and validate input data

### "Cache is full"

**Cause:** In-memory cache has accumulated many entries  
**Solution:** Normal behavior; cache will be cleared when script ends

### "Audit trail is empty"

**Cause:** Adapter not being used for tool calls  
**Solution:** Ensure you're calling `adapter.method()`, not direct tool calls

---

## NEXT STEPS

1. **For Developers:**
   - Convert Portfolio-Analyst-PERPLEXITY-ADAPTER.md pseudocode to actual Python
   - Implement actual Perplexity tool calls (replace template calls)
   - Add error handling for tool failures
   - Test with sample portfolios

2. **For Analysts:**
   - Use integration guide to run Portfolio-Analyst.md
   - Call adapter methods instead of abstract functions
   - Review audit trail after each run
   - Report any tool failures

3. **For QA:**
   - Test with known edge cases (SHLD, delisted tickers, OTC stocks)
   - Verify ticker reuse detection works
   - Validate sector classification matches official GICS
   - Check audit trail completeness

---

**Document:** INTEGRATION-GUIDE-Portfolio-Analyst-Perplexity.md v1.0  
**Status:** READY FOR IMPLEMENTATION  
**Audience:** Developers, analysts, QA teams  
**Last Updated:** December 25, 2025
