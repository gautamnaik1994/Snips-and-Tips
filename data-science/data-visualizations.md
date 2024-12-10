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

## Plot a Gantt Chart using Plotly

|      | symbol     | buy_date   | sell_date  | buy_price   | sell_price  | quantity | days_diff | profit |
| ---- | ---------- | ---------- | ---------- | ----------- | ----------- | -------- | --------- | ------ |
| 1510 | PERSISTENT | 2023-04-25 | 2023-06-09 | 4448.000000 | 4860.000000 | 2.0      | 7 days    | 824.0  |
| 367  | BEL        | 2020-05-28 | 2020-07-29 | 21.390625   | 29.890625   | 467.0    | 16 days   | 3969.5 |
| 1593 | POONAWALLA | 2022-06-10 | 2022-06-13 | 247.750000  | 230.625000  | 40.0     | 29 days   | -685.0 |
| 493  | CIPLA      | 2021-11-24 | 2021-11-25 | 882.000000  | 888.500000  | 11.0     | 2 days    | 71.5   |
| 65   | ADANIENT   | 2022-05-31 | 2022-06-01 | 2166.000000 | 2148.000000 | 4.0      | 5 days    | -72.0  |

```python
import plotly.express as px

fig = px.timeline(dataframe, x_start="buy_date", x_end="sell_date", y="symbol", color="symbol")
fig.update_layout(xaxis_rangeslider_visible=True)
fig.show()
```

## Plot Line equation using matplotlib

```python
import numpy as np
import matplotlib.pyplot as plt

x_values = np.linspace(-10, 10, 100)

y_values = 3 * x_values + 4

# Plot the graph
plt.plot(x_values, y_values, label='y = 3x + 4')
plt.xlabel('x')
plt.ylabel('y')
plt.title('Plot of y = 3x + 4')
plt.grid(True)
plt.legend()
plt.show()
```

## Crosstab Plotly subplot

```python
fig = make_subplots(rows=1, cols=2, subplot_titles=("Term vs Loan Status", "Grade vs Loan Status"))

term_vs_loan_status = pd.crosstab(
    pdf['term'], pdf['loan_status'], normalize="index")
grade_vs_loan_status = pd.crosstab(
    pdf['grade'], pdf['loan_status'], normalize="index")


colors = ['#636EFA', '#EF553B', '#00CC96', '#AB63FA', '#FFA15A',
          '#19D3F3', '#FF6692', '#B6E880', '#FF97FF', '#FECB52']

for i, col in enumerate(term_vs_loan_status.columns):
    fig.add_trace(go.Bar(name=col, x=term_vs_loan_status.index,
                  y=term_vs_loan_status[col], marker_color=colors[i % len(colors)], showlegend=True), row=1, col=1)

for i, col in enumerate(grade_vs_loan_status.columns):
    fig.add_trace(go.Bar(name=col, x=grade_vs_loan_status.index,
                  y=grade_vs_loan_status[col], marker_color=colors[i % len(colors)], showlegend=False), row=1, col=2)

fig.update_layout(
    height=500, width=1200,
    title_text="Crosstab Plots of Different Features",
    barmode='stack',
    legend=dict(
        yanchor="top",
        y=-0.2,
        xanchor="center",
        x=0.5,
        orientation="h"
    )
)
fig.show();
```

## Polar Plot

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

def create_polar_plot(data: np.ndarray, features: list, cluster_label: int):
    num_vars = len(features)

    # Compute angle of each axis (in radians)
    angles = np.linspace(0, 2 * np.pi, num_vars, endpoint=False).tolist()

    # The plot is made in a circular (polar) form, so we need to "close the loop"
    data = np.concatenate((data, [data[0]]))
    angles += angles[:1]

    fig, ax = plt.subplots(figsize=(6, 6), subplot_kw=dict(polar=True))

    ax.fill(angles, data, color='blue', alpha=0.25)
    ax.plot(angles, data, color='blue', linewidth=2)

    ax.set_xticks(angles[:-1])
    ax.set_xticklabels(features)

    plt.title(f'Cluster {cluster_label}', size=20, color='blue', y=1.1)

    plt.show()

def create_combined_polar_plot(normalized_means, features, cluster_labels):
    num_vars = len(features)
    angles = np.linspace(0, 2 * np.pi, num_vars, endpoint=False).tolist()
    angles += angles[:1]

    fig, ax = plt.subplots(figsize=(12, 12), subplot_kw=dict(polar=True))

    for i, data in enumerate(normalized_means):
        data = np.concatenate((data, [data[0]]))
        ax.fill(angles, data, alpha=0.25, label=f'Cluster {cluster_labels[i]}')
        ax.plot(angles, data, linewidth=2)

    ax.set_xticks(angles[:-1])
    ax.set_xticklabels(features)

    plt.title('Cluster Comparison', size=20, y=1.1)
    plt.legend(loc='upper right', bbox_to_anchor=(1.1, 1.1))
    plt.show()
```

**Usage**

````python
cluster_means = df.group_by("y_kmeans").mean().to_pandas().drop(["y_kmeans"], axis=1)
scaler = MinMaxScaler()
normalized_means = scaler.fit_transform(cluster_means)

features =cluster_means.columns.to_list()

##Single polar plot
for cluster in range(cluster_means.shape[0]):
    create_polar_plot(normalized_means[cluster], features, cluster)

##Combine Polar Plot```python
cluster_labels = df["y_kmeans"].unique().to_list()
create_combined_polar_plot(normalized_means, features, cluster_labels)
````

## Seasonal Decomposition using Plotly

```python

from plotly.subplots import make_subplots
from statsmodels.tsa.seasonal import DecomposeResult, seasonal_decompose

def plot_seasonal_decompose(result:DecomposeResult, dates:pd.Series=None, title:str="Seasonal Decomposition"):
    x_values = dates if dates is not None else np.arange(len(result.observed))
    return (
        make_subplots(
            rows=4,
            cols=1,
            subplot_titles=["Observed", "Trend", "Seasonal", "Residuals"],
        )
        .add_trace(
            go.Scatter(x=x_values, y=result.observed, mode="lines", name='Observed'),
            row=1,
            col=1,
        )
        .add_trace(
            go.Scatter(x=x_values, y=result.trend, mode="lines", name='Trend'),
            row=2,
            col=1,
        )
        .add_trace(
            go.Scatter(x=x_values, y=result.seasonal, mode="lines", name='Seasonal'),
            row=3,
            col=1,
        )
        .add_trace(
            go.Scatter(x=x_values, y=result.resid, mode="lines", name='Residual'),
            row=4,
            col=1,
        )
        .update_layout(
            height=900, title=f'<b>{title}</b>', margin={'t':100}, title_x=0.5, showlegend=False
        )
    )


decomposition = seasonal_decompose(data['#Passengers'], model='additive', period=12)
fig = plot_seasonal_decompose(decomposition, dates=data['Month'])
fig.show()

```
