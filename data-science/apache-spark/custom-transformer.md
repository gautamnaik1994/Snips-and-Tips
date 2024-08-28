# Custom Transformer

### **Custom Tranformer**

```python
from pyspark.ml import Transformer
from pyspark.ml.param.shared import HasInputCol, HasOutputCol, Param, Params
from pyspark.ml.util import DefaultParamsReadable, DefaultParamsWritable
from pyspark.sql import DataFrame
from pyspark.sql import functions as sf

class CustomTransformer(Transformer, HasInputCol, HasOutputCol, DefaultParamsReadable, DefaultParamsWritable):
    """
    A custom transformer for PySpark pipelines.

    Parameters:
    -----------
    inputCol : str
        The name of the input column for the transformer.

    outputCol : str
        The name of the output column for the transformer.
    """

    # Optionally, you can define additional parameters here
    my_param: Param = Param(Params._dummy(), "my_param", "A custom parameter for the transformer")

    def __init__(self, inputCol: str = None, outputCol: str = None, my_param_value: str = "default_value"):
        super(CustomTransformer, self).__init__()
        # Set input and output columns using PySpark's shared param functionality
        self._setDefault(my_param="default_value")
        self._set(my_param=my_param_value)
        
        # Set input and output columns if provided
        if inputCol is not None:
            self.setInputCol(inputCol)
        if outputCol is not None:
            self.setOutputCol(outputCol)

    def _transform(self, df: DataFrame) -> DataFrame:
        """
        The core logic for the transformation.

        Parameters:
        -----------
        df : DataFrame
            The input DataFrame to be transformed.

        Returns:
        --------
        DataFrame
            The transformed DataFrame with an additional column.
        """
        input_col = self.getInputCol()
        output_col = self.getOutputCol()
        my_param_value = self.getOrDefault(self.my_param)
        
        # Implement your transformation logic here
        # Example: Adding a constant value to the input column
        transformed_df = df.withColumn(output_col, sf.col(input_col) + sf.lit(my_param_value))

        return transformed_df

    # Optional: Define a getter for custom parameters
    def getMyParam(self) -> str:
        """
        Returns the value of the custom parameter `my_param`.
        """
        return self.getOrDefault(self.my_param)

    # Optional: Define a setter for custom parameters
    def setMyParam(self, value: str):
        """
        Sets the value of the custom parameter `my_param`.

        Parameters:
        -----------
        value : str
            The value to set for `my_param`.
        """
        self._set(my_param=value)
        return self

# Usage in a Pipeline
from pyspark.ml import Pipeline

# Create your custom transformer
custom_transformer = CustomTransformer(inputCol="input_column_name", outputCol="output_column_name", my_param_value="some_value")

# Create a pipeline
pipeline = Pipeline(stages=[
    custom_transformer, 
    # add other transformers like VectorAssembler, OneHotEncoder, etc.
])

# Fit the pipeline
model = pipeline.fit(input_df)

# Transform the data
transformed_df = model.transform(input_df)
```

### Class Weight Transformer

```python
from pyspark.ml import Transformer
from pyspark.ml.util import DefaultParamsReadable, DefaultParamsWritable
from pyspark.sql import functions as sf
from pyspark.sql import DataFrame

class ClassWeightTransformer(Transformer, DefaultParamsReadable, DefaultParamsWritable):
    def __init__(self, target_col, output_col="classWeightCol"):
        super(ClassWeightTransformer, self).__init__()
        self.target_col = target_col
        self.output_col = output_col

    def _transform(self, df: DataFrame) -> DataFrame:
        # Calculate class weights
        class_weights = df.groupBy(self.target_col).count().collect()
        total_count = df.count()
        weights = {row[self.target_col]: total_count / row['count'] for row in class_weights}
        
        # Apply class weights
        df = df.withColumn(
            self.output_col,
            sf.when(df[self.target_col] == 1, weights[1]).otherwise(weights[0])
        )
        return df

# Usage in a Pipeline
from pyspark.ml import Pipeline

# Create your custom transformer
class_weight_transformer = ClassWeightTransformer(target_col="Churned")

# Create a pipeline
pipeline = Pipeline(stages=[class_weight_transformer, 
                            # add other transformers like VectorAssembler, OneHotEncoder, etc.
                            ])

# Fit the pipeline
model = pipeline.fit(model_df)

# Transform the data
transformed_df = model.transform(model_df)
```
