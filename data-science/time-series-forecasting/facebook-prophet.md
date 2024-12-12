# Facebook Prophet

### Hyperparameters

Here is a comprehensive list of the hyperparameters available in Facebook's Prophet library, along with their significance, explanation, and possible ranges of values:

***

#### **1. `growth`**

* **Significance:** Determines the trend type of the time series.
* **Explanation:** Prophet supports two types of trends:
  * `"linear"`: A linear trend model.
  * `"logistic"`: A logistic growth trend model with a cap and floor.
* **Possible Values:** `"linear"`, `"logistic"`

***

#### **2. `changepoint_prior_scale`**

* **Significance:** Controls the flexibility of the trend by determining the rate of change at changepoints.
* **Explanation:** Higher values make the trend more flexible; lower values make it less flexible and smoother.
* **Possible Values:** Typically in the range `[0.001, 0.5]`.

***

#### **3. `changepoints`**

* **Significance:** Specifies the locations where trend changes are allowed.
* **Explanation:** If not specified, Prophet automatically places them. A list of dates can be provided to define custom changepoints.
* **Possible Values:** List of dates (e.g., `["2023-01-01", "2023-06-01"]`) or `None`.

***

#### **4. `seasonality_mode`**

* **Significance:** Controls the type of seasonality.
* **Explanation:**
  * `"additive"`: Adds seasonal components to the trend.
  * `"multiplicative"`: Multiplies seasonal components with the trend.
* **Possible Values:** `"additive"`, `"multiplicative"`

***

#### **5. `seasonality_prior_scale`**

* **Significance:** Controls the flexibility of the seasonality components.
* **Explanation:** Larger values allow for more flexible seasonality patterns.
* **Possible Values:** Typically in the range `[0.01, 10]`.

***

#### **6. `holidays_prior_scale`**

* **Significance:** Controls the flexibility of holiday effects.
* **Explanation:** Larger values allow the model to better capture holiday effects but may lead to overfitting.
* **Possible Values:** Typically in the range `[0.01, 10]`.

***

#### **7. `interval_width`**

* **Significance:** Determines the uncertainty interval width for forecasts.
* **Explanation:** The confidence interval for predictions is centered on the forecast, with a default of 80% coverage.
* **Possible Values:** A float in the range `[0, 1]` (e.g., `0.8` for 80%).

***

#### **8. `uncertainty_samples`**

* **Significance:** Specifies the number of samples drawn to estimate uncertainty intervals.
* **Explanation:** More samples increase precision but require more computation.
* **Possible Values:** Integer (e.g., `1000` by default).

***

#### **9. `n_changepoints`**

* **Significance:** Determines the number of potential changepoints in the time series.
* **Explanation:** Controls how many trend changes are considered. Prophet places these uniformly over the first 80% of the data unless `changepoints` is provided.
* **Possible Values:** Integer (default: `25`).

***

#### **10. `yearly_seasonality`**

* **Significance:** Specifies whether yearly seasonality is included in the model.
* **Explanation:**
  * `True`: Automatically detects yearly seasonality.
  * `False`: Disables it.
  * Integer: Specifies the Fourier order for custom yearly seasonality.
* **Possible Values:** `True`, `False`, or an integer.

***

#### **11. `weekly_seasonality`**

* **Significance:** Specifies whether weekly seasonality is included in the model.
* **Explanation:** Similar to `yearly_seasonality`, but for weekly patterns.
* **Possible Values:** `True`, `False`, or an integer.

***

#### **12. `daily_seasonality`**

* **Significance:** Specifies whether daily seasonality is included in the model.
* **Explanation:** Similar to `yearly_seasonality`, but for daily patterns.
* **Possible Values:** `True`, `False`, or an integer.

***

#### **13. `holidays`**

* **Significance:** Defines custom holidays and their effects on the time series.
* **Explanation:** You can pass a Pandas DataFrame with columns `holiday`, `ds` (dates), and optionally `lower_window`/`upper_window` for the impact range.
* **Possible Values:** Pandas DataFrame or `None`.

***

#### **14. `mcmc_samples`**

* **Significance:** Specifies the number of MCMC samples for estimating uncertainty intervals.
* **Explanation:** By default, Prophet uses MAP estimation. Set `mcmc_samples` > 0 to perform MCMC sampling for better uncertainty estimates.
* **Possible Values:** Integer (default: `0`).

***

#### **15. `cap`**

* **Significance:** Used with logistic growth to specify the maximum value the series can reach.
* **Explanation:** Should be included in the input dataset when using `"logistic"` growth.
* **Possible Values:** Numeric (greater than 0).

***

#### **16. `floor`**

* **Significance:** Used with logistic growth to specify the minimum value the series can reach.
* **Explanation:** Should be included in the input dataset when using `"logistic"` growth.
* **Possible Values:** Numeric (default: `0`).

***

#### **17. `add_seasonality`**

* **Significance:** Allows adding custom seasonal components.
* **Explanation:** Users can define custom seasonalities using a Fourier order, period, and prior scale.
* **Possible Values:** `model.add_seasonality(name="custom", period=<float>, fourier_order=<int>, prior_scale=<float>)`.

***

#### **18. `seasonality_scale` (deprecated)**

* **Significance:** Older version of `seasonality_prior_scale`.
* **Explanation:** Kept for backward compatibility.

***

### Holiday Parameter

The `holidays` parameter in **Facebook Prophet** allows you to incorporate the effects of holidays or special events into the model. By using this parameter, you can specify particular dates (or date ranges) that may influence the time series.

Here’s a detailed explanation:

***

The `holidays` parameter is a **Pandas DataFrame** with the following columns:

1. **`holiday` (string)**: The name of the holiday or event (e.g., "Christmas").
2. **`ds` (date)**: The date(s) of the holiday/event.
3. **Optional: `lower_window` (integer)**:
   * Specifies the number of days **before** the holiday date (`ds`) where the effect begins.
   * A **negative integer**.
4. **Optional: `upper_window` (integer)**:
   * Specifies the number of days **after** the holiday date (`ds`) where the effect continues.
   * A **positive integer**.

***

#### **Default Behavior Without `lower_window` or `upper_window`**

If `lower_window` and `upper_window` are not provided:

* Prophet assumes the holiday effect is confined to the specified date (`ds`) only.

***

#### **How `lower_window` and `upper_window` Work**

* These parameters define a **window of influence** for a holiday.
* **Example:**
  * Suppose "Christmas" (`ds` = December 25th) affects sales starting **2 days before** and lasting **3 days after**.
  * You can set:
    * `lower_window = -2`
    * `upper_window = 3`
  * The effect will apply from December 23rd to December 28th.

***

#### **Significance**

1. **Capturing Pre-Holiday Effects:**
   * Some holidays (like Black Friday) may have an impact on sales or activity before the actual date.
   * `lower_window` accounts for this pre-holiday buildup.
2. **Capturing Post-Holiday Effects:**
   * Holidays may have a lingering effect (e.g., New Year's Day celebrations affecting January 2nd).
   * `upper_window` models these residual effects.

***

#### **Example DataFrame**

Here’s an example of a `holidays` DataFrame for Prophet:

```python
import pandas as pd

holidays = pd.DataFrame({
    'holiday': ['Christmas', 'Christmas', 'Black Friday', 'Black Friday'],
    'ds': ['2024-12-25', '2024-12-25', '2024-11-29', '2024-11-29'],
    'lower_window': [-2, -2, -1, -1],
    'upper_window': [3, 3, 1, 1]
})

print(holidays)
```

**Output:**

```yaml
     holiday          ds  lower_window  upper_window
0  Christmas  2024-12-25            -2             3
1  Christmas  2024-12-25            -2             3
2 Black Friday 2024-11-29           -1             1
3 Black Friday 2024-11-29           -1             1
```

***

#### **Adding the Holidays to Prophet**

You can incorporate the holidays into Prophet like this:

```python
from prophet import Prophet

# Define holidays
holidays = pd.DataFrame({
    'holiday': ['Christmas', 'Black Friday'],
    'ds': ['2024-12-25', '2024-11-29'],
    'lower_window': [-2, -1],
    'upper_window': [3, 1]
})

# Initialize Prophet model
model = Prophet(holidays=holidays)

# Fit the model
model.fit(data)  # where `data` is your time series DataFrame
```

***

#### **Interpreting Results**

* Prophet will estimate the **effect size** of each holiday using a parameter (additive or multiplicative, depending on your model).
* The output will include a **holiday effect plot**, showing the impact of each holiday over time.

***

#### **When to Use `lower_window` and `upper_window`**

* **Use Cases for `lower_window`:**
  * Sales/marketing campaigns leading up to an event.
  * Preparations before holidays like Thanksgiving or Christmas.
* **Use Cases for `upper_window`:**
  * Post-event effects (e.g., New Year’s sales or returns).
  * Lingering effects from long holidays (e.g., holiday travel or festivals).

***

#### **Why Use Strings as Holiday Names?**

The **string names for holidays** are important because they allow Prophet to:

1.  **Group Similar Holiday Effects Together:**

    * If multiple dates are tagged with the same holiday name (e.g., "Christmas"), Prophet assumes these dates have a **shared effect** and estimates a single set of parameters for them.
    * This helps in modeling consistent holiday effects across years or instances of the same holiday.

    **Example:**

    * Suppose you have Christmas in 2023, 2024, and 2025. If they all share the name `"Christmas"`, Prophet will learn a common effect size for Christmas, making the model more robust.
2.  **Interpretability of Effects:**

    * The holiday names help during interpretation and plotting. Prophet generates a plot of **holiday effects**, showing the impact of each holiday string name on the predictions.

    **Example Output Plot:**

    * If `"Christmas"` and `"Black Friday"` are used as holiday names, the output will display:
      * `"Christmas"` effect over the `lower_window` and `upper_window`.
      * `"Black Friday"` effect over its window.
3. **Multiple Holiday Types:**
   * String names allow you to model and compare the effects of **different types of holidays** (e.g., `"Christmas"`, `"Easter"`, `"Black Friday"`) separately.
   * If all holidays were unnamed or identical, the model wouldn’t differentiate between their impacts.
4. **Ease of Debugging and Analysis:**
   * Naming holidays allows you to trace back effects to specific events when evaluating the model.

***

### Future Holidays

Include **future holidays** in the `holidays` DataFrame if you want Prophet to account for their effects when forecasting. Prophet uses the holiday information provided in the `holidays` parameter to adjust forecasts based on the expected impact of holidays.

***

#### **Why Future Holidays Are Needed**

1. **Holidays Influence Forecasts:**
   * If you don’t specify future holidays, Prophet won’t account for their potential effects, which may lead to inaccurate predictions for those periods.
   * For example, if `"Christmas"` significantly increases sales, failing to include future Christmas dates will miss this seasonal spike in your forecast.
2. **Prophet Expects a Fixed List of Holidays:**
   * Prophet doesn’t automatically infer future holidays. The user must explicitly provide holiday dates to ensure proper modeling.

***

#### **Steps to Include Future Holidays**

**1. Generate Future Holiday Dates**

If your holidays are regular (e.g., `"Christmas"` or `"New Year"`), you can manually add them for future years. For complex or regional holidays, use a library like `holidays` or `workalendar` to programmatically generate holiday dates.

Example using the `holidays` library for the US:

```python
import holidays
import pandas as pd

# Generate US holidays for past and future years
us_holidays = holidays.US(years=[2023, 2024, 2025])

# Create a holidays DataFrame for Prophet
holidays_df = pd.DataFrame({
    'holiday': [name for date, name in us_holidays.items()],
    'ds': [str(date) for date, name in us_holidays.items()],
    'lower_window': 0,
    'upper_window': 1
})

print(holidays_df.head())
```

**Output:**

```python
holiday            ds              lower_window  upper_window
0 New Year's Day   2023-01-01             0             1
1 Martin Luther    2023-01-16             0             1
2 Valentine's Day  2023-02-14             0             1
```

***

**2. Combine with Historical Data**

If you already have holiday data for past years in your `holidays` DataFrame, append future holiday dates to it.

```python
# Append future holidays to existing DataFrame
all_holidays_df = pd.concat([historical_holidays_df, holidays_df])
```

***

**3. Pass the Holidays DataFrame to Prophet**

Include the extended `holidays` DataFrame when initializing the Prophet model.

```python
from prophet import Prophet

model = Prophet(holidays=all_holidays_df)
model.fit(df)

# Forecast into the future
future = model.make_future_dataframe(periods=365)
forecast = model.predict(future)

# Plot the forecast
model.plot(forecast)
```

***

#### **What Happens If You Don’t Specify Future Holidays?**

* Prophet will not apply any holiday effects to the forecasted periods.
* This may lead to under- or over-prediction around dates that typically experience holiday effects.

***

#### **Key Considerations**

* **Dynamic Holidays:** For holidays with varying dates (e.g., Easter), use a library to programmatically calculate future dates.
* **Regional Holidays:** Include holidays specific to your region or domain (e.g., local festivals, company-specific holidays).
