# Pandas

#### Plotting

To plot histogram from dataframe

```python
df[anything].hist(bins =50)
```

If you the plot to be separated by categorical variables. for eg `final_close_flag` has values 0 and 1

```
df.hist(column="record_number", by="final_close_flag", bins=20)
```

Box plot

```python
df.boxplot(column='name')
```

Segregation by features

```python
df.boxplot(column='name', by='feature')
```

To use plotly as backend to plot directly from pandas dataframe

```python
import pandas as pd
import plotly.graph_objects as go
pd.options.plotting.backend = 'plotly'

df["Close"].plot()
```

#### To plot candlestick chart using plotly

```python
fig = go.Figure(data=[go.Candlestick(x=df.index, open=df["Open"],
                high=df["High"], low=df["Low"], close=df["Close"])])
fig.show()
```

#### Links

[https://www.analyticsvidhya.com/blog/2016/01/12-pandas-techniques-python-data-manipulation/](https://www.analyticsvidhya.com/blog/2016/01/12-pandas-techniques-python-data-manipulation/) [https://www.analyticsvidhya.com/blog/2016/01/guide-data-exploration/](https://www.analyticsvidhya.com/blog/2016/01/guide-data-exploration/) [https://www.analyticsvidhya.com/blog/2015/09/hypothesis-testing-explained/](https://www.analyticsvidhya.com/blog/2015/09/hypothesis-testing-explained/)

#### Tips

List missing values of each column

```python
df.apply(lambda x :sum(x.isnull()), axis=0)
df.isnull().sum
```

Boolean Indexing in Pandas

```python
data.loc[(data["Gender"]=="Female") & (data["Education"]=="Not Graduate") & (data["Loan_Status"]=="Y"), ["Gender","Education","Loan_Status"]]
```

#### Cut function for binning

Sometimes numerical values make more sense if clustered together. For example, if we’re trying to model traffic (#cars on road) with time of the day (minutes). The exact minute of an hour might not be that relevant for predicting traffic as compared to actual period of the day like “Morning”, “Afternoon”, “Evening”, “Night”, “Late Night”. Modeling traffic this way will be more intuitive and will avoid overfitting.

Here we define a simple function which can be re-used for binning any variable fairly easily.

```python
#Binning:
def binning(col, cut_points, labels=None):
  #Define min and max values:
  minval = col.min()
  maxval = col.max()

  #create list by adding min and max to cut_points
  break_points = [minval] + cut_points + [maxval]

  #if no labels provided, use default labels 0 ... (n-1)
  if not labels:
    labels = range(len(cut_points)+1)

  #Binning using cut function of pandas
  colBin = pd.cut(col,bins=break_points,labels=labels,include_lowest=True)
  return colBin

#Binning age:
cut_points = [90,140,190]
labels = ["low","medium","high","very high"]
data["LoanAmount_Bin"] = binning(data["LoanAmount"], cut_points, labels)
print (pd.value_counts(data["LoanAmount_Bin"], sort=False))

```

#### Coding nominal data using Pandas

Often, we find a case where we’ve to modify the categories of a nominal variable. This can be due to various reasons:

Some algorithms (like Logistic Regression) require all inputs to be numeric. So nominal variables are mostly coded as 0, 1….(n-1) Sometimes a category might be represented in 2 ways. For e.g. temperature might be recorded as “High”, “Medium”, “Low”, “H”, “low”. Here, both “High” and “H” refer to same category. Similarly, in “Low” and “low” there is only a difference of case. But, python would read them as different levels. Some categories might have very low frequencies and its generally a good idea to combine them. Here I’ve defined a generic function which takes in input as a dictionary and codes the values using ‘replace’ function in Pandas.

```python
#Define a generic function using Pandas replace function
def coding(col, codeDict):
  colCoded = pd.Series(col, copy=True)
  for key, value in codeDict.items():
    colCoded.replace(key, value, inplace=True)
  return colCoded

#Coding LoanStatus as Y=1, N=0:
print 'Before Coding:'
print pd.value_counts(data["Loan_Status"])
data["Loan_Status_Coded"] = coding(data["Loan_Status"], {'N':0,'Y':1})
print '\nAfter Coding:'
print pd.value_counts(data["Loan_Status_Coded"])
```

#### Show missing values

```python
def show_missing_count(data):
    miss_percent = (data.isnull().sum() / len(data)) * 100
    missing = pd.DataFrame({"Percent":miss_percent, 'Count':data.isnull().sum()}).sort_values(by="Percent", ascending=False)
    missing=missing.loc[missing['Percent'] > 0]
    return missing
```

#### Check if there are empty rows or columns and find them

```python
import pandas as pd

df = pd.DataFrame({
  "Accessories": ["Laptop", "Laptop", "Ipad", pd.NA, "Tablet", "Laptop"],
  "customer": ["Andrew", "Andrew", "Tom", "Andrew", "Tobey", pd.NA],
  "quantity": [1, 2, 2, 3, 1, 2],
})

df[df.isnull().any(axis=1)]
```

Output

| index | Accessories | customer | quantity |
| ----- | ----------- | -------- | -------- |
| 3     |             | Andrew   | 3        |
| 5     | Laptop      |          | 2        |

Here `.any()` select a row if one of the columns is 'NA' . Internally a numpy mask `df.isnull().any(axis=1)` is created which outputs following

```
0    False
1    False
2    False
3     True
4    False
5     True
dtype: bool
```

#### List all individual values from categorical columns and their count

```python
df[categorical_columns].melt().groupby(['variable', 'value'])[['value']].count()
```
