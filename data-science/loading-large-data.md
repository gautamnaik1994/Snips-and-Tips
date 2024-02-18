# Loading large data

#### Use datatable library

```python
import datatable as dt
dt.fread("file").to_pandas()
```

#### Use DuckDB library

```python
duckdb.sql(
    """
        select
         days_till_primary_close,
         days_till_final_close,
         loans_outstanding_balance,
         utilization,
         primary_close_flag, final_close_flag
           from df
        where primary_close_flag = 1 and final_close_flag = 0 limit 100
    """
).pl().sample(10)
```

where df is the dataframe and .pl() converts the dataframe into Polars dataframe

#### Convert float64 to float32

```python
df = df.astype({c: np.float32 for c in df.select_dtypes(include='float64').columns})
```

#### Load pickle file

```python
import pickle
import pandas as pd
import datatable as dt

train_file = '/kaggle/input/jane-street-market-prediction/train.csv'
pickle.dump(dt.fread(train_file).to_pandas(), open('train.csv.pandas.pickle', 'wb'))

# load pickle file

train_pickle_file = '/kaggle/input/pickling/train.csv.pandas.pickle'
train = pickle.load(open(train_pickle_file, 'rb'))
```
