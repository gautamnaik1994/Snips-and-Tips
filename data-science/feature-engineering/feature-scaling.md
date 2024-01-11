# Feature Scaling

### Scaling Data

**Standard Scaler** standardizes data by subtracting the mean and dividing by the standard deviation. This is generally preferred for machine learning models as it:

* Makes all features have zero mean and unit variance, which can improve model performance.
* Ensures all features contribute equally to the model, regardless of their original units or scales.

**Min-Max Scaler** scales the data to a specific range, typically between 0 and 1. This may be useful in certain cases, but it can be problematic for machine learning models because:

* It removes information about the spread of the data (variance), which can be important for certain models.
* It can amplify the effect of outliers.
