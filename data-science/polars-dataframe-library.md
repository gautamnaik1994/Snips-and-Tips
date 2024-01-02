# Polars Dataframe Library

## Some useful tips about polars dataframe library

### Using SQL queries inside polars

```python
res = pl.SQLContext(frame=df).execute(
"""
with cte as (
  SELECT customer_id, primary_term, final_term from frame where primary_term > final_term limit 100
)
select * from cte order by primary_term desc limit 10
"""
)
res.collect()
```

### Polars basic

[https://github.com/gautamnaik1994/2023-Pycon-Polars](https://github.com/gautamnaik1994/2023-Pycon-Polars)

{% @github-files/github-code-block url="<https://github.com/gautamnaik1994/2023-Pycon-Polars>" %}
