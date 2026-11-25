# How macro news gets priced: Stage 1 data pulls

```
pip install -r requirements.txt
python src/pull_kalshi.py                 # ~6 min: ~500 markets + hourly candles, cached in data/raw/kalshi_json/
python src/pull_kalshi.py --series KXCPI KXCPICORE   # add core CPI m/m
python src/pull_nowcast.py                # ~10 MB JSON; if blocked, save the URL from the browser and use --file
python src/pull_rates.py                  # FRED DGS2
```

All outputs land in `data/raw/`. No API keys needed. Reruns resume from the candle cache.

No-look-ahead notes
- Kalshi `close_time` is a few minutes before the 8:30 ET release; snapshot from candles ending before it.
- Nowcast value dated d is the nowcast shown on d (~10:00 ET update): use dates strictly before release day.
- Realized print comes from the settled ladder (`print_flag == "exact"`); `interval` means a strike gap, check by hand.
- `no_report` marks Oct 2025 (no BLS release); drop it.
