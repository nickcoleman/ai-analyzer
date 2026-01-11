# MARS PHASE 2 DELIVERABLE 2.3
## MacroRegimeScorer_v8.1.0.py

**Date:** December 29, 2025, 1:45 AM MST  
**Phase:** 2 - Data Integration & Algorithms  
**Deliverable:** 2.3 of 6  
**Token Budget:** 3,500 tokens  
**Status:** IMPLEMENTATION COMPLETE

---

## OVERVIEW

MacroRegimeScorer calculates market regime classification from 7 macro dimensions. Input: Macro data from 2.1. Output: Regime classification (BULLISH/NEUTRAL/BEARISH/CRISIS) with confidence. Execution time: <5 seconds.

---

## CODE IMPLEMENTATION

```python
"""
MarketAnalyst v8.1.0 - Macro Regime Scorer
Scores 7 macro dimensions and classifies market regime
"""

import logging
from datetime import datetime
from typing import Dict, Optional, Tuple
from dataclasses import dataclass
from abc import ABC, abstractmethod

# Configure logging
logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)


@dataclass
class RegimeScore:
    """Represents calculated regime score"""
    regime_score: float  # 0-100
    current_regime: str  # BULLISH, NEUTRAL, BEARISH, CRISIS
    confidence_percent: int  # 50-95
    regime_multiplier: float  # Weighting for portfolio
    timestamp: str


class Scorer(ABC):
    """Abstract base class for scorers"""
    
    @abstractmethod
    def score(self, data: Dict) -> Dict:
        """Calculate score from data"""
        pass


class MacroRegimeScorer(Scorer):
    """
    Scores 7 macro dimensions and classifies market regime
    
    7 Dimensions (with weights):
    1. Interest rates (15%) - Fed policy stance
    2. Inflation trends (20%) - Price stability
    3. Credit conditions (20%) - Financial system health
    4. GDP growth (25%) - Economic activity
    5. Geopolitical risk (10%) - Tail risks
    6. Equity risk premiums (5%) - VIX/volatility
    7. Consensus agreement (5%) - Signal alignment
    """
    
    # Weights for each dimension
    DIMENSION_WEIGHTS = {
        'interest_rates': 0.15,
        'inflation_trends': 0.20,
        'credit_conditions': 0.20,
        'gdp_growth': 0.25,
        'geopolitical_risk': 0.10,
        'equity_risk_premiums': 0.05,
        'consensus_agreement': 0.05
    }
    
    # Regime thresholds and multipliers
    REGIME_CONFIG = {
        'BULLISH': {'min_score': 75, 'multiplier_range': (1.10, 1.25)},
        'NEUTRAL': {'min_score': 60, 'multiplier_range': (1.00, 1.00)},
        'BEARISH': {'min_score': 40, 'multiplier_range': (0.75, 0.85)},
        'CRISIS': {'min_score': 0, 'multiplier_range': (0.50, 0.50)}
    }
    
    def __init__(self):
        """Initialize scorer"""
        self.dimension_scores = {}
        self.regime_score = None
        self.regime = None
        self.confidence = None
    
    def score(self, macro_data: Dict) -> Dict:
        """
        Main entry point: Score macro regime
        
        Args:
            macro_data: Dict with 'fed_policy', 'inflation', 'credit', 
                       'gdp_employment', 'volatility', 'geopolitical'
        
        Returns:
            Dict with regime classification + metadata
        """
        logger.info("Starting MacroRegimeScorer...")
        start_time = datetime.now()
        
        try:
            # Score 7 dimensions
            self.dimension_scores = {
                'interest_rates': self._score_interest_rates(macro_data.get('fed_policy', {})),
                'inflation_trends': self._score_inflation(macro_data.get('inflation', {})),
                'credit_conditions': self._score_credit(macro_data.get('credit', {})),
                'gdp_growth': self._score_gdp(macro_data.get('gdp_employment', {})),
                'geopolitical_risk': self._score_geopolitical(macro_data.get('geopolitical', {})),
                'equity_risk_premiums': self._score_volatility(macro_data.get('volatility', {})),
                'consensus_agreement': self._score_consensus(self.dimension_scores)
            }
            
            # Calculate weighted regime score
            self.regime_score = self._calculate_weighted_score()
            
            # Classify regime
            regime_info = self._classify_regime(self.regime_score)
            self.regime = regime_info['regime']
            
            # Calculate confidence
            self.confidence = self._calculate_confidence(self.regime_score, regime_info)
            
            # Extract drivers and risks
            drivers = self._extract_drivers()
            risks = self._extract_risks()
            
            # Build output
            execution_time = (datetime.now() - start_time).total_seconds()
            
            result = {
                'regime_score': round(self.regime_score, 1),
                'current_regime': self.regime,
                'confidence_percent': self.confidence,
                'regime_multiplier': regime_info['multiplier'],
                'dimension_scores': {k: round(v, 1) for k, v in self.dimension_scores.items()},
                'key_drivers': drivers,
                'risks_to_regime': risks,
                'execution_time': round(execution_time, 3),
                'timestamp': datetime.now().isoformat()
            }
            
            logger.info(f"MacroRegimeScorer completed: {self.regime} ({self.confidence}% confidence)")
            return result
            
        except Exception as e:
            logger.error(f"MacroRegimeScorer failed: {e}")
            return self._get_neutral_fallback(str(e))
    
    # ========== DIMENSION SCORING METHODS ==========
    
    def _score_interest_rates(self, fed_data: Dict) -> float:
        """
        Score Federal Reserve policy and rate environment
        
        Lower rates = more supportive = higher score
        Cuts expected = higher score
        Hikes expected = lower score
        """
        if not fed_data or 'error' in fed_data:
            return 50  # Neutral fallback
        
        fed_rate = fed_data.get('fed_funds_rate')
        policy_stance = fed_data.get('policy_stance', '').upper()
        
        # Rate-based scoring
        if fed_rate is not None:
            if fed_rate <= 1.0:
                rate_score = 85  # Very accommodative
            elif fed_rate <= 2.5:
                rate_score = 70  # Accommodative
            elif fed_rate <= 4.0:
                rate_score = 55  # Neutral
            elif fed_rate <= 5.5:
                rate_score = 40  # Restrictive
            else:
                rate_score = 20  # Very restrictive
        else:
            rate_score = 50
        
        # Policy stance adjustment
        if policy_stance == 'EASING':
            rate_score = min(90, rate_score + 15)
        elif policy_stance == 'TIGHTENING':
            rate_score = max(20, rate_score - 15)
        
        return rate_score
    
    def _score_inflation(self, inflation_data: Dict) -> float:
        """
        Score inflation trends
        
        CPI 2-3% = ideal = high score
        Below 2% or above 4% = concerning = lower score
        """
        if not inflation_data or 'error' in inflation_data:
            return 50
        
        cpi = inflation_data.get('cpi')
        
        if cpi is not None:
            if cpi <= 2.0:
                return 75  # Low inflation risk
            elif cpi <= 3.0:
                return 80  # Ideal range
            elif cpi <= 4.0:
                return 60  # Elevated but manageable
            elif cpi <= 5.0:
                return 40  # High inflation
            else:
                return 20  # Runaway inflation
        
        return 50
    
    def _score_credit(self, credit_data: Dict) -> float:
        """
        Score credit market conditions
        
        Tight spreads = healthy credit = high score
        Widening spreads = credit stress = lower score
        """
        if not credit_data or 'error' in credit_data:
            return 50
        
        hy_spread = credit_data.get('hy_spread')  # High-yield spread in bps
        
        if hy_spread is not None:
            if hy_spread < 250:
                return 85  # Comfortably tight
            elif hy_spread < 300:
                return 70  # Tight
            elif hy_spread < 350:
                return 55  # Normal
            elif hy_spread < 400:
                return 40  # Widening
            else:
                return 20  # Stress
        
        return 50
    
    def _score_gdp(self, gdp_data: Dict) -> float:
        """
        Score GDP growth and employment
        
        GDP >2.5% growth = strong = high score
        Unemployment declining = positive signal
        """
        if not gdp_data or 'error' in gdp_data:
            return 50
        
        gdp_growth = gdp_data.get('gdp_growth')
        unemployment = gdp_data.get('unemployment')
        
        if gdp_growth is not None:
            if gdp_growth > 2.5:
                gdp_score = 85  # Strong growth
            elif gdp_growth > 2.0:
                gdp_score = 75  # Good growth
            elif gdp_growth > 1.0:
                gdp_score = 55  # Moderate growth
            elif gdp_growth > 0:
                gdp_score = 35  # Weak growth
            else:
                gdp_score = 15  # Contraction
        else:
            gdp_score = 50
        
        # Unemployment adjustment
        if unemployment is not None:
            if unemployment < 4.0:
                gdp_score = min(90, gdp_score + 10)  # Tight labor market
            elif unemployment > 5.0:
                gdp_score = max(20, gdp_score - 10)  # Slack labor market
        
        return gdp_score
    
    def _score_geopolitical(self, geo_data: Dict) -> float:
        """
        Score geopolitical risk
        
        Low risk = higher score (markets prefer stability)
        Active conflicts = lower score
        """
        if not geo_data or 'error' in geo_data:
            return 50
        
        conflicts = geo_data.get('active_conflicts', [])
        trade_policy = geo_data.get('trade_policy', '').lower()
        
        # Conflict count assessment
        if len(conflicts) == 0:
            geo_score = 80  # No known conflicts
        elif len(conflicts) == 1:
            geo_score = 60  # One active conflict
        elif len(conflicts) <= 3:
            geo_score = 40  # Multiple conflicts
        else:
            geo_score = 20  # Widespread conflicts
        
        # Trade policy adjustment
        if 'tariff' in trade_policy or 'restriction' in trade_policy:
            geo_score = max(20, geo_score - 20)
        elif 'favorable' in trade_policy or 'agreement' in trade_policy:
            geo_score = min(85, geo_score + 10)
        
        return geo_score
    
    def _score_volatility(self, vol_data: Dict) -> float:
        """
        Score volatility and risk appetite
        
        VIX <15 = low volatility = good for equities = high score
        VIX >25 = high volatility = risk-off = lower score
        """
        if not vol_data or 'error' in vol_data:
            return 50
        
        vix = vol_data.get('vix_level')
        
        if vix is not None:
            if vix < 12:
                return 80  # Complacency/calm
            elif vix < 15:
                return 75  # Low volatility
            elif vix < 20:
                return 60  # Moderate volatility
            elif vix < 25:
                return 40  # Elevated volatility
            else:
                return 20  # High fear
        
        return 50
    
    def _score_consensus(self, dimension_scores: Dict) -> float:
        """
        Score signal alignment (consensus agreement)
        
        Higher score if dimensions agree on regime direction
        """
        if not dimension_scores:
            return 50
        
        # Count dimensions in each bucket
        bullish = sum(1 for s in dimension_scores.values() if s >= 70)
        bearish = sum(1 for s in dimension_scores.values() if s <= 40)
        neutral = sum(1 for s in dimension_scores.values() if 40 < s < 70)
        
        total = len(dimension_scores)
        
        # Strong consensus = high confidence = higher score
        if bullish >= 4:
            return 85  # Strong bullish consensus
        elif bullish >= 3:
            return 70  # Bullish consensus
        elif bearish >= 4:
            return 20  # Strong bearish consensus
        elif bearish >= 3:
            return 35  # Bearish consensus
        else:
            return 50  # Mixed signals
    
    # ========== CALCULATION METHODS ==========
    
    def _calculate_weighted_score(self) -> float:
        """
        Calculate weighted average of 7 dimensions
        
        Returns: Score 0-100
        """
        if not self.dimension_scores:
            return 50
        
        weighted_sum = 0
        for dimension, weight in self.DIMENSION_WEIGHTS.items():
            score = self.dimension_scores.get(dimension, 50)
            weighted_sum += score * weight
        
        # Clamp to 0-100
        return max(0, min(100, weighted_sum))
    
    def _classify_regime(self, score: float) -> Dict:
        """
        Classify regime from score and calculate multiplier
        
        Args:
            score: 0-100 regime score
        
        Returns:
            Dict with regime name and multiplier
        """
        if score >= 75:
            regime = 'BULLISH'
            # Multiplier increases with score: 1.10 at 75, 1.25 at 100
            multiplier = 1.10 + (score - 75) / 25 * 0.15
        elif score >= 60:
            regime = 'NEUTRAL'
            multiplier = 1.0
        elif score >= 40:
            regime = 'BEARISH'
            # Multiplier decreases as score falls: 0.85 at 60, 0.75 at 40
            multiplier = 0.85 - (60 - score) / 20 * 0.10
        else:
            regime = 'CRISIS'
            multiplier = 0.50
        
        return {
            'regime': regime,
            'multiplier': round(multiplier, 2)
        }
    
    def _calculate_confidence(self, regime_score: float, regime_info: Dict) -> int:
        """
        Calculate confidence percentage (50-95%)
        
        Higher confidence when:
        - Score is far from regime boundaries
        - Dimensions agree (consensus)
        """
        regime = regime_info['regime']
        
        # Base confidence by regime
        if regime == 'BULLISH':
            base_confidence = 60 + min(20, (regime_score - 75) * 0.4)
        elif regime == 'NEUTRAL':
            # Confidence lower when neutral (more ambiguous)
            distance_from_boundary = min(abs(regime_score - 60), abs(regime_score - 75))
            base_confidence = 50 + distance_from_boundary
        elif regime == 'BEARISH':
            base_confidence = 60 + min(15, (60 - regime_score) * 0.3)
        else:  # CRISIS
            base_confidence = 50 + min(25, (40 - regime_score) * 0.5)
        
        # Consensus adjustment
        consensus_score = self.dimension_scores.get('consensus_agreement', 50)
        if consensus_score > 75:
            base_confidence += 5
        elif consensus_score < 40:
            base_confidence -= 5
        
        # Clamp to 50-95%
        return max(50, min(95, int(base_confidence)))
    
    def _extract_drivers(self) -> list:
        """
        Extract top 2-3 positive drivers (highest scores)
        
        Returns:
            List of dimension names driving the regime
        """
        ranked = sorted(
            self.dimension_scores.items(),
            key=lambda x: x[1],
            reverse=True
        )
        
        # Return top dimensions that contribute positively
        drivers = [name for name, score in ranked[:3] if score >= 60]
        
        return drivers if drivers else [ranked[0][0]]  # At least return top 1
    
    def _extract_risks(self) -> list:
        """
        Extract top 2-3 risks (lowest scores)
        
        Returns:
            List of dimension names that are risks
        """
        ranked = sorted(
            self.dimension_scores.items(),
            key=lambda x: x[1]
        )
        
        # Return bottom dimensions that are concerning
        risks = [name for name, score in ranked[:3] if score <= 50]
        
        return risks if risks else []
    
    def _get_neutral_fallback(self, error: str = "") -> Dict:
        """
        Return neutral fallback if scoring fails
        
        Args:
            error: Error message
        
        Returns:
            Neutral regime dict with caveat
        """
        logger.warning(f"Using neutral fallback: {error}")
        
        return {
            'regime_score': 50.0,
            'current_regime': 'NEUTRAL',
            'confidence_percent': 50,
            'regime_multiplier': 1.0,
            'dimension_scores': {k: 50.0 for k in self.DIMENSION_WEIGHTS.keys()},
            'key_drivers': [],
            'risks_to_regime': [],
            'fallback': True,
            'caveat': f'Scoring failed: {error}. Using neutral regime.',
            'timestamp': datetime.now().isoformat()
        }


# ========== USAGE EXAMPLE ==========

if __name__ == '__main__':
    # Example macro data (from MacroDataFetcher)
    macro_data = {
        'fed_policy': {
            'fed_funds_rate': 4.5,
            'policy_stance': 'NEUTRAL'
        },
        'inflation': {
            'cpi': 3.2
        },
        'credit': {
            'hy_spread': 320
        },
        'gdp_employment': {
            'gdp_growth': 2.1,
            'unemployment': 4.0
        },
        'volatility': {
            'vix_level': 16.5
        },
        'geopolitical': {
            'active_conflicts': ['Russia-Ukraine'],
            'trade_policy': 'Moderate restrictions'
        }
    }
    
    # Score regime
    scorer = MacroRegimeScorer()
    result = scorer.score(macro_data)
    
    # Print results
    print(f"Regime: {result['current_regime']}")
    print(f"Score: {result['regime_score']}/100")
    print(f"Confidence: {result['confidence_percent']}%")
    print(f"Multiplier: {result['regime_multiplier']}")
    print(f"Drivers: {result['key_drivers']}")
    print(f"Risks: {result['risks_to_regime']}")
```

---

## ALGORITHM SPECIFICATION

### 7 Dimensions (Weights)

1. **Interest Rates** (15%)
   - Score: Fed rate, policy stance
   - Scoring: Lower rates = higher score (0.5% → 85, 5.5% → 40)
   - Signal: Monetary policy stance

2. **Inflation Trends** (20%)
   - Score: CPI level, trend
   - Scoring: 2-3% = ideal (80), <2% or >4% = concerning (<60)
   - Signal: Price stability

3. **Credit Conditions** (20%)
   - Score: HY spreads, credit trends
   - Scoring: <250 bps = tight (85), >400 bps = stress (20)
   - Signal: Financial system health

4. **GDP Growth** (25%)
   - Score: GDP rate, unemployment
   - Scoring: >2.5% = strong (85), <0% = contraction (15)
   - Signal: Economic activity

5. **Geopolitical Risk** (10%)
   - Score: Active conflicts, trade policy
   - Scoring: No conflicts (80), multiple conflicts (20)
   - Signal: Tail risks

6. **Equity Risk Premiums** (5%)
   - Score: VIX level
   - Scoring: VIX <12 (80), VIX >25 (20)
   - Signal: Risk appetite

7. **Consensus Agreement** (5%)
   - Score: Signal alignment across dimensions
   - Scoring: 4+ dimensions bullish (85), mixed (50)
   - Signal: Confidence in regime

### Regime Classification

```
Score 75-100: BULLISH
  Multiplier: 1.10-1.25 (portfolio boost)
  Confidence: 60-95%

Score 60-75: NEUTRAL
  Multiplier: 1.0 (no adjustment)
  Confidence: 50-70% (ambiguous)

Score 40-60: BEARISH
  Multiplier: 0.75-0.85 (portfolio reduce)
  Confidence: 40-75%

Score <40: CRISIS
  Multiplier: 0.50 (defensive mode)
  Confidence: 50-95% (high certainty of crisis)
```

---

## EXECUTION TIME ANALYSIS

**Expected Execution Time: <5 seconds**

- Dimension scoring (7 × calculations): 1-2 seconds
- Weighted average calculation: <1 second
- Regime classification: <1 second
- Confidence calculation: <1 second
- Driver/risk extraction: <1 second
- **Total: 2-5 seconds** ✓

---

## OUTPUT SCHEMA EXAMPLE

```json
{
  "regime_score": 72.5,
  "current_regime": "NEUTRAL",
  "confidence_percent": 68,
  "regime_multiplier": 1.0,
  "dimension_scores": {
    "interest_rates": 55.0,
    "inflation_trends": 75.0,
    "credit_conditions": 68.0,
    "gdp_growth": 72.0,
    "geopolitical_risk": 55.0,
    "equity_risk_premiums": 75.0,
    "consensus_agreement": 60.0
  },
  "key_drivers": ["inflation_trends", "equity_risk_premiums", "gdp_growth"],
  "risks_to_regime": ["interest_rates", "geopolitical_risk"],
  "execution_time": 2.341,
  "timestamp": "2025-12-29T17:00:00Z"
}
```

---

## INTEGRATION NOTES

**Input:** Macro data from 2.1 (MacroDataFetcher)
- fed_policy, inflation, credit, gdp_employment, volatility, geopolitical

**Output:** Regime classification
- Used by Phase 3 (Daily/Weekly reports)
- Used by 2.4 (SectorAttractivenessScorer) for weighting
- Used by Portfolio-Orchestrator for position sizing

**Dependencies:**
- 2.1 MacroDataFetcher (upstream)
- 2.4 SectorAttractivenessScorer (downstream)

---

## QUALITY GATES FOR 2.3

| Gate | Requirement | Verification |
|---|---|---|
| **Accuracy** | 100% regime classification | All 4 regimes correctly identified in test data |
| **Performance** | <5 seconds | execution_time < 5 |
| **Consistency** | Dimension weights apply correctly | Weighted sum matches manual calc |
| **Confidence** | 50-95% range | All confidences within bounds |
| **Multiplier** | Applied correctly | Multipliers match regime ranges |
| **Fallback** | Graceful failure | Returns neutral regime if input missing |

---

**MARS Phase 2 Deliverable 2.3: COMPLETE**

Ready for integration with Phase 2.4 (SectorAttractivenessScorer)

