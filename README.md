# Trade Sentiment Analysis

Analysis of trader behavior and performance (PnL, win rate, drawdown, leverage proxy, long/short bias)
against the Crypto Fear & Greed Index, segmented by trade size, frequency, and consistency — with two
data-driven "rules of thumb" derived at the end.

## Files

- `Trade_Analysis.ipynb` — the full analysis notebook (Parts A, B, and C)

## Requirements

- Python 3.9+
- Jupyter (Notebook or Lab)
- Packages: `pandas`, `numpy`, `matplotlib`

Install everything with:

```bash
pip install jupyter pandas numpy matplotlib
```

## Data setup

The notebook needs two input CSV files:

1. **Trades file** — must contain, at minimum: `Account`, `Timestamp IST` (format `dd-mm-yyyy HH:MM`),
   `Direction`, `Size USD`, `Closed PnL`.
2. **Fear & Greed index file** — must contain: `date`, `value`, `classification`.

Before running, open the first cell of the notebook and point the two path variables at your files:

```python
TRADES_PATH = "path/to/historical_data.csv"
FG_PATH = "path/to/fear_greed_index.csv"
```

> The notebook currently has these hardcoded to local Windows paths (`E:/Downloads/...`) — update them
> to wherever your CSVs live before running, or the first cell will fail with a `FileNotFoundError`.

## How to execute

1. Place `Trade_Analysis.ipynb` and your two CSV files somewhere on disk (they don't need to be in the
   same folder, as long as the paths in step 2 are correct).
2. Launch Jupyter from that folder:
   ```bash
   jupyter notebook
   ```
   (or `jupyter lab`, or open the file directly in VS Code / another notebook-capable editor)
3. Open `Trade_Analysis.ipynb`.
4. Edit the `TRADES_PATH` and `FG_PATH` variables in the first cell as described above.
5. Run all cells top to bottom:
   - Jupyter Notebook/Lab: **Run → Run All Cells**
   - Or via command line, without opening a UI:
     ```bash
     jupyter nbconvert --to notebook --execute --inplace Trade_Analysis.ipynb
     ```

Cells must run in order — later cells (Part B and Part C) depend on variables created earlier
(`merged`, `daily`, `pnl_by_regime`, `behavior_summary`, `acct_stats`, etc.), so don't skip around.

## What gets produced

Running the notebook end to end will:

- Print data-quality checks (shape, dtypes, missing values, duplicates) for both input files
- Write these CSVs to the working directory:
  - `daily_pnl_with_sentiment.csv`
  - `win_rate_per_account.csv`
  - `avg_trade_size_per_account.csv`
  - `trades_per_day.csv`
  - `long_short_ratio_per_account.csv`
- Save these chart images to the working directory:
  - `q1_performance_by_regime.png` — PnL, win rate, drawdown by Fear/Neutral/Greed
  - `q2_behavior_by_regime.png` — trade frequency, trade size, long/short bias by regime
  - `q3_segments.png` — win rate / PnL by size, frequency, and consistency segments
- Print three data-backed insights (Part B) and two data-backed strategy rules of thumb (Part C),
  with every number computed live from the tables above rather than hardcoded

## Notes/limitations

- "Leverage" is not directly available in the trades data, so **average trade size (USD)** is used
  throughout as a leverage proxy — this is called out explicitly in the notebook.
- Win rate is computed only on closing trades (`Closed PnL != 0`); trades with zero PnL (opens,
  transfers, etc.) are excluded from that calculation.
- The drawdown figure is a proxy: running peak minus current cumulative PnL per account, not a true
  mark-to-market drawdown.
- Trade rows with no matching Fear & Greed date are dropped before the regime-level analysis in Part B/C.
