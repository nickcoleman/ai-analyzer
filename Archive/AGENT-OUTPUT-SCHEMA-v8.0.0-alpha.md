# AGENT OUTPUT SCHEMA v8.0.0-alpha

**Status:** DELIVERABLE - Directive C  
**Date:** December 7, 2025  
**Version:** v8.0.0-alpha  
**Scope:** JSON Schema definitions for SAOutput and MAOutput per Analyst-Technical-Specification-v8.0.4.md Section 2

---

## SCHEMA DEFINITION

### Root Schema

```json
{
  "$schema": "https://json-schema.org/draft-2020-12/schema",
  "$id": "urn:analyst-system:v8.0.0:AGENT-OUTPUT-SCHEMA",
  "title": "Analyst System v8.0.0 Output Schema",
  "type": "object",
  "definitions": {
    "RiskRegime": {
      "type": "string",
      "enum": ["NORMAL", "ELEVATED", "HIGH", "CRISIS"],
      "description": "System-wide risk environment classification."
    },
    "MarketRegime": {
      "type": "string",
      "enum": ["GROWTH", "VALUE", "DEFENSIVE", "TRANSITION"],
      "description": "Dominant market style/leadership regime."
    },
    "ConvictionTier": {
      "type": "string",
      "enum": ["HIGH", "MEDIUM-HIGH", "MEDIUM", "MEDIUM-LOW", "LOW"],
      "description": "Discrete conviction tier derived from blended expert scores."
    },
    "Confidence": {
      "type": "number",
      "minimum": 0.0,
      "maximum": 1.0,
      "description": "Scalar confidence value from 0.0 to 1.0."
    },
    "SAOutput": {
      "type": "object",
      "description": "Stock Analyst v8.0.0 output contract (Section 2.2)",
      "properties": {
        "meta": {
          "type": "object",
          "description": "Metadata for the stock-level thesis",
          "properties": {
            "symbol": {
              "type": "string",
              "description": "Ticker symbol, e.g., AAPL"
            },
            "timestamp": {
              "type": "string",
              "format": "date-time",
              "description": "ISO8601 generation timestamp"
            },
            "version": {
              "type": "string",
              "const": "v8.0.0",
              "description": "Schema contract version"
            }
          },
          "required": ["symbol", "timestamp", "version"],
          "additionalProperties": false
        },
        "thesis": {
          "type": "object",
          "description": "High-level stock thesis and targeting",
          "properties": {
            "convictiontier": { "$ref": "#/definitions/ConvictionTier" },
            "convictionscore": {
              "type": "number",
              "minimum": 0.0,
              "maximum": 1.0,
              "description": "Continuous blended conviction score mapped into ConvictionTier"
            },
            "strategytype": {
              "type": "string",
              "enum": ["GROWTH", "CONTRARIAN", "VALUE"],
              "description": "Primary strategy classification for this signal"
            },
            "targetprice": {
              "type": "number",
              "description": "Numeric target price in quote currency"
            },
            "timehorizon": {
              "type": "string",
              "enum": ["3MONTHS", "6MONTHS", "9MONTHS", "12MONTHS", "18MONTHS", "24MONTHS"],
              "description": "Primary investment time horizon"
            }
          },
          "required": ["convictiontier", "convictionscore", "strategytype", "targetprice", "timehorizon"],
          "additionalProperties": false
        },
        "expertinputs": {
          "type": "object",
          "description": "Structured inputs from the four expert agents",
          "properties": {
            "FMA": {
              "type": "object",
              "description": "Fundamental-Macro Analyst input",
              "properties": {
                "assessment": {
                  "type": "string",
                  "description": "Natural language summary of FMA view"
                },
                "confidence": { "$ref": "#/definitions/Confidence" }
              },
              "required": ["assessment", "confidence"],
              "additionalProperties": false
            },
            "IRM": {
              "type": "object",
              "description": "Integrated Risk Manager input",
              "properties": {
                "assessment": {
                  "type": "string",
                  "description": "Natural language summary of IRM view"
                },
                "confidence": { "$ref": "#/definitions/Confidence" }
              },
              "required": ["assessment", "confidence"],
              "additionalProperties": false
            },
            "MSPA": {
              "type": "object",
              "description": "Macro Sentiment Positioning Analyst input",
              "properties": {
                "assessment": {
                  "type": "string",
                  "description": "Natural language summary of MSPA view"
                },
                "confidence": { "$ref": "#/definitions/Confidence" }
              },
              "required": ["assessment", "confidence"],
              "additionalProperties": false
            },
            "QMA": {
              "type": "object",
              "description": "Quantitative Technical Analyst input",
              "properties": {
                "assessment": {
                  "type": "string",
                  "description": "Natural language summary of QMA view"
                },
                "confidence": { "$ref": "#/definitions/Confidence" }
              },
              "required": ["assessment", "confidence"],
              "additionalProperties": false
            }
          },
          "required": ["FMA", "IRM", "MSPA", "QMA"],
          "additionalProperties": false
        },
        "reasoning": {
          "type": "object",
          "description": "Key reasoning artifacts and QA-relevant flags",
          "properties": {
            "weightingcontext": {
              "type": "string",
              "enum": ["BASELINE", "EVENT-DRIVEN", "MACRO-DRIVEN", "CRISIS", "TECHNICAL", "SENTIMENT", "MOMENTUMFADE", "MACROSUBORDINATE"],
              "description": "Active weighting matrix row used in decision logic (Section 4.1)"
            },
            "deconflictinglogic": {
              "type": "string",
              "minLength": 50,
              "description": "Natural language description of de-conflicting protocol and key trade-offs"
            },
            "escalationcheck": {
              "type": "string",
              "enum": ["PASS", "FAILESCALATION", "REQUIRESARCHITECTREVIEW"],
              "description": "Result of escalation threshold checks (Section 4.5)"
            }
          },
          "required": ["weightingcontext", "deconflictinglogic", "escalationcheck"],
          "additionalProperties": false
        }
      },
      "required": ["meta", "thesis", "expertinputs", "reasoning"],
      "additionalProperties": false
    },
    "MAOutput": {
      "type": "object",
      "description": "Market Analyst v8.0.0 output contract (Section 2.3)",
      "properties": {
        "meta": {
          "type": "object",
          "description": "Metadata for market regime evaluation",
          "properties": {
            "timestamp": {
              "type": "string",
              "format": "date-time",
              "description": "ISO8601 generation timestamp"
            },
            "version": {
              "type": "string",
              "const": "v8.0.0",
              "description": "Schema contract version"
            }
          },
          "required": ["timestamp", "version"],
          "additionalProperties": false
        },
        "regime": {
          "type": "object",
          "description": "Current market and risk regime classification",
          "properties": {
            "marketregime": { "$ref": "#/definitions/MarketRegime" },
            "riskregime": { "$ref": "#/definitions/RiskRegime" },
            "riskscore": {
              "type": "number",
              "minimum": 0.0,
              "maximum": 1.0,
              "description": "Continuous risk score used by Portfolio Orchestrator"
            }
          },
          "required": ["marketregime", "riskregime", "riskscore"],
          "additionalProperties": false
        },
        "sectors": {
          "type": "object",
          "description": "Sector leadership and avoidance guidance",
          "properties": {
            "leading": {
              "type": "array",
              "items": { "type": "string" },
              "description": "Sectors with leadership overweight bias"
            },
            "lagging": {
              "type": "array",
              "items": { "type": "string" },
              "description": "Sectors underperforming (underweight bias)"
            },
            "avoid": {
              "type": "array",
              "items": { "type": "string" },
              "description": "Sectors to avoid or block new exposure"
            }
          },
          "required": ["leading", "lagging", "avoid"],
          "additionalProperties": false
        },
        "constraints": {
          "type": "object",
          "description": "Global risk and allocation constraints for Portfolio Orchestrator",
          "properties": {
            "maxpositionsize": {
              "type": "number",
              "minimum": 0.0,
              "maximum": 1.0,
              "description": "Maximum single-name position size as fraction of total portfolio NAV (e.g., 0.03 = 3%)"
            },
            "cashbuffermin": {
              "type": "number",
              "minimum": 0.0,
              "maximum": 1.0,
              "description": "Minimum cash buffer fraction (e.g., 0.10 = 10%)"
            }
          },
          "required": ["maxpositionsize", "cashbuffermin"],
          "additionalProperties": false
        }
      },
      "required": ["meta", "regime", "sectors", "constraints"],
      "additionalProperties": false
    }
  },
  "properties": {
    "SAOutput": { "$ref": "#/definitions/SAOutput" },
    "MAOutput": { "$ref": "#/definitions/MAOutput" }
  },
  "additionalProperties": false
}
```

---

## Implementation Standards

**Strict Conformance Required:**
- **JSON Schema Draft 2020-12** conformance
- **Enumerations** exhaustively defined—any value outside enum set will fail validation
- **`additionalProperties: false`** enforced at all levels to prevent schema drift
- **Timestamp fields** use ISO8601 format with strict validation
- **All required fields and types** explicitly declared to ensure completeness
- **No optional fields** without documented rationale

---

## Authority and Scope

### Source of Truth For:

- SAOutput interface contract (Stock Analyst output)
- MAOutput interface contract (Market Analyst output)
- All enum definitions used across v8.0.0 framework

### Referenced By:

- Stock-Analyst.md v8.0.3
- Market-Analyst.md v8.0.0
- Portfolio-Orchestrator.md v8.0.0
- QUALITY-ASSURANCE-ENGINEER.md v8.0.3
- All JSON generators and validators

**Authority:** Analyst-Technical-Specification.md v8.0.4 Section 2

---

## Version History

| Version | Date | Status | Notes |
|---------|------|--------|-------|
| v8.0.0-alpha | 2025-12-07 | DELIVERABLE - Directive C | Canonical JSON schema definitions for SAOutput and MAOutput per Analyst-Technical-Specification v8.0.4 Section 2. Interface contracts. Strict enum conformance required. |

---

## Implementation Notes

**Validation Standards:**
- All required fields must be present
- All enum values must match exactly (case-sensitive, no typos)
- Numeric ranges (e.g., confidence 0.0-1.0) enforced
- String length minimums enforced (e.g., deconflictinglogic ≥50 chars)
- No additional top-level properties allowed
- All timestamps must be valid ISO8601 format

**Error Handling:**
- Invalid schema → QA FAIL, return to Stock Analyst
- Enum mismatch → QA FAIL, return to Stock Analyst
- Missing required field → QA FAIL, return to Stock Analyst
- Data type mismatch → QA FAIL, return to Stock Analyst

---
