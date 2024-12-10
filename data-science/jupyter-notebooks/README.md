# Jupyter Notebooks

## Some tips about Jupyter Notebooks

### Show multiple outputs

By default jupyter notebooks only outputs once per cell. If you want to display multiple outputs use the following code snippet\`\`\`python

```python
from IPython.core.interactiveshell import InteractiveShell
InteractiveShell.ast_node_interactivity = "all"
```

If you want to print multiple varibles in one line then just put the names of varibles with comma in between

### Alert Boxes

```markdown
<div class="alert alert-danger">
    <b> Danger </b> Danger alert box.
</div>
```

### Run SQL queries

Use JupySQL plugin

[https://github.com/ploomber/jupysql](https://github.com/ploomber/jupysql)

{% embed url="https://jupysql.ploomber.io/en/latest/quick-start.html" %}

### Print Markdown in Jupyter Notebook

```python
from IPython.display import Markdown, display

display(Markdown('**bold**'))
```
