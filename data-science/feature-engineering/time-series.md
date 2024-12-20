# Time Series

#### **1. Aggregate Data**

#### **2. Aggregate and Reshape**

Aggregate the data by `Store_ID` and generate time-series features:

* Group by `Store_ID`.
* Generate features for each store based on the time series.

***

#### **3. Feature Engineering**

**a. Sales Statistics**

Compute aggregate statistics for each store:

* Mean sales
* Median sales
* Standard deviation
* Min and max sales
* Coefficient of variation (std/mean)

**b. Temporal Trends**

Extract temporal trends:

* **Seasonality**: Average sales by day of the week, month, or season.
* **Growth/Decline**: Sales trend (slope) over time.
* **Autocorrelation**: Correlation between lagged sales.

**c. Distribution Features**

Analyze sales distribution:

* Percentiles (e.g., 25th, 50th, 75th percentiles)
* Skewness and kurtosis

**d. Time-Based Features**

Capture periodic patterns:

* Ratio of weekend sales to weekday sales
* Sales spikes (e.g., max sales deviation from average)

**e. Missing Data**

Handle and feature missing data:

* Count or percentage of missing sales values
* Fill missing values using interpolation or a constant.

**f. Derived Metrics**

Derive additional metrics:

* Rolling averages (e.g., 7-day or 30-day moving averages)
* Rolling standard deviation
* Ratio of sales to previous periods (e.g., week-over-week or month-over-month growth)

**g. External Factors (Optional)**

Incorporate external data:

* Holidays (binary feature: 1 if the date is a holiday, 0 otherwise)
* Local weather conditions (average temperature, precipitation)
* Macroeconomic indicators (CPI, local unemployment rate)

#### **3. Autocorrelation**

For autocorrelation:

* Compute the autocorrelation for each store over the entire time series.
* Use this single autocorrelation value as a feature.

**Example (Python)**

```python
def compute_autocorr(series, lag=1):
    return series.autocorr(lag=lag)

data['autocorr'] = data.groupby('Store_ID')['Sales'].transform(lambda x: compute_autocorr(x, lag=1))

autocorr_features = data.groupby('Store_ID').agg(
    autocorr_mean=('autocorr', 'mean')
)
```

***

#### **4. Growth/Decline**

Growth/decline trends can be summarized using:

* **Slope of the linear trend**: Fit a line to the sales data and use its slope.
* **Percentage change**: Compare the first and last sales values.
* **Compound annual growth rate (CAGR)**: Measure overall growth.

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
