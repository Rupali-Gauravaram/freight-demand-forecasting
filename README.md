# Freight Demand Forecasting

I forecast monthly US rail container volumes two years ahead with a seasonal ARIMA (SARIMA) model, and checked whether it does better than two simple baseline rules. It does: the model is off by 2.8% on average, against 3.8% and 6.9% for the baselines.

## The problem

Logistics and supply chain teams plan capacity, equipment and staffing months ahead. To do that they need to know how much freight to expect, and which months will be busy.

## Data

**Monthly Transportation Statistics** from the US Bureau of Transportation Statistics (public data). The file holds 136 monthly series. I used one: **Freight Rail Intermodal Units**, the number of shipping containers and trailers moved by rail each month.

- 451 months, from January 1989 to July 2026
- The source file had one duplicated month (April 2023, identical values), which I removed
- After that, no missing months

## What I did

1. **Cleaned and checked the timeline:** date range, duplicates and missing months.
2. **Looked at trend and seasonality** with a plot, monthly averages and a decomposition.
3. **Split the data by time:** the last 24 months are the test set. Not a random split, because the model must only learn from the past.
4. **Built two baselines:**
   - Naive: every future month equals the last known month
   - Seasonal naive: every future month equals the same month a year earlier
5. **Prepared the data for the model:** tested for stationarity (ADF test), then applied one ordinary difference for the trend and one seasonal difference for the yearly pattern.
6. **Read the ACF and PACF plots** to choose candidate models.
7. **Fitted four SARIMA models** and compared them by AIC.
8. **Forecast the 24 test months** and compared the model with the baselines.

## Results

| Model | MAE (units per month) | MAPE |
|---|---|---|
| **SARIMA (0,1,0)(0,1,1,12)** | **32,924** | **2.8%** |
| Naive | 46,351 | 3.8% |
| Seasonal naive | 82,843 | 6.9% |

The series averages about 1.19 million units a month in the test period, so the model is off by about 33,000 units a month.

## What I found

- **The series has an upward trend that levels off after 2016,** and a stable yearly pattern: lowest around February, highest in August and October.
- **The simple baseline beat the seasonal one.** The seasonal baseline copied 2023-24, which was a low year, so it had the right shape but sat too low. The naive baseline had the right level but is a flat line.
- **A lower error is not always a more useful forecast.** The flat line cannot tell a planner which months will be busy.
- **The four SARIMA models had almost the same AIC,** so I chose the simplest one.
- **The model did better than both baselines** because it follows the current level of demand and the yearly pattern together.

## Limits

- The model was tested on one 24-month period with no major shock. It would not have predicted the 2008-09 financial crisis or the drop in 2020.
- Forecasts further ahead are less certain.
- It uses only the series' own history, with no outside factors such as fuel prices or the wider economy.

## What I would do next

- Test the model over several different periods, not just one.
- Compare it with other forecasting methods.

## Files

```
freight_demand_forecasting.ipynb        # the full notebook, with outputs
monthly_transportation_statistics.csv   # the data (US Bureau of Transportation Statistics)
requirements.txt
```

## Run it

```bash
pip install -r requirements.txt
jupyter notebook freight_demand_forecasting.ipynb
```

## Stack

Python · pandas · NumPy · Matplotlib · statsmodels · scikit-learn
