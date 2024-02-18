# Z - Test

## Z Test for Testing Means

### One Sample Z Test

### Two Sample Z Test

```python
import numpy as np

def TwoSampZTest(samp_mean_1, samp_mean_2, samp_std_1, samp_std_2, n1, n2):
    denominator = np.sqrt((pow(samp_std_1, 2) / n1) + (pow(samp_std_2, 2) / n2))
    z_score = (samp_mean_1 - samp_mean_2) / denominator
    return z_score
```

When series data is given use following code

```python
from statsmodels.stats import weightstats as stests
z_score, p_value = stests.ztest(list_1, list_2, alternative ='smaller')
```

## Proportion test

### 2 sample z proportion test

The Quidditch teams at Hogwarts conducted tryouts for two positions: **Chasers** and **Seekers**.

In Group Chasers, out of 90 students who tried out, 57 were selected. In Group Seekers, out of 120 students who tried out, 98 were selected.

Is there a significant difference in the proportion of students selected for Chasers and Seekers positions?

Conduct a test at 90% confidence level.

Using Formula

```python
n1 = 90
x1 = 57
n2 = 120
x2 = 98

# Calculate sample proportions
p1 = x1 / n1
p2 = x2 / n2

# Calculate pooled sample proportion
pooled_p = (x1 + x2) / (n1 + n2)

# Calculate standard error
se = np.sqrt(pooled_p * (1 - pooled_p) * (1/n1 + 1/n2))

# Calculate Z-test statistic
z_statistic = (p1 - p2) / se

# Two-tailed p-value
p_value = 2 * (1 - norm.cdf(abs(z_statistic)))

# Print the results
print("Z-statistic:", z_statistic)
print("p-value:", p_value)

# Check if the p-value is less than the significance level
alpha = 0.10
if p_value < alpha:
    print("Reject the null hypothesis: There is a significant difference in proportions.")
else:
    print("Fail to reject the null hypothesis: No significant difference in proportions.")

"""
Z-statistic: -2.990306921349541
p-value: 0.002786972588957992
Reject the null hypothesis: There is a significant difference in proportions.
"""
```

Using Scipy package

```python
from statsmodels.stats.proportion import proportions_ztest

successes = [57, 98]
trial = [90, 120]
z_statistic, p_value = proportions_ztest(successes, trial, alternative='two-sided')
z_statistic, p_value # (-2.990306921349541, 0.002786972588958094)
```

As a product manager, you want to evaluate the user satisfaction for two different seasons of Naruto Shippuden (Season 1 and Season 2).

You collected feedback from 250 viewers who watched Season 1 of Naruto Shippuden, and 120 expressed satisfaction. Similarly, for Season 2, you gathered data from 300 viewers, and 150 of them expressed satisfaction.

Conduct an appropriate test at a **95% confidence interval** to determine if there's a higher user satisfaction for Season 2 than for Season 1.

```python
import numpy as np
# Data
n1 = 250
x1 = 120
n2 = 300
x2 = 150

# Calculate sample proportions
p1 = x1 / n1
p2 = x2 / n2

# Calculate pooled sample proportion
pooled_p = (x1 + x2) / (n1 + n2)

# Calculate standard error
se = np.sqrt(pooled_p * (1 - pooled_p) * (1/n1 + 1/n2))

# Calculate Z-test statistic
z_statistic = (p2 - p1) / se

# One-tailed p-value (since the alternative is p2 < p1)
p_value = 1-norm.cdf(z_statistic)

# Print the results
print("Z-statistic:", z_statistic)
print("p-value:", p_value)

# Check if the p-value is less than the significance level
alpha = 0.05
if p_value < alpha:
    print("Reject the null hypothesis: There is higher user satisfaction for Season 2.")
else:
    print("Fail to reject the null hypothesis: No evidence of higher user satisfaction for Season 2.")
"""
Z-statistic: 0.46717659215115714
p-value: 0.32018676972652416
Fail to reject the null hypothesis: No evidence of higher user satisfaction for Season 2.
"""
```

```python
successes = np.array([120, 150])
trials = np.array([250, 300])

# Perform two-sample Z-test for proportions
z_statistic, p_value = proportions_ztest(successes, trials, alternative='smaller')  # 'smaller' for p2 < p1

# Print the results
print("Z-statistic:", z_statistic)
print("p-value:", p_value)

# Check if the p-value is less than the significance level
alpha = 0.05
if p_value < alpha:
    print("Reject the null hypothesis: There is higher user satisfaction for Season 2.")
else:
    print("Fail to reject the null hypothesis: No evidence of higher user satisfaction for Season 2.")

"""
Z-statistic: -0.46717659215115714
p-value: 0.3201867697265242
Fail to reject the null hypothesis: No evidence of higher user satisfaction for Season 2.
"""
```
