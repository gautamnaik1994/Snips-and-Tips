# Outlier Detection

**Consider the student exam scores scenario which contains some outliers**

**IQR method:**

* This method identifies outliers based on the quartiles of the data.
* It is more resistant to outliers than the mean-based methods like Z-score, making it **better suited for skewed data** like exam scores where a few high scores can significantly impact the mean.

**Z-score method:**

* This method identifies outliers based on their standard deviation from the mean.
* However, it can be influenced by outliers itself, leading to inaccurate outlier detection when the data is skewed.
* In this scenario, the presence of a few high scores can inflate the standard deviation, potentially masking other outliers or misidentifying valid data points as outliers.
