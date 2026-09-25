# Zillow Home Values Analysis & Forecasting

**Author:** Hala Salah

📊 **[View the interactive Tableau dashboard](https://public.tableau.com/views/ZillowHomeValuesAnalysis/HomeValuesAnalysis)**

## Overview

This project analyzes U.S. home values from Zillow at the zip-code level. It has two parts:
1. **Data preparation and visualization:** transforming the raw data into a clean time series and building an interactive Tableau dashboard of home value trends across five western states (2010–2020).
2. **Forecasting:** predicting average home values in Oregon for the next 12 months using ARIMA/SARIMA models.

## Data

- **Source:** Zillow Home Value Index (ZHVI) by zip code
- **Size:** 27,442 zip codes across 275 monthly date columns (2000–2022)
- **Format:** wide format, with one column per month

## Data Preparation

- **Reshaped** the data from wide to long format with `melt`, turning 275 date columns into a single `Date` column
- **Converted** dates from text (`DDMMYYYY`) to a datetime index
- **Imputed 1.8 million missing values** using linear interpolation within each zip code's own time series, instead of a global average, since home prices vary drastically by region
- **Filtered** the data to five states (CA, WA, OR, AZ, NV) and the years 2010–2020, and exported it for Tableau

## Key Findings (2010–2020)

- **California is consistently the most expensive state** by a wide margin, rising from about $410K in 2010 to nearly $680K in 2020, roughly double any other state in the group.
- **All five states dipped between 2010 and 2012**, reflecting the lingering effects of the 2008 housing crash.
- **From 2012 onward, all five states recovered and grew steadily** through 2020.

<!-- Add a screenshot of the Tableau dashboard here -->

## Forecasting Oregon Home Values

### Approach
- Built a monthly time series of average Oregon home values (2000–2018)
- Found the series non-stationary (ADF p-value = 0.78) and applied second-order differencing (d = 2)
- Found that seasonality was only about **0.18% of the average home value**, too weak to be worth modeling
- Compared a manual SARIMA model (orders chosen from ACF/PACF plots) with a model selected by `auto_arima`, using the last 12 months as a test set

### Results (12-month test set, 2018)

| Model | MAE | RMSE | R² | MAPE |
|---|---|---|---|---|
| Manual SARIMA(2,2,1)(1,0,1,12) | $1,415 | $1,936 | 0.837 | 0.45% |
| **auto_arima ARIMA(1,2,0)** | **$822** | **$1,091** | **0.948** | **0.26%** |

**Selected model:** ARIMA(1,2,0), which outperformed the manual model on every metric while being much simpler. It dropped all seasonal terms, which is consistent with the weak seasonality found earlier, and is a good example of the principle of parsimony: simpler models often generalize better.

### Forecast for 2019

After refitting the selected model on the full 2000–2018 data:
- **Last actual value (Dec 2018):** $317,156
- **Forecast for Dec 2019:** $335,092
- **Expected change:** **+5.7%** over 12 months

The 95% confidence interval widens gradually over the forecast horizon, but even its lower bound stays above the last observed value, meaning the model expects Oregon home values to keep rising through 2019.

<!-- Add the final forecast chart here -->

## Tools

Python, pandas, NumPy, Matplotlib, statsmodels, pmdarima, scikit-learn, Tableau Public

## Repository Contents

- `beltExamTimeSeries.ipynb`: the full analysis and forecasting notebook
- `Data/data-for-tableau.zip`: the filtered dataset used for the Tableau dashboard
