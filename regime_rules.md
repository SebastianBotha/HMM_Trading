# Market Regime Classification System: Final Scorecard

## General Framework

**Daily Computation Process:**
1. Calculate all technical indicators and metrics based on the available features.
2. Score each regime (Bull, Neutral, Bear) using its respective scorecard below. Apply graduated scoring where specified.
3. Apply the regime persistence factor to the raw scores.
4. Identify the regime with the highest final score as the current market state.
5. Apply a risk-averse tiebreaker if needed (Bear > Neutral > Bull).

**Regime Persistence Factor:**
*   Add **+8 points** to the raw score of the regime that was identified as active on the previous trading day.
*   Add **+4 points** to the raw score of the regime that was identified as active two trading days ago.
*   *Rationale:* This reduces excessive regime switching during borderline conditions while still allowing for timely transitions.

---

## I. Bull Regime Scorecard (100 Base Points)

| # | Rule Description       | Calculation                                                        | Weight | Graduated Scoring                                                                  | Rationale                                                                     |
| :-- | :--------------------- | :----------------------------------------------------------------- | :----- | :--------------------------------------------------------------------------------- | :---------------------------------------------------------------------------- |
| 1 | **Trend Structure**    | `SMA_Fast > SMA_Slow` for at least 5 consecutive days                | 12     | No - Binary rule                                                                   | Golden cross with persistence confirms established uptrend and filters noise    |
| 2 | **Price Strength**     | `Close > SMA_Slow * 1.02`                                          | 12     | Yes:<br>• 100% if > 1.02<br>• 67% if > 1.01<br>• 33% if > 1.00                      | Price meaningfully above trend indicates momentum; captures strength degrees    |
| 3 | **Medium-Term Momentum** | `Momentum > Momentum.rolling(252).quantile(0.60)`                  | 13     | Yes:<br>• 100% if > 70th percentile<br>• 67% if > 60th percentile<br>• 33% if > 50th percentile | Strong past performance relative to history; captures developing momentum       |
| 4 | **Volatility Env.**    | `VIX < VIX.rolling(252).quantile(0.40) AND Volatility < Volatility.rolling(63).mean()` | 10     | No - Combined conditions                                                           | Lower implied & realized volatility coincide with sustainable bullish markets   |
| 5 | **Credit Conditions**  | `HYG_TLT_Z > 0.5`                                                  | 12     | Yes:<br>• 100% if > 1.0<br>• 67% if > 0.7<br>• 33% if > 0.5                        | High-yield outperformance indicates risk appetite; strength of signal matters |
| 6 | **Broad Participation**| `AD_Line > AD_Line_50MA AND Advancing_Percentage > 55`             | 11     | No - Combined conditions                                                           | Strong breadth confirms rally is broad-based, not narrowly concentrated       |
| 7 | **Sector Leadership**  | `XLY_XLP_Z > 0.3 AND XLF_SPY_Z > 0`                                | 10     | No - Combined conditions                                                           | Discretionary & financials outperforming signals economic optimism            |
| 8 | **Yield Curve Health** | `Yield_Curve_Spread > 0 AND Yield_Curve_Spread_Change (21d) >= -0.05%` | 8      | No - Combined conditions                                                           | Positive, non-rapidly-flattening curve indicates healthy growth expectations  |
| 9 | **Risk Appetite**      | `BB_PercentB > 0.7 OR (GLD_Momentum > 0 AND UUP_Z_Score < 0.3)`      | 7      | No - Alternate conditions                                                          | Captures risk appetite via price position or intermarket relationships        |
| 10 | **Volume Confirm.**  | `Volume > Volume.rolling(21).mean()` on days when `Close > Close.shift(1)` | 5      | No - Binary rule                                                                   | Rising prices on above-average volume confirms buying pressure                |
|     | **Base Total**         |                                                                    | **100**|                                                                                    |                                                                               |

---

## II. Neutral Regime Scorecard (100 Base Points)

| # | Rule Description     | Calculation                                                                              | Weight | Graduated Scoring                                                                 | Rationale                                                                         |
| :-- | :------------------- | :--------------------------------------------------------------------------------------- | :----- | :-------------------------------------------------------------------------------- | :-------------------------------------------------------------------------------- |
| 1 | **Range-Bound Price**| `ABS(Price_to_SMA_Fast) < 0.03 AND ABS(Price_to_SMA_Slow) < 0.04`                      | 14     | Yes:<br>• 100% if both < 0.02<br>• 67% if as stated<br>• 33% if within 0.05       | Price oscillating around MAs without clear direction is hallmark of neutral market  |
| 2 | **Trend Flatness**   | `ABS(SMA_Ratio - 1) < 0.02`                                                              | 12     | Yes:<br>• 100% if < 0.01<br>• 67% if < 0.02<br>• 33% if < 0.03                     | MAs converging indicates trend neutrality; degree matters                           |
| 3 | **Choppiness**       | `Choppiness_Index > 55 AND Choppiness_Index < 80`                                        | 13     | No - Range condition                                                              | Choppiness Index specifically designed to identify sideways markets             |
| 4 | **Limited Momentum** | `ABS(Momentum_Z) < 0.75`                                                                 | 11     | Yes:<br>• 100% if < 0.5<br>• 67% if < 0.75<br>• 33% if < 1.0                      | Little directional progress; graduated approach captures degree of neutrality     |
| 5 | **Moderate Vol.**    | `ABS(VIX_Z_Score) < 0.8`                                                                 | 10     | Yes:<br>• 100% if < 0.5<br>• 67% if < 0.8<br>• 33% if < 1.0                      | Volatility neither high nor low; graduated approach measures closeness to average |
| 6 | **Band Contraction** | `BB_Width_Z BETWEEN -0.8 AND 0.5`                                                        | 9      | No - Range condition                                                              | Neither abnormally wide nor narrow bands; both extremes predict directional moves |
| 7 | **Credit Stability** | `ABS(HYG_TLT_Z) < 0.7`                                                                   | 9      | Yes:<br>• 100% if < 0.4<br>• 67% if < 0.7<br>• 33% if < 1.0                      | Credit markets showing neither strong risk-on nor risk-off behavior             |
| 8 | **Mixed Breadth**    | `ABS(McClellan_Oscillator_Norm) < 25`                                                    | 8      | Yes:<br>• 100% if < 15<br>• 67% if < 25<br>• 33% if < 35                         | Neither strong buying nor selling pressure across the market                    |
| 9 | **Sector Balance**   | `ABS(XLY_XLP_Z) < 0.5 AND ABS(XLU_SPY_Z) < 0.5`                                          | 8      | No - Combined conditions                                                          | Neither cyclicals nor defensives showing meaningful outperformance              |
| 10 | **Mean-Reversion** | `BB_PercentB.rolling(10).std() > 0.15 AND BB_PercentB.rolling(10).mean().between(0.4, 0.6)` | 6      | No - Combined conditions                                                          | Price oscillating within bands but returning to midpoint - classic consolidation  |
|     | **Base Total**       |                                                                                          | **100**|                                                                                   |                                                                                   |

---

## III. Bear Regime Scorecard (100 Base Points)

| # | Rule Description       | Calculation                                                                                 | Weight | Graduated Scoring                                                                  | Rationale                                                                         |
| :-- | :--------------------- | :------------------------------------------------------------------------------------------ | :----- | :--------------------------------------------------------------------------------- | :-------------------------------------------------------------------------------- |
| 1 | **Trend Structure**    | `SMA_Fast < SMA_Slow * 0.98` for at least 3 consecutive days                                  | 12     | No - Binary rule                                                                   | Clear death cross with meaningful separation confirms established downtrend       |
| 2 | **Price Weakness**     | `Close < SMA_Slow * 0.97`                                                                   | 12     | Yes:<br>• 100% if < 0.97<br>• 67% if < 0.98<br>• 33% if < 0.99                      | Price significantly below trend indicates weakness; captures degree               |
| 3 | **Negative Momentum**  | `Momentum < Momentum.rolling(252).quantile(0.35)`                                           | 13     | Yes:<br>• 100% if < 30th percentile<br>• 67% if < 35th percentile<br>• 33% if < 40th percentile | Poor relative performance; graduated approach captures severity                   |
| 4 | **Elevated Vol.**      | `VIX > VIX.rolling(252).quantile(0.70) AND VIX > 20`                                          | 10     | Yes (VIX percentile):<br>• 100% if > 80th<br>• 67% if > 70th<br>• 33% if > 60th       | Heightened fear; higher VIX correlates with bear markets                          |
| 5 | **Credit Stress**      | `HYG_TLT_Ratio < HYG_TLT_MA * 0.985 AND HYG_TLT_Z < -0.7`                                    | 12     | Yes (Z-score):<br>• 100% if < -1.2<br>• 67% if < -0.9<br>• 33% if < -0.7         | Flight from high-yield to safety; severity indicates degree of stress           |
| 6 | **Defensive Rotation** | `XLY_XLP_Z < -0.5 AND XLU_SPY_Z > 0.3`                                                      | 10     | No - Combined conditions                                                           | Classic bear market sector rotation - away from cyclicals toward defensives     |
| 7 | **Breadth Deterior.**  | `AD_Line < AD_Line_50MA AND (Declining_Percentage > 55 OR AD_Negative_Divergence > 0)`      | 11     | No - Combined conditions                                                           | Confirms market-wide selling pressure, not just large-cap weakness            |
| 8 | **Vol. Structure**     | `VIX_VIX3M_Ratio > 1.03 OR VIX_VIX3M_Ratio_Z > 1.5`                                         | 8      | No - Alternate conditions                                                          | Term structure inversion (backwardation) signals acute fear/stress                |
| 9 | **Yield Curve Warn.**  | `Yield_Curve_Spread < 0.05% OR (Yield_Curve_Spread < 0.15% AND Yield_Curve_Spread_Change (21d) < -0.10%)` | 7      | No - Alternate conditions                                                          | Captures both inverted curves and rapidly flattening curves                       |
| 10 | **Flight-to-Quality**| `GLD_SPY_Ratio > GLD_SPY_Ratio.rolling(63).mean() * 1.03 OR TLT > TLT.rolling(20).max() * 0.98` | 5      | No - Alternate conditions                                                          | Defensive assets outperforming equities indicates risk aversion                 |
|     | **Base Total**         |                                                                                             | **100**|                                                                                    |                                                                                   |

---

## Implementation Process

**1. Daily Calculation Sequence:**
   ```python
   # Pseudocode for daily calculation
   def calculate_daily_regime(previous_regime, regime_2_days_ago, daily_metrics):
       # 1. Calculate all indicators (assumed done in daily_metrics)
       
       # 2. Score each regime
       raw_score = {}
       raw_score['Bull'] = score_bull_regime(daily_metrics)
       raw_score['Neutral'] = score_neutral_regime(daily_metrics)
       raw_score['Bear'] = score_bear_regime(daily_metrics)

       # 3. Apply regime persistence
       final_score = {}
       for regime in ['Bull', 'Neutral', 'Bear']:
           persistence_bonus = 0
           if previous_regime == regime:
               persistence_bonus += 8
           if regime_2_days_ago == regime:
               persistence_bonus += 4
           final_score[regime] = raw_score[regime] + persistence_bonus

       # 4. Determine current regime (with tiebreaker)
       max_score = -1
       current_regime = 'Neutral' # Default or previous day's regime

       if final_score['Bear'] > max_score:
           max_score = final_score['Bear']
           current_regime = 'Bear'
       
       # Check Neutral only if it beats Bear (or Bear wasn't highest)
       if final_score['Neutral'] >= max_score: # Use >= to allow Neutral tiebreak over Bull
           # If Bear was equal, keep Bear due to tiebreaker
           if current_regime != 'Bear' or final_score['Neutral'] > max_score:
              max_score = final_score['Neutral']
              current_regime = 'Neutral'

       # Check Bull only if it beats Bear and Neutral
       if final_score['Bull'] > max_score:
           max_score = final_score['Bull']
           current_regime = 'Bull'
           
       return current_regime, final_score # Return determined regime and scores

## 2. Rule Evaluation Example (Graduated Scoring):
### Example: Bull Rule #3 (Medium-Term Momentum)
weight = 13
momentum_value = daily_metrics['Momentum']
mom_perc_70 = daily_metrics['Momentum_Perc_70'] # Pre-calculated rolling percentiles
mom_perc_60 = daily_metrics['Momentum_Perc_60']
mom_perc_50 = daily_metrics['Momentum_Perc_50']

score_component = 0
if momentum_value > mom_perc_70:
    score_component = weight * 1.0  # 100% of weight
elif momentum_value > mom_perc_60:
    score_component = weight * 0.67  # 67% of weight
elif momentum_value > mom_perc_50:
    score_component = weight * 0.33  # 33% of weight

# Add score_component to the total raw score for the Bull regime
# Example calculation after determining final scores
scores = list(final_score.values())
scores.sort(reverse=True)
highest_score = scores[0]
second_highest_score = scores[1]
score_margin = highest_score - second_highest_score

if score_margin > 25:
    confidence = "High"
elif score_margin > 15:
    confidence = "Medium"
else:
    confidence = "Low"
# Output: Determined Regime, Confidence Level, Individual Scores
