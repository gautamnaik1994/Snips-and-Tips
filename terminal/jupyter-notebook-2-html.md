# Jupyter Notebook 2 HTML

Following is the untested Github Action code to convert and host Jupyter notebook on Github Pages. 

```yaml
name: Build and Publish Jupyter Notebook

on:
  push:
    branches:
      - main  # change this to your default branch

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Repository
        uses: actions/checkout@v2

      - name: Set up Python
        uses: actions/setup-python@v2
        with:
          python-version: 3.8

      - name: Install Dependencies
        run: |
          python -m pip install --upgrade pip
          pip install nbconvert

      - name: Convert Notebook to HTML
        run: |
          jupyter nbconvert --to html path/to/your/notebook.ipynb

      - name: Deploy to GitHub Pages
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./path/to/converted/html

```
