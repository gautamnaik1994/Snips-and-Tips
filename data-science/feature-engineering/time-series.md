# Time Series

#### **1. Aggregate Data**

#### **2. Aggregate and Reshape**

Aggregate the data by `Store_ID` and generate time-series features:

- Group by `Store_ID`.
- Generate features for each store based on the time series.

---

#### **3. Feature Engineering**

**a. Sales Statistics**

Compute aggregate statistics for each store:

- Mean sales
- Median sales
- Standard deviation
- Min and max sales
- Coefficient of variation (std/mean)

**b. Temporal Trends**

Extract temporal trends:

- **Seasonality**: Average sales by day of the week, month, or season.
- **Growth/Decline**: Sales trend (slope) over time.
- **Autocorrelation**: Correlation between lagged sales.

**c. Distribution Features**

Analyze sales distribution:

- Percentiles (e.g., 25th, 50th, 75th percentiles)
- Skewness and kurtosis

**d. Time-Based Features**

Capture periodic patterns:

- Ratio of weekend sales to weekday sales
- Sales spikes (e.g., max sales deviation from average)

**e. Missing Data**

Handle and feature missing data:

- Count or percentage of missing sales values
- Fill missing values using interpolation or a constant.

**f. Derived Metrics**

Derive additional metrics:

- Rolling averages (e.g., 7-day or 30-day moving averages)
- Rolling standard deviation
- Ratio of sales to previous periods (e.g., week-over-week or month-over-month growth)

**g. External Factors (Optional)**

Incorporate external data:

- Holidays (binary feature: 1 if the date is a holiday, 0 otherwise)
- Local weather conditions (average temperature, precipitation)
- Macroeconomic indicators (CPI, local unemployment rate)

#### **3. Autocorrelation**

For autocorrelation:

- Compute the autocorrelation for each store over the entire time series.
- Use this single autocorrelation value as a feature.

**Example (Python)**

```python
def compute_autocorr(series, lag=1):
    return series.autocorr(lag=lag)

data['autocorr'] = data.groupby('Store_ID')['Sales'].transform(lambda x: compute_autocorr(x, lag=1))

autocorr_features = data.groupby('Store_ID').agg(
    autocorr_mean=('autocorr', 'mean')
)
```

---

#### **4. Growth/Decline**

Growth/decline trends can be summarized using:

- **Slope of the linear trend**: Fit a line to the sales data and use its slope.
- **Percentage change**: Compare the first and last sales values.
- **Compound annual growth rate (CAGR)**: Measure overall growth.

**Example (Python)**

```python
def compute_trend(series):
    if len(series) > 1:
        return np.polyfit(range(len(series)), series, 1)[0]  # Slope of trend line
    return 0

data['sales_trend'] = data.groupby('Store_ID')['Sales'].transform(compute_trend)

trend_features = data.groupby('Store_ID').agg(
    sales_trend_mean=('sales_trend', 'mean')
)
```

## LOWESS Smoothing

Locally Weighted Scatterplot Smoothing (LOWESS) is a non-parametric regression
method that fits multiple regression lines to local subsets of the data to
create a smooth curve. It is useful for visualizing trends in time series data.

Use this method to smooth noisy time series data and identify underlying trends.
This can be used inplace of moving averages for trend analysis.

Pros:

- LOWESS is robust to outliers and can capture non-linear trends.
- It can be used to smooth noisy time series data.

Cons:

- LOWESS is computationally expensive for large datasets.
- The smoothing parameter `frac` needs to be tuned for optimal results.

**Example (Python)**

```python
from statsmodels.nonparametric.smoothers_lowess import lowess

def smooth_series(series, frac=0.1):
    smoothed = lowess(series, range(len(series)), frac=frac)
    return smoothed[:, 1]

data['smoothed_sales'] = data.groupby('Store_ID')['Sales'].transform(smooth_series)
```

## MSTL Decomposition

Multiple Seasonal-Trend decomposition using LOESS (MSTL) is a method for
decomposing time series data into seasonal, trend, and remainder components. It
is an extension of the STL decomposition method that can handle multiple
seasonalities.

Use this method to extract seasonal patterns and trends from time series data.
This can be useful for forecasting and anomaly detection.

Pros:

- MSTL can handle multiple seasonal patterns in the data.
- It provides interpretable components for analysis.

Cons:

- MSTL is computationally expensive for large datasets.
- The decomposition may not work well for time series with irregular patterns.

**Example (Python)**

```python
from stldecompose import decompose

def decompose_series(series):
    result = decompose(series, period=7)  # Daily seasonality
    return result.trend, result.seasonal, result.resid

data['trend'], data['seasonal'], data['residual'] =
    zip(*data.groupby('Store_ID')['Sales'].transform(decompose_series))
```
