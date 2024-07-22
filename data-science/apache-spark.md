# Apache Spark

## Common Snippets

Create Session

```python
from pyspark.sql import SparkSession
spark = SparkSession.builder.appName("SparkSQL").getOrCreate()
```

Read CSV

```python
spark.read.csv("./test.csv", header=True, inferSchema=True)
```

Target Encoding

```python
# spark inputs
spark_data = [Row(label=0, cate1='abc'),
              Row(label=1, cate1='abc'),
              Row(label=0, cate1='def'),
              Row(label=0, cate1='def'),
              Row(label=1, cate1='ghi')]

df = spark.createDataFrame(spark_data)

df.show()
>>>
+-----+-----+
|cate1|label|
+-----+-----+
|  abc|    0|
|  abc|    1|
|  def|    0|
|  def|    0|
|  ghi|    1|
+-----+-----+


# function
def target_mean_encoding(df, col, target):
    """
    :param df: pyspark.sql.dataframe
        dataframe to apply target mean encoding
    :param col: str list
        list of columns to apply target encoding
    :param target: str
        target column
    :return:
        dataframe with target encoded columns
    """
    target_encoded_columns_list = []
    for c in col:
        means = df.groupby(F.col(c)).agg(F.mean(target).alias(f"{c}_mean_encoding"))
        dict_ = means.toPandas().to_dict()
        target_encoded_columns = [F.when(F.col(c) == v, encoder)
                                  for v, encoder in zip(dict_[c].values(),
                                                        dict_[f"{c}_mean_encoding"].values())]
        target_encoded_columns_list.append(F.coalesce(*target_encoded_columns).alias(f"{c}_mean_encoding"))
    return df.select(target, *target_encoded_columns_list)


# function apply on spark inputs
df_target_encoded = target_mean_encoding(df, col=['cate1'], target='label')

df_target_encoded.show()
>>> 
+-----+-------------------+
|label|cate1_mean_encoding|
+-----+-------------------+
|    0|                0.5|
|    1|                0.5|
|    0|                0.0|
|    0|                0.0|
|    1|                1.0|
+-----+-------------------+


# if you want to keep the same column name after target mean encoder
df_target_encoded.withColumnRenamed('cate1_mean_encoding', 'cate1')

df_target_encoded.show()
>>>
+-----+-----+
|label|cate1|
+-----+-----+
|    0|  0.5|
|    1|  0.5|
|    0|  0.0|
|    0|  0.0|
|    1|  1.0|
+-----+-----+
```
