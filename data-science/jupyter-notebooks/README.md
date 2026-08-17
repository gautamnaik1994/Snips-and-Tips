# Jupyter notebooks

## Tips for Jupyter notebooks

### Show multiple outputs

By default, Jupyter notebooks display one output per cell. To display multiple outputs, use:

```python
from IPython.core.interactiveshell import InteractiveShell
InteractiveShell.ast_node_interactivity = "all"
```

To print multiple variables on one line, separate their names with commas.

### Alert boxes

```markdown
<div class="alert alert-danger">
    <b> Danger </b> Danger alert box.
</div>
```

### Run SQL queries

Use the JupySQL plugin:

[JupySQL GitHub repository](https://github.com/ploomber/jupysql)

{% embed url="https://jupysql.ploomber.io/en/latest/quick-start.html" %}

### Print Markdown in a Jupyter notebook

```python
from IPython.display import Markdown, display

display(Markdown('**bold**'))
```
