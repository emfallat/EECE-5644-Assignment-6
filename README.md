# EECE 5644 Assignment 6

## What this project does
- LSTM forecasting model on the UCI Household Power Consumption dataset (~2M minute-level readings from one French household, Dec 2006 - Nov 2010).
- Cleans and resamples the raw minute-level data to hourly aggregates.
- Trains an LSTM that uses the past 7 days (168 hours) of readings to forecast the next 24 hours of global active power consumption.

## Repository contents
| File | Description |
|---|---|
| `energy_consumption.ipynb` | Loads `household_power_consumption.txt`, handles missing values, resamples to hourly aggregates, builds chronological train/val/test splits, scales features, trains the LSTM, and evaluates forecast accuracy, inference latency, and anomaly alerting |
| `household_power_consumption.txt` | Raw minute-level dataset  |
| `power_consumption.csv` | Raw dataset in csv format |
| `requirements.txt` | Python library versions needed to reproduce this work |

## Pipeline overview
1. **Data preprocessing and gap handling**: fill the ~1.25% of minute-level readings missing from sensor outages (short gaps via time interpolation, longer gaps via same-time-last-week fill), then resample to hourly aggregates (mean for power/voltage/intensity, sum for sub-metering channels, plus a peak-power feature).
2. **Prediction accuracy**: LSTM forecasts the next 24 hours of `Global_active_power` from a 168-hour input window, targeting MAPE ≤ 10% on the test set.
3. **Temporal integrity**: chronological train/val/test splits (last 6 months test, prior 3 months validation) with MinMax scaling fit only on the training set.
4. **Inference latency**: single 24-hour forecast timed over multiple runs, targeting < 500 ms.
5. **Anomaly alerting**: flags households when residuals exceed a 3-std threshold for 2+ consecutive hours, targeting a false-alarm rate below 5%.

## How to run
1. Clone this repository.
2. Download `household_power_consumption.txt` from the [UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/235/individual+household+electric+power+consumption) and place it in the repo root.
3. Create a virtual environment and install dependencies:
   ```bash
   python -m venv venv
   source venv/bin/activate    # Windows: venv\Scripts\activate
   pip install requirements.txt
   ```
4. Open `energy_consumption.ipynb` in Jupyter or VS Code.
5. Run all cells from top to bottom (**Restart Kernel + Run All**).