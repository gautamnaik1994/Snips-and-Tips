# Pandas Apply Function

Consider the following data frame

<table><thead><tr><th width="106">total_bill</th><th>tip</th><th width="88">sex</th><th>smoker</th><th>day</th><th>time</th><th>size</th><th data-hidden></th></tr></thead><tbody><tr><td>16.99</td><td>1.01</td><td>Female</td><td>No</td><td>Sun</td><td>Dinner</td><td>2</td><td>0</td></tr><tr><td>10.34</td><td>1.66</td><td>Male</td><td>No</td><td>Sun</td><td>Dinner</td><td>3</td><td>1</td></tr><tr><td>21.01</td><td>3.50</td><td>Male</td><td>No</td><td>Sun</td><td>Dinner</td><td>3</td><td>2</td></tr><tr><td>23.68</td><td>3.31</td><td>Male</td><td>No</td><td>Sun</td><td>Dinner</td><td>2</td><td>3</td></tr><tr><td>24.59</td><td>3.61</td><td>Female</td><td>No</td><td>Sun</td><td>Dinner</td><td>4</td><td>4</td></tr></tbody></table>

When we run the following code, all values of **each column** will be printed

```python
df.apply(lambda x:print(x), axis=0)
```

```
    0      16.99
    1      10.34
    2      21.01
    3      23.68
    4      24.59
          ...  
    Name: total_bill, Length: 244, dtype: float64
    0      1.01
    1      1.66
    2      3.50
    3      3.31
    4      3.61
          ... 
    Name: tip, Length: 244, dtype: float64
    0      Female
    1        Male
    2        Male
    3        Male
    4      Female
            ...  
    Name: sex, Length: 244, dtype: category
    Categories (2, object): ['Male', 'Female']
    0       No
    1       No
    2       No
    3       No
    4       No
          ... 
    Name: smoker, Length: 244, dtype: category
    Categories (2, object): ['Yes', 'No']
    0       Sun
    1       Sun
    2       Sun
    3       Sun
    4       Sun
          ... 
    Name: day, Length: 244, dtype: category
    Categories (4, object): ['Thur', 'Fri', 'Sat', 'Sun']
    0      Dinner
    1      Dinner
    2      Dinner
    3      Dinner
    4      Dinner
            ...  
    Name: time, Length: 244, dtype: category
    Categories (2, object): ['Lunch', 'Dinner']
    0      2
    1      3
    2      3
    3      2
    4      4
          ..
    Name: size, Length: 244, dtype: int64

```

When we run the following code, all values of **each row** will be printed

```python
df.apply(lambda x:print(x), axis=1)
```

```
total_bill     16.99
tip             1.01
sex           Female
smoker            No
day              Sun
time          Dinner
size               2
Name: 0, dtype: object
total_bill     10.34
tip             1.66
sex             Male
smoker            No
day              Sun
time          Dinner
size               3
Name: 1, dtype: object
...
```

Following are different ways to use `apply` function to create new columns in a data frame

```python
def multiply(d):
    # print(d.shape) # (7,)
    d["res"]=d["total_bill"] * d["size"]
    d["res2"]=d["total_bill"] * d["size"]
    return d

def sum(d):
    return d["total_bill"] + d["size"]

df = df.apply(multiply, axis=1)
df["sum"]=df.apply(sum, axis=1)
```
