# Data Visualizations

## Dataviz library

* Pandas-Profiling
* Sweetviz
* Autoviz
* D-Tale

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

