# Apache Spark

## Common Snippets

Initialize Spark Session&#x20;

```python
import os
import sys

SPARK_HOME = "/opt/homebrew/Cellar/apache-spark/3.5.3/libexec"
JAVA_HOME = '/opt/homebrew/opt/openjdk@17'

os.environ['SPARK_HOME'] = SPARK_HOME
os.environ['JAVA_HOME'] = JAVA_HOME
sys.path.extend([
    f"{SPARK_HOME}/python/lib/py4j-0.10.9.7-src.zip",
    f"{SPARK_HOME}/python/lib/pyspark.zip",
])

from pyspark.sql import SparkSession
spark = SparkSession.builder\
    .master('local[*]') \
    .getOrCreate()

```

Using findspark

```python
import findspark
import os
from pyspark.sql import SparkSession

# Set JAVA_HOME if needed, findspark will handle SPARK_HOME
os.environ['JAVA_HOME'] = '/opt/homebrew/opt/openjdk@17'

# Initialize findspark
findspark.init("/opt/homebrew/Cellar/apache-spark/3.5.3/libexec")

# Create a Spark session
spark = SparkSession.builder \
    .master("local[*]") \
    .getOrCreate()

```

Create Session

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder \
    .appName("SessionName") \
    .config("spark.sql.debug.maxToStringFields", 1000) \
    .config("spark.sql.execution.arrow.pyspark.enabled", "true") \
    .config("spark.sql.shuffle.partitions", 1) \
    .config("spark.network.timeout", "120s") \
    .config("spark.executor.heartbeatInterval", "10s") \
    .getOrCreate()
```

Read CSV

```python
df = spark.read \
    .option("header", "true") \
    .option("inferSchema", "true") \
    .option("multiLine", "true") \
    .option("escape", "\"") \
    .csv("./test.csv")
```

Drop null values

```python
df.na.drop(how="all") #if all columns have null values
df.na.drop(how="any") #if any one columns have null value
df.na.drop(how="any", thresh=2) #if atleast there are 2 null values
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

Window Functions and Coalesce

```python
window_spec = Window.partitionBy("Driver_ID")

df = df.withColumn(
    "Age",
    sf.coalesce(sf.col("Age"), sf.first("Age", True).over(window_spec))
)
```

Conditional column values

```python
default_date = "2020-12-31"

df = df.withColumn(
    "Tenure",
      sf.abs(
        sf.datediff(
            sf.when(sf.col("Last_Working_Date").isNull(), sf.lit(default_date)).otherwise(sf.col("Last_Working_Date")),
            sf.col("Date_Of_Joining")
        )
    )
)
```
