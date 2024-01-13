# Categorical Encoding

## Target Encoding

- **High-cardinality features**: A feature with a large number of categories can be troublesome to encode: a one-hot encoding would generate too many features and alternatives, like a label encoding, might not be appropriate for that feature. A target encoding derives numbers for the categories using the feature's most important property: its relationship with the target.
- **Domain-motivated features**: From prior experience, you might suspect that a categorical feature should be important even if it scored poorly with a feature metric. A target encoding can help reveal a feature's true informativeness.

```python
from sklearn.preprocessing import TargetEncoder
```

## Label Encoding

Can be used for features with 2 categories.

## One Hot Encoder

Good when there are more than 2 categories but will result in a sparse matrix when there are a large number of categories
