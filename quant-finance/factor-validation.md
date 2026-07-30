---
description: >-
  Check whether RSI or anything similar has predictive power by building zero
  investment porfolio
---

# Factor Validation

In quantitative finance, testing whether an technical indicator like RSI (Relative Strength Index) carries predictive power is done by constructing a Zero-Investment (Long-Short) Portfolio or calculating the Information Coefficient (IC) between the factor value at time $$ $t$ $$ and future stock returns at time $$ $t+1$ $$.

Here is how the quantitative methodology works in practice:

1. Rank & Segment: At the end of each period (e.g., monthly/daily), calculate the 14-day RSI for all stocks in the universe (Nifty 50).
2. Form Portfolios: \* Mean-Reversion Thesis: Go Long oversold stocks (lowest RSI) and Short overbought stocks (highest RSI).
   * Momentum Thesis: Go Long high-RSI stocks and Short low-RSI stocks.
3. Measure Performance: The return of the Long-Short portfolio is the Factor Premium. If the average premium over time is significantly positive (t-statistic $$ $> 2.0$ $$), the factor has predictive power.

#### Python Code Implementation (Pandas + `yfinance`)

Below is a complete, working script that downloads data for Nifty 50 stocks, computes 14-day RSI, forms a monthly Long-Short (Zero-Investment) Factor Portfolio, and evaluates its statistical significance.

Python

```python
import numpy as np
import pandas as pd
import yfinance as yf

# 1. Define Nifty 50 Tickers (Yahoo Finance format: '.NS')
nifty50_tickers = [
    "RELIANCE.NS",
    "TCS.NS",
    "HDFCBANK.NS",
    "ICICIBANK.NS",
    "INFY.NS",
    "BHARTIARTL.NS",
    "ITC.NS",
    "SBIN.NS",
    "LTIM.NS",
    "LT.NS",
    "HINDUNILVR.NS",
    "AXISBANK.NS",
    "KOTAKBANK.NS",
    "M&M.NS",
    "TATAMOTORS.NS",
    "NTPC.NS",
    "MARUTI.NS",
    "POWERGRID.NS",
    "SUNPHARMA.NS",
    "TITAN.NS",
    "ULTRACEMCO.NS",
    "BAJFINANCE.NS",
    "ADANIENT.NS",
    "TATASTEEL.NS",
    "JSWSTEEL.NS",
    "GRASIM.NS",
    "ASIANPAINT.NS",
    "COALINDIA.NS",
    "NESTLEIND.NS",
    "TECHM.NS",
    "HCLTECH.NS",
    "HDFCLIFE.NS",
    "WIPRO.NS",
    "SBILIFE.NS",
    "DRREDDY.NS",
    "BAJAJFINSV.NS",
    "TATACONSUM.NS",
    "DIVISLAB.NS",
    "EICHERMOT.NS",
    "CIPLA.NS",
    "BPCL.NS",
    "HEROMOTOCO.NS",
    "APOLLOHOSP.NS",
    "INDUSINDBK.NS",
    "BEL.NS",
    "TRENT.NS",
    "BAJAJ-AUTO.NS",
    "SHRIRAMFIN.NS",
    "ONGC.NS",
    "ADANIPORTS.NS",
]


# 2. Function to Calculate 14-Day RSI
def compute_rsi(prices, window=14):
    delta = prices.diff()
    gain = (delta.where(delta > 0, 0)).rolling(window=window).mean()
    loss = (-delta.where(delta < 0, 0)).rolling(window=window).mean()
    rs = gain / loss
    return 100 - (100 / (1 + rs))


# 3. Download Historical Daily Price Data
print("Downloading stock data...")
data = yf.download(nifty50_tickers, start="2018-01-01", end="2024-01-01")[
    "Adj Close"
]

# 4. Calculate RSI for all stocks
rsi_df = data.apply(compute_rsi)

# 5. Resample to Monthly Frequency (End of Month)
# Factor Signal (RSI) measured at Month End
monthly_rsi = rsi_df.resample("ME").last()

# Calculate Forward 1-Month Return for each stock
monthly_prices = data.resample("ME").last()
forward_returns = monthly_prices.pct_change().shift(-1)  # Return of NEXT month

# 6. Backtest Long-Short Zero-Investment Factor Portfolio
long_returns = []
short_returns = []
factor_premiums = []
dates = []

# Loop over each month (excluding last row due to look-ahead shift)
for date in monthly_rsi.index[:-1]:
    rsi_signal = monthly_rsi.loc[date].dropna()
    fwd_ret = forward_returns.loc[date].dropna()

    # Get common stocks present in both signal and return
    common_stocks = rsi_signal.index.intersection(fwd_ret.index)

    if len(common_stocks) < 10:
        continue

    sig = rsi_signal[common_stocks]
    ret = fwd_ret[common_stocks]

    # Mean-Reversion Strategy:
    # Long Quintile 1 (Lowest RSI = Oversold)
    # Short Quintile 5 (Highest RSI = Overbought)
    q1_threshold = sig.quantile(0.20)
    q5_threshold = sig.quantile(0.80)

    long_basket = ret[sig <= q1_threshold]
    short_basket = ret[sig >= q5_threshold]

    long_ret = long_basket.mean()
    short_ret = short_basket.mean()

    # Zero-Investment Factor Return (Long - Short)
    f_rsi = long_ret - short_ret

    dates.append(date)
    long_returns.append(long_ret)
    short_returns.append(short_ret)
    factor_premiums.append(f_rsi)

# 7. Evaluate Predictive Power & Statistical Significance
results = pd.DataFrame(
    {
        "Long_Return": long_returns,
        "Short_Return": short_returns,
        "RSI_Factor_Premium": factor_premiums,
    },
    index=dates,
)

avg_premium = results["RSI_Factor_Premium"].mean()
std_premium = results["RSI_Factor_Premium"].std()
t_stat = (avg_premium / std_premium) * np.sqrt(len(results))
annualized_return = avg_premium * 12
annualized_vol = std_premium * np.sqrt(12)
sharpe_ratio = (
    annualized_return / annualized_vol if annualized_vol != 0 else np.nan
)

print("\n--- RSI FACTOR VALIDATION RESULTS ---")
print(
    f"Average Monthly Factor Premium (Long-Short): {avg_premium:.4%} per month"
)
print(f"Annualized Premium: {annualized_return:.2%}")
print(f"t-Statistic: {t_stat:.2f}")
print(f"Sharpe Ratio: {sharpe_ratio:.2f}")

if t_stat > 2.0:
    print(
        "\nConclusion: RSI has STATISTICALLY SIGNIFICANT PREDICTIVE POWER at the 95% confidence level."
    )
elif t_stat < -2.0:
    print(
        "\nConclusion: Reverse-RSI (Momentum) has STATISTICALLY SIGNIFICANT PREDICTIVE POWER."
    )
else:
    print(
        "\nConclusion: RSI does NOT show statistically significant predictive power (t-stat between -2 and +2)."
    )
```

#### Key Takeaways from this Validation Test

1. t-Statistic Rule of Thumb: \* A $$ $t$ $$-statistic $$ $> \vert{}2.0\vert{}$ $$ indicates that the RSI factor premium is statistically different from zero (not random luck).
2.  Information Coefficient (IC): \* You can also measure predictive power using Spearman rank correlation between RSI at $$ $t$ $$ and returns at $$ $t+1$ $$:

    \$$\text{IC}\_t = \text{Corr}\big(\text{Rank}(\text{RSI}\_t), \text{Rank}(\text{Return}\_{t+1})\big)\$$

    * An average $$ $\text{IC} > 0.05$ $$ is considered a good predictive factor in quantitative equity management.
3. Common Pitfalls to Avoid:
   * Look-ahead Bias: Always ensure factor inputs (RSI at month-end) use price data up to month-end, and returns are evaluated for the _subsequent_ period ($$ $t+1$ $$).
   * Transaction Costs & Slippage: A zero-investment portfolio requires rebalancing, which incurs brokerage costs and impact cost, especially in shorting.
