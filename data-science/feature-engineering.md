# Feature Engineering

### **Target Encoding**

* **High-cardinality features**: A feature with a large number of categories can be troublesome to encode: a one-hot encoding would generate too many features and alternatives, like a label encoding, might not be appropriate for that feature. A target encoding derives numbers for the categories using the feature's most important property: its relationship with the target.
* **Domain-motivated features**: From prior experience, you might suspect that a categorical feature should be important even if it scored poorly with a feature metric. A target encoding can help reveal a feature's true informativeness.

```python
from sklearn.preprocessing import TargetEncoder
```

### Label Encoding

Can be used for features with 2 categories

### One Hot Encoder

Good when there are more than 2 categories but will result in a sparse matrix when there are a large number of categories



### Scaling Data

**Standard Scaler** standardizes data by subtracting the mean and dividing by the standard deviation. This is generally preferred for machine learning models as it:

* Makes all features have zero mean and unit variance, which can improve model performance.
* Ensures all features contribute equally to the model, regardless of their original units or scales.

**Min-Max Scaler** scales the data to a specific range, typically between 0 and 1. This may be useful in certain cases, but it can be problematic for machine learning models because:

* It removes information about the spread of the data (variance), which can be important for certain models.
* It can amplify the effect of outliers.

