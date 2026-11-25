# Amendment 1 to the pre-registration of November 25, 2026

Registered: November 25, 2026 (GitHub commit), before any payroll price, futures or first-release data was examined.

Purpose: fix the implementation details of secondary test S2 (payrolls) that the original text left open. Nothing in the primary test or in S1 changes.

## S2 signal construction
- K: mean of the Kalshi payrolls (KXPAYROLLS, including legacy PAYROLLS and PROLLS tickers) distribution at the last hourly candle ending by 16:00 ET on the business day before the Employment Situation release.
  - Per strike: bid/ask mid, last trade if a side is missing.
  - Ladder made non-increasing across strikes (pool-adjacent-violators), then differenced into bins.
  - Bin values: midpoint of adjacent strikes; below the lowest strike, the lowest strike minus half the first gap; above the highest strike, the highest strike plus half the last gap.
  - Raw prices, no longshot correction.
- T: average of the previous 12 monthly payroll changes as first published (ALFRED PAYEMS real-time vintages: level of month m minus level of month m-1, both from the vintage in which month m first appeared), using only prints released before the trade.
- e_trend = K - T, in jobs.
- Release date: the US Eastern date of the latest close time among the event's Kalshi markets.

## S2 trade
- As registered: at 16:05 ET on the business day before release, short one ZN (volume-front continuous contract) if e_trend > 0, long if < 0, no trade if 0; exit at the last trade before 08:30:00 ET. Costs 1/64 point plus 0.005 points per round trip.
- Price at time t: close of the last 1-minute bar ending by t, within the 30 minutes before t; otherwise no trade.
- If the continuous front contract changes between entry and exit (a roll), the release is skipped (data limitation: only the front contract is pulled).

## S2 exclusions
- Releases delayed or merged by the October to November 2025 government shutdown: the September, October and November 2025 reports (Kalshi event codes 25SEP, 25OCT, 25NOV).
- Releases with fewer than 2 Kalshi strikes priced at the snapshot, or fewer than 12 prior first-published prints.

## S2 inference
- One-sided t-test, mean net P&L per trade > 0. Also: hit rate with a one-sided binomial test, mean gross P&L, per-trade Sharpe.
- Holm correction with S1 (ZT on CPI) at family alpha 0.05 once S1 is available; until then S2's p-value is reported on its own.
