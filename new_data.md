Bewlo are 7 new indicators to add to Main_V5 to enhance the capabilities. 


1. Treasury Yields & Yield Curve

A) 10-Year Yield (^TNX) - Done 
	1.	How to Implement
	•	Absolute Level: Directly use the yield value (e.g., 3.5%) as a continuous feature. Often valuable as it reflects the current cost of capital for longer-term risk assets.
	•	Daily Change or 1-Week Change: Compute yield differences (e.g., TNX[t] - TNX[t-1]). Large positive or negative shifts can signal impending transitions.
	•	Rolling Z-Score: Subtract a rolling mean and divide by rolling std to normalize across different yield environments.
	2.	Why it Helps
	•	A rising 10-year yield can indicate higher inflation expectations or tighter financial conditions—both can push equity markets into a risk-off or sideways regime if the rise is abrupt.

B) 30-Year Yield (^TYX) & 2-Year Yield (^FVX or ^IRX) - Next 
	1.	How to Implement
	•	Absolute Levels for each maturity if you want to see the entire term structure.
	•	Daily or Weekly Changes to detect abrupt shifts in short-term vs. long-term rates.
	•	Yield Curve Spread: Subtract the shorter yield from the longer (e.g., 10Y - 2Y). Use this as a single feature.
	2.	Why it Helps
	•	Inversion or flattening (where short-term yields approach or exceed long-term yields) often precedes recessions and bear markets.
	•	In a bull market, typically yields are normal (short < long) and stable.

⸻

2. Credit Spread via ETFs: HYG / TLT Ratio - done 
	1.	How to Implement
	•	Ratio: After downloading prices (or returns), compute \text{ratio} = \frac{HYG}{TLT}.
	•	Daily % Change in the ratio: (\text{ratio}[t] - \text{ratio}[t-1]) / \text{ratio}[t-1].
	•	Moving Average Divergence: Compare ratio to its 20- or 30-day MA to detect unusual swings (a sign of risk or flight to safety).
	2.	Why it Helps
	•	When HYG (high-yield bonds) strongly outperforms TLT (Treasuries), markets tend to be in a risk-on mode.
	•	A sharp decline in HYG/TLT can signal heightened credit concerns—often leading to or coinciding with a bear regime.

⸻

3. Sector Rotation Signals - Done 

A) XLY/XLP (Discretionary vs. Staples), XLP/SPY, XLU/SPY, XLF/SPY
	1.	How to Implement
	•	Ratio of Prices: e.g., XLY_Close / XLP_Close.
	•	Rolling % Change: e.g., use a 10-day return of the ratio.
	•	Z-score or Normalized Spread: If you prefer each feature in a comparable scale, subtract a rolling mean and divide by rolling std.
	2.	Why it Helps
	•	When cyclical (XLY) outperforms defensive (XLP), it suggests bullish sentiment.
	•	Outperformance of XLU (utilities) or XLP often signals caution—a potential sideways or bear environment.

⸻

4. US Dollar Strength: UUP
	1.	How to Implement
	•	Absolute Price: The UUP ETF price as-is.
	•	Daily % Change: (UUP[t] - UUP[t-1]) / UUP[t-1].
	•	Rolling Average Deviation: Compare UUP to its own 10-day or 20-day average to see if it’s spiking or weakening.
	2.	Why it Helps
	•	A rising dollar can weigh on commodities and international markets, often leading to more defensive equity conditions.
	•	A falling dollar can indicate risk-on or bullish appetite for equities.

⸻

5. Gold Performance: GLD - DONE
	1.	How to Implement
	•	Absolute Price of GLD or log returns (for more stable distribution).
	•	Ratio of GLD to SPY, or daily changes in that ratio.
	•	Volatility of GLD: If gold volatility is spiking, it can indicate macro or risk-related uncertainty.
	2.	Why it Helps
	•	Risk-off environment: Investors often flock to gold, causing GLD to rise or at least hold value while equities drop.
	•	A notable divergence between gold and SPY can confirm an oncoming or ongoing regime shift to bear or sideways.

⸻

6. Volatility Term Structure: ^VIX vs. ^VIX3M -done 
	1.	How to Implement
	•	Absolute Values: You can directly use vix_close and vix3m_close.
	•	Spread or Ratio: \text{VIX3M} - \text{VIX} or \frac{\text{VIX3M}}{\text{VIX}}.
	•	Slope: A large positive slope (VIX3M >> VIX) typically means normal contango; an inversion signals near-term fear.
	2.	Why it Helps
	•	Near-Term Fear: If ^VIX suddenly trades above ^VIX3M, it implies immediate panic vs. future volatility—a sign of potential short-term bear moves.
	•	A normal upward slope with low absolute levels suggests a steady or complacent environment (often bull or mild sideways).

⸻

7. Inflation Expectations: TIP, VTIP
	1.	How to Implement
	•	Price / NAV of the ETFs: TIPS ETF rising often indicates demand for inflation protection.
	•	Daily or Weekly Changes in TIP vs. SPY can highlight shifts in inflation concerns.
	•	Ratio: (TIP or VTIP) / SPY if you want a direct measure of relative outperformance.
	2.	Why it Helps
	•	Rising TIP prices can mean investors expect either lower real rates or higher inflation, both of which can tilt equity valuations and risk sentiment.
	•	If TIPS strongly outperform nominal Treasuries, the market might be pricing in inflation or other macro stress, impacting your equity regime signals.
