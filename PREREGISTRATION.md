# Pre-registration: Kalshi-expected core CPI trend deviation and the pre-release move in 10-year Treasury futures

Registered: November 25, 2026 (GitHub commit). Author: Alex Hossack.

## Status of the hypothesis (disclosure)
- Found in-sample, not predicted. Discovered on 40 CPI releases (Apr 2022 to Oct 2025) after a specification search: about 12 trading-rule tests and many regressions across ZT, ZN, SR3, ES and 6E and several intraday windows.
- In-sample result: ZN sign rule (below), hit rate 0.70, mean net P&L about 0.078 points per contract per release, t = 2.44. Regression of the pre-release ZN move on the signal, controlling for the headline and core surprises: about 23bp of yield per pp, p < 0.001; survives year fixed effects and the inflation level.
- This registration is a confirmation test on data not yet examined.

## Hypothesis
- H1 (primary): when Kalshi's pre-release core CPI expectation is above the trailing trend, 10-year Treasury futures fall (yields rise) between 16:05 ET on the business day before the release and 8:30 ET on release day; when below, they rise.

## Data
- Kalshi core CPI month-over-month threshold markets (KXCPICORE; legacy CPICORE), hourly candles, public API.
- CME 10-year Treasury note futures (ZN), 2-year (ZT), 1-minute OHLCV bars from Databento (GLBX.MDP3), continuous front contract by volume (ZN.v.0, ZT.v.0).
- First-published monthly core CPI changes (BLS, as first released): Kalshi settlement values where available, otherwise ALFRED real-time vintages. No revised data enter the signal.

## Signal (known before the trade)
- K = mean of the Kalshi-implied core CPI m/m distribution at the last hourly candle ending by 16:00 ET on the business day before the release. Construction as in Stage 1: bid/ask mid per strike (last trade if a side is missing), non-increasing fit across strikes (pool-adjacent-violators), differenced into 0.1pp bins; mass below the lowest strike at that strike, mass above the highest strike one step above it. Raw prices, no longshot correction.
- T = average of the previous 12 monthly core CPI changes as first published, using only prints released before the trade.
- e_trend = K - T.

## Trading rule (fixed)
- At 16:05 ET on the business day before the release: short one ZN contract if e_trend > 0, long one if e_trend < 0, no trade if e_trend = 0.
- Instrument: the volume-front ZN contract at entry; exit the same contract.
- Exit: last trade before 08:30:00 ET on release day.
- Prices: 1-minute bar closes; price at time t = close of the last bar ending by t.
- Costs per round trip: 1/64 point (one tick) plus $5 commissions and fees (0.005 points). P&L in points per contract, net.

## Sample
- Confirmation sample: (a) the holdout, CPI release days after 2025-11-10 (13 release days, listed in stage2_event_windows.parquet, not yet analysed), plus (b) every CPI release after the registration date through the December 2027 release.
- Exclusions, fixed now: releases without a Kalshi core snapshot at 16:00 the day before; releases delayed or altered by a government shutdown (as Oct and Nov 2025); days with no ZN trade in the 30 minutes before entry or exit.

## Analysis
- Primary: one-sided t-test that mean net P&L per trade > 0, alpha = 0.05, on the full confirmation sample at the end date. Also report hit rate with a binomial test, mean gross P&L, and per-trade Sharpe.
- Interim: the holdout result is reported when computed, labelled interim; no change to the rule after seeing it.
- Secondary (Holm correction across these two):
  - S1: same rule on ZT (cost 1/256 point plus 0.0025 points).
  - S2: payroll version: K = Kalshi payrolls mean (KXPAYROLLS) at 16:00 ET the business day before the Employment Situation release; T = average of the previous 12 first-published monthly payroll changes; same ZN trade and exit (8:30 ET release). Sample: every payroll release with a Kalshi payrolls market, March 2023 through December 2027. No payroll price or futures data had been examined at registration, so the historical releases (about 44, Mar 2023 to Oct 2026) are out of sample. Excluded: shutdown-affected releases.
- Everything else is exploratory and will be labelled so.

## Power
- 13 holdout releases can only detect a very large effect; the in-sample effect (per-trade Sharpe about 0.39) needs roughly 40 or more trades for 80% power at one-sided alpha 0.05. The test that settles it is the accumulation of releases to the end date; the interim holdout result alone is not decisive.

## Deviations
- Any deviation (data errors, contract changes, fee changes) will be reported with its reason and its effect on the result.
