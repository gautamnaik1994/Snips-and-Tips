# Statistical Tests

A telecom company had taken a **survey** of smartphone owners in a certain town 5 years back and found **73%** of the population own a **smartphone**, and have been since using this data to make their business decisions.

Now a new marketing manager has joined, and he believes this value is not valid anymore. Thus he conducts a survey of **500 people** and finds that **420** of them responded with affirmation as to owning a smartphone. Which **statistical test** would you use to **compare** these two survey data?

1. **Test of proportions, z-test:**
   * Applicability: This is the correct option. The z-test for proportions is suitable when comparing the proportions of two independent samples. In this case, you are comparing the proportion of smartphone owners in the town based on the data from 5 years ago (73%) and the recent survey (where 420 out of 500 respondents own a smartphone).
   * Reasoning: The z-test for proportions allows you to assess whether the observed difference in proportions is statistically significant. It is appropriate when you have a large sample size (which is often the case in surveys) and when the conditions for using a z-test are met.
2. **Test of independence, chi-square test:**
   * Applicability: The chi-square test of independence is used when you have categorical data and want to test if there is a significant association between two variables.
   * Reasoning: While the chi-square test is useful in certain scenarios, it is not the best choice for comparing proportions between two independent samples. It is more suitable for analyzing contingency tables with categorical data.
3. **Test of means, t-test:**
   * Applicability: The t-test is used when comparing means of two independent samples, not proportions.
   * Reasoning: Since you are interested in comparing the proportion of smartphone owners, the t-test is not the appropriate choice. The t-test is used for continuous data (such as comparing the means of two groups) and is not suitable for proportions.

## Kolmogorov–Smirnov Test (KS Test)

This test checks if two sets of data have the same type of distribution

## Kruskal Wallis Test

This test does not assume that the data are normal, it does assume that the different groups have the same distribution, and groups with different standard deviations have different distributions

## ANOVA

## Levene

This test checks if data arrays passed to it has equal variance

## Shapiro-Wilk Test

This test checks whether data has normal distribution

## CHI Square

This is used to check if 2 categorical variable are related

* Null Hypothesis : 2 groups are independent
* Alternate Hypothesis: 2 groups are dependent

This means that the expected value table received from following calculations have independent values

```python

from scipy.stats import chi2_contingency

table = [[10, 20, 30],[6,  9,  17]]
stat, p, dof, expected = chi2_contingency(table)

# interpret test-statistic
prob = 0.95
critical = chi2.ppf(prob, dof)
if abs(stat) >= critical:
 print('Dependent (reject H0)')
else:
 print('Independent (fail to reject H0)')
# p-value < alpha, then reject the null hypothesis
```

## Different combinations and corresponding test

| Scenario                                           | Statistical Test             | Example                                                                                                         |
| -------------------------------------------------- | ---------------------------- | --------------------------------------------------------------------------------------------------------------- |
| Numerical vs. Numerical                            | Correlation                  | Examining the relationship between hours of study and exam scores.                                              |
| Numerical vs. Categorical (Binary)                 | Logistic Regression          | Predicting the likelihood of a student passing an exam based on the number of hours of study.                   |
| Numerical vs. Categorical (More than 2 Categories) | ANOVA                        | Comparing the average test scores of students who studied for different durations across multiple study groups. |
| Numerical vs. Categorical (Repeated Measures)      | Repeated Measures ANOVA      | Investigating changes in blood pressure levels across different time points with different treatment groups.    |
| Numerical vs. Categorical (Longitudinal Data)      | Mixed Effects Models         | Analyzing repeated measurements of cholesterol levels over time for patients receiving different treatments.    |
| Numerical vs. Categorical (Survival Analysis)      | Kaplan-Meier, Cox Regression | Assessing the time until relapse for patients with different types of cancer treatments.                        |
| Categorical vs. Categorical                        | Chi-square Test              | Examining the association between gender and smoking status.                                                    |
| Categorical vs. Categorical (Association)          | Cramér's V                   | Measuring the strength of association between political affiliation and voting behavior.                        |
| Numerical vs. Categorical (Ordinal)                | Kruskal-Wallis Test          | Comparing the median satisfaction scores for customers across different levels of service quality.              |

1 sample z test for mean\
1 sample t test for mean

1 sample z test for proportion 1 sample t test for proportion

2 sample independent test for mean 2 sample independent test for proportion

Paired test

### Cheat sheet for different test

{% embed url="https://machinelearningmastery.com/statistical-hypothesis-tests-in-python-cheat-sheet/" %}

### Hopkins test to check clustering tendency

```python
import numpy as np
import pandas as pd
from sklearn.neighbors import NearestNeighbors

def hopkins_statistic(X: pd.DataFrame, sample_size: float = 0.1) -> float:
    """
    Calculate the Hopkins statistic for a given dataset to measure its clustering tendency.

    Parameters:
    X (pd.DataFrame): The input dataset.
    sample_size (float): The proportion of the dataset to be used as a sample. Default is 0.1.

    Returns:
    float: The Hopkins statistic, a value between 0 and 1.
           - Values close to 0 indicate that the dataset is highly clustered.
           - Values close to 1 indicate that the dataset is uniformly distributed.
           - Values around 0.5 indicate random distribution.
    """
    # Number of data points in the dataset
    n = X.shape[0] 
    
    # Number of samples to draw
    m = int(sample_size * n) 

    # Randomly select m data points from the dataset
    random_indices = np.random.choice(np.arange(n), size=m, replace=False)
    X_sample = X.iloc[random_indices]

    # Determine the min and max values for each feature
    X_min = np.min(X, axis=0)
    X_max = np.max(X, axis=0)
    
    # Generate m uniformly random data points within the min and max range of the dataset
    X_uniform_random = np.random.uniform(X_min, X_max, (m, X.shape[1]))

    # Fit the Nearest Neighbors model on the dataset
    nbrs = NearestNeighbors(n_neighbors=1).fit(X)

    # Calculate the sum of distances from each sample point to its nearest neighbor in the dataset
    u_distances, _ = nbrs.kneighbors(X_sample)
    u_distances = u_distances.sum()
    
    # Calculate the sum of distances from each uniformly random point to its nearest neighbor in the dataset
    w_distances, _ = nbrs.kneighbors(X_uniform_random)
    w_distances = w_distances.sum()

    # Calculate the Hopkins statistic
    hopkins_stat = w_distances / (u_distances + w_distances)

    return hopkins_stat
```

If output is close to 1, that means data does not have clusters. If 0 then data has clusters
