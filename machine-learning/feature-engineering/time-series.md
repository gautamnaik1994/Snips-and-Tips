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

## LOWESS Smoothing

Locally Weighted Scatterplot Smoothing (LOWESS) is a non-parametric regression method that fits multiple regression lines to local subsets of the data to create a smooth curve. It is useful for visualizing trends in time series data.

Use this method to smooth noisy time series data and identify underlying trends. This can be used inplace of moving averages for trend analysis.

Pros:

* LOWESS is robust to outliers and can capture non-linear trends.
* It can be used to smooth noisy time series data.

Cons:

* LOWESS is computationally expensive for large datasets.
* The smoothing parameter `frac` needs to be tuned for optimal results.

**Example (Python)**

```python
from statsmodels.nonparametric.smoothers_lowess import lowess

def smooth_series(series, frac=0.1):
    smoothed = lowess(series, range(len(series)), frac=frac)
    return smoothed[:, 1]

data['smoothed_sales'] = data.groupby('Store_ID')['Sales'].transform(smooth_series)
```

## MSTL Decomposition

Multiple Seasonal-Trend decomposition using LOESS (MSTL) is a method for decomposing time series data into seasonal, trend, and remainder components. It is an extension of the STL decomposition method that can handle multiple seasonalities.

Use this method to extract seasonal patterns and trends from time series data. This can be useful for forecasting and anomaly detection.

Pros:

* MSTL can handle multiple seasonal patterns in the data.
* It provides interpretable components for analysis.

Cons:

* MSTL is computationally expensive for large datasets.
* The decomposition may not work well for time series with irregular patterns.

**Example (Python)**

```python
from stldecompose import decompose

def decompose_series(series):
    result = decompose(series, period=7)  # Daily seasonality
    return result.trend, result.seasonal, result.resid

data['trend'], data['seasonal'], data['residual'] =
    zip(*data.groupby('Store_ID')['Sales'].transform(decompose_series))
```

## More Techniques

Comprehensive list of feature engineering techniques to enhance forecasting models, covering both **temporal** and **non-temporal** approaches. These techniques enable your machine learning models to capture short-term trends, long-term trends, seasonality, and external factors effectively.

***

#### **1. Lag Features (Temporal)**

Lag features use past values of the target variable to predict the future.

**Examples:**

* **Single Lag**: `sales_lag_1` (sales yesterday)
* **Multiple Lags**: `sales_lag_1, sales_lag_2, ..., sales_lag_30` (last 30 days)
* **Seasonal Lags**: Lagged values for the same day in previous periods, e.g., `sales_lag_365` for annual seasonality.

**Use Case:**

Captures short-term dependencies.

***

#### **2. Rolling/Window Features (Temporal)**

Rolling statistics summarize trends over a specific window size.

**Examples:**

* **Rolling Mean**: `rolling_mean_7` (average sales over the last 7 days)
* **Rolling Std**: `rolling_std_30` (volatility over the last 30 days)
* **Rolling Sum**: `rolling_sum_14`
* **Rolling Min/Max**: `rolling_min_30`, `rolling_max_30`

**Use Case:**

Captures smoothed local trends and variability.

***

#### **3. Expanding Window Features (Temporal)**

Cumulative statistics from the start of the time series.

**Examples:**

* **Cumulative Mean**: `expanding_mean`
* **Cumulative Sum**: `expanding_sum`

**Use Case:**

Captures long-term trends and changes over time.

***

#### **4. Time-Based Features (Non-Temporal)**

Derived from the date itself to capture seasonality or temporal patterns.

**Examples:**

* **Static Features**:
  * Day of the week: `day_of_week` (Monday = 0, Sunday = 6)
  * Month: `month`
  * Quarter: `quarter`
  * Year: `year`
* **Cyclic Transformations**:
  * `sin_day_of_year = sin(2 * pi * day_of_year / 365)`
  * `cos_day_of_year = cos(2 * pi * day_of_year / 365)`

**Use Case:**

Captures fixed recurring patterns (e.g., weekly or monthly seasonality).

***

#### **5. Trend Features (Temporal)**

Capture long-term trends over time.

**Examples:**

* **Time Index**: Number of days since the start of the dataset.
* **Polynomial Trends**: `time_index^2` or higher-order terms.
* **Interaction with Seasonality**: `time_index * sin_day_of_year`

**Use Case:**

Models growth or decline over time.

***

#### **6. External Factors (Non-Temporal)**

External data sources that influence the target.

**Examples:**

* **Holidays**: Binary flags for holidays (e.g., `is_holiday`).
* **Weather Data**: Temperature, rainfall, or snowfall.
* **Marketing Campaigns**: Binary or categorical variables for promotions.
* **Economic Indicators**: Inflation rates, unemployment rates, etc.

**Use Case:**

Incorporates real-world influences beyond historical data.

***

#### **7. Interaction Features (Temporal/Non-Temporal)**

Combine multiple features to capture complex relationships.

**Examples:**

* `day_of_week * sales_lag_1` (interaction between day and recent sales)
* `rolling_mean_7 * sin_day_of_year` (interaction between local trend and seasonality)

**Use Case:**

Improves feature richness for better pattern capture.

***

#### **8. Count-Based Features (Temporal)**

Capture the frequency of events within a period.

**Examples:**

* Count of zero sales days in the last 30 days: `zero_sales_count`
* Count of high sales days in the last week: `high_sales_count`

**Use Case:**

Highlights event-based patterns in the data.

***

#### **9. Change-Based Features (Temporal)**

Capture the rate of change or difference between consecutive observations.

**Examples:**

* **Day-to-Day Change**: `change_1 = sales - sales_lag_1`
* **Percentage Change**: `(sales - sales_lag_1) / sales_lag_1`
* **Rolling Change**: Change in rolling mean: `rolling_mean_7 - rolling_mean_30`

**Use Case:**

Highlights accelerating or decelerating trends.

***

#### **10. Aggregated Features (Temporal and Non-Temporal)**

Summarize historical data across multiple dimensions.

**Examples:**

* Total sales for each store: `total_sales_store`
* Mean sales for each product: `mean_sales_product`
* Median sales for a specific month across years.

**Use Case:**

Useful in multi-hierarchy forecasting problems (e.g., products, stores, regions).

***

#### **11. Fourier Features (Temporal)**

Advanced periodicity representation using Fourier transforms.

**Examples:**

* Low-order Fourier terms for seasonality:
  * `sin(2 * pi * t / T)`
  * `cos(2 * pi * t / T)`

**Use Case:**

Captures multiple seasonalities more compactly than lag-based features.

***

#### **12. Target Encoding (Non-Temporal)**

Encode categorical variables using aggregated statistics of the target.

**Examples:**

* Mean sales per store: `store_target_mean`
* Mean sales per month: `month_target_mean`

**Use Case:**

Captures the impact of categorical variables on the target.

***

#### **13. Residual Features (Temporal)**

Incorporate errors from a baseline model (e.g., SARIMA or linear regression).

**Example:**

* Compute residuals: `residual = actual - predicted_baseline`

**Use Case:**

Allows machine learning models to learn residual patterns not captured by the baseline.

***

#### **14. Clustering-Based Features (Non-Temporal)**

Group observations based on patterns in external features.

**Examples:**

* Cluster stores/products by sales patterns.
* Assign cluster IDs as features.

**Use Case:**

Reduces dimensionality while capturing group-level behavior.

***

#### **15. Feature Scaling and Transformation**

Apply scaling or transformations to improve model interpretability and performance.

**Examples:**

* Log Transformation: `log_sales = log(sales + 1)`
* Standardization: `(sales - mean) / std`
* Min-Max Scaling: `(sales - min) / (max - min)`

**Use Case:**

Helps models deal with outliers and non-linear relationships.

***

#### **Comprehensive Feature Set for Forecasting**

| **Feature Type**          | **Examples**                          | **Use Case**                        |
| ------------------------- | ------------------------------------- | ----------------------------------- |
| Lag Features              | `sales_lag_1, sales_lag_30`           | Short-term dependencies             |
| Rolling Features          | `rolling_mean_7, rolling_std_30`      | Local trends and volatility         |
| Expanding Window Features | `expanding_mean, expanding_sum`       | Long-term cumulative behavior       |
| Time-Based Features       | `day_of_week, sin_day_of_year`        | Fixed seasonality                   |
| Trend Features            | `time_index, time_index^2`            | Growth or decline over time         |
| External Factors          | `is_holiday, temperature`             | Real-world influences               |
| Interaction Features      | `rolling_mean_7 * sin_day_of_year`    | Complex relationships               |
| Change-Based Features     | `change_1, percentage_change`         | Highlight acceleration/deceleration |
| Aggregated Features       | `total_sales_store, mean_sales_month` | Hierarchical forecasting            |
| Fourier Features          | `sin(2*pi*t/T), cos(2*pi*t/T)`        | Multiple seasonalities              |
| Residual Features         | `residual`                            | Learn residual patterns             |
| Clustering-Based Features | `store_cluster`                       | Group-level behavior                |

***

#### Next Steps
