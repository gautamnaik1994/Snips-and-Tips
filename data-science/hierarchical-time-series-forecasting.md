# Hierarchical Time Series Forecasting

#### Key Concepts:

1. **Top-down Approach:**
   * Start by forecasting at the highest level (e.g., total sales for a company).
   * Allocate the forecast to lower levels based on historical proportions or other rules.
2. **Bottom-up Approach:**
   * Forecast at the lowest level (e.g., sales for each store).
   * Aggregate these forecasts to get higher-level forecasts.
3. **Middle-out Approach:**
   * Combine top-down and bottom-up approaches.
   * Start from an intermediate level and use it to refine forecasts at higher and lower levels.
4. **Reconciliation:**
   * Adjust forecasts across levels to ensure coherence. For example, the sum of city-level forecasts should equal the state-level forecast.

#### Algorithms for HTSF:

1. **Forecast at each level independently:**
   * Use models like ARIMA, SARIMA, or machine learning models at each hierarchy level.
   * Reconcile forecasts afterward.
2. **Reconciliation Methods:**
   * **OLS Reconciliation:** Uses ordinary least squares to adjust forecasts.
   * **MinT (Minimum Trace):** Minimizes forecast errors while reconciling.
   * **BU (Bottom-Up):** Aggregates lower-level forecasts directly.
   * **TD (Top-Down):** Allocates higher-level forecasts to lower levels.

#### Tools for HTSF:

1. **R:**
   * `hts` package for hierarchical and grouped time series forecasting.
   * `forecast` package for general time series modeling.
2. **Python:**
   * **Statsmodels:** For ARIMA/SARIMAX modeling.
   * **scikit-hts:** For hierarchical time series forecasting.
   * **Prophet:** Can be adapted for hierarchical forecasting using aggregation.
3. **Others:**
   * Custom models using machine learning (e.g., LSTMs or XGBoost).

Example of **Hierarchical Time Series Forecasting** for sales data of 10 stores in 5 regions, where each region has 2 stores, and one store in each region is in an urban city.

***

#### **Scenario:**

1. **Hierarchy:**
   * **Level 1 (Total Sales):** Aggregated sales for all 10 stores.
   * **Level 2 (Regions):** Aggregated sales for 5 regions.
   * **Level 3 (Stores):** Individual sales for 10 stores.
   * **Urban City Classification:** Indicates whether a store is in an urban city (binary feature).
2. **Objective:**
   * Forecast future sales for all stores.
   * Ensure that:
     * Store sales sum up to region sales.
     * Region sales sum up to total sales.

***

#### **Synthetic Data Generation:**

For simplicity, we'll generate random sales data to demonstrate the hierarchy.

```python
import pandas as pd
import numpy as np

# Generate synthetic sales data
np.random.seed(42)

# Store details
stores = [f"Store_{i+1}" for i in range(10)]
regions = [f"Region_{i//2 + 1}" for i in range(10)]  # 2 stores per region
urban_city = [True if i % 2 == 0 else False for i in range(10)]  # Urban city flag

# Generate random sales for each store
dates = pd.date_range(start="2024-01-01", periods=30)  # 30 days of data
data = {
    "Date": np.repeat(dates, len(stores)),
    "Store": stores * len(dates),
    "Region": regions * len(dates),
    "Urban_City": urban_city * len(dates),
    "Sales": np.random.randint(50, 200, len(stores) * len(dates)),
}

df = pd.DataFrame(data)

# Aggregate sales at region and total levels
region_sales = df.groupby(["Date", "Region"])["Sales"].sum().reset_index()
total_sales = df.groupby("Date")["Sales"].sum().reset_index()

# Display the datasets
print("Sample Store-Level Data:")
print(df.head())

print("\nSample Region-Level Data:")
print(region_sales.head())

print("\nSample Total Sales Data:")
print(total_sales.head())
```

***

#### **Forecasting Example:**

1. **Forecast at Store Level:**
   * Use any time series model (e.g., ARIMA, SARIMA) for individual stores.
   * Example for one store:

```python
from statsmodels.tsa.arima.model import ARIMA

# Forecast for one store (e.g., Store_1)
store_1_sales = df[df["Store"] == "Store_1"].set_index("Date")["Sales"]
model = ARIMA(store_1_sales, order=(1, 1, 1))
model_fit = model.fit()
forecast_store_1 = model_fit.forecast(steps=7)  # Forecast 7 days ahead
print(f"Forecast for Store_1: {forecast_store_1}")
```

2. **Aggregate to Region Level:**
   * Sum the forecasts for the two stores in each region.

```python
region_forecasts = (
    df.groupby(["Date", "Region"])["Sales"]
    .sum()
    .groupby("Region")
    .apply(lambda x: x.resample("D").sum())
)
```

3. **Aggregate to Total Sales:**
   * Sum all region forecasts to get the total sales.

```python
total_forecast = region_forecasts.sum(level="Date")
print(f"Total Sales Forecast: {total_forecast}")
```

***

#### **Reconciliation:**

To ensure consistency:

* Use **Bottom-Up**, **Top-Down**, or **MinT Reconciliation** methods to adjust forecasts.
* Example using Bottom-Up:
  * Sum store-level forecasts to match region-level.
  * Sum region-level forecasts to match total sales.
