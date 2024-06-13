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
