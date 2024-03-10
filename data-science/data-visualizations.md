# Data Visualizations

## Dataviz library

- Pandas-Profiling
- Sweetviz
- Autoviz
- D-Tale

## Subplot syntax

### To plot 3 x 2 plots

```python
variables = ["Age", "Education", "Usage", "Fitness", "Income", "Miles"]

fig, axes = plt.subplots(2, 3, figsize=(20, 12))

for i in range(2):
    for j in range(3):
        variable = variables[i * 3 + j]
        sns.boxplot(ax=axes[i, j], data=df, x="Product", y=variable, hue="Gender")
        axes[i, j].set_title(f"Gender wise {variable} vs Product")

plt.show();
```

### Subplots using single line

```python
data.plot(kind='density', subplots=True, layout=(3, 3), sharex=False)
```

To generate box plot with solid background color

```python
colors = dict(boxes='lightblue', whiskers='dimgrey', medians='dimgrey', caps='dimgrey')
df.plot(kind='box', subplots=True, layout=(3, 4), sharex=False, figsize=(12, 15), patch_artist=True, color=colors);
plt.suptitle("Outliers", y=0.92, fontsize=14);
```

### Add a Pie chart inside the subplot

```python
unique_users["Gender"].value_counts(normalize=True )[:10].plot(kind="pie", autopct='%1.1f%%', startangle=90, ax=axes[1,0])
axes[1,0].set_title("Gender Distribution");
```

### Load top n values in seaborn countplot

```python
sns.countplot(x="Product_Category", data=df,
    order=df["Product_Category"].value_counts().iloc[:10].index)
```

### Plot a Gantt Chart

<table border="1" class="dataframe">  <thead>    <tr style="text-align: right;">      <th></th>      <th>symbol</th>      <th>buy_date</th>      <th>sell_date</th>      <th>buy_price</th>      <th>sell_price</th>      <th>quantity</th>      <th>days_diff</th>      <th>profit</th>    </tr>  </thead>  <tbody>    <tr>      <th>1510</th>      <td>PERSISTENT</td>      <td>2023-04-25</td>      <td>2023-06-09</td>      <td>4448.000000</td>      <td>4860.000000</td>      <td>2.0</td>      <td>7 days</td>      <td>824.0</td>    </tr>    <tr>      <th>367</th>      <td>BEL</td>      <td>2020-05-28</td>      <td>2020-07-29</td>      <td>21.390625</td>      <td>29.890625</td>      <td>467.0</td>      <td>16 days</td>      <td>3969.5</td>    </tr>    <tr>      <th>1593</th>      <td>POONAWALLA</td>      <td>2022-06-10</td>      <td>2022-06-13</td>      <td>247.750000</td>      <td>230.625000</td>      <td>40.0</td>      <td>29 days</td>      <td>-685.0</td>    </tr>    <tr>      <th>493</th>      <td>CIPLA</td>      <td>2021-11-24</td>      <td>2021-11-25</td>      <td>882.000000</td>      <td>888.500000</td>      <td>11.0</td>      <td>2 days</td>      <td>71.5</td>    </tr>    <tr>      <th>65</th>      <td>ADANIENT</td>      <td>2022-05-31</td>      <td>2022-06-01</td>      <td>2166.000000</td>      <td>2148.000000</td>      <td>4.0</td>      <td>5 days</td>      <td>-72.0</td>    </tr>  </tbody></table>

```python
import plotly.express as px

fig = px.timeline(dataframe, x_start="buy_date", x_end="sell_date", y="symbol", color="symbol")
fig.update_layout(xaxis_rangeslider_visible=True)
fig.show()
```
![Gantt Plot](/.gitbook/assets/gantt.png "Gantt Plot")