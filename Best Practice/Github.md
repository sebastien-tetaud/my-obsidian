
### Remove Jupyter Notebook from Language Analysis

GitHub's language analysis is based on the files present in your repository, and Jupyter Notebook (`.ipynb`) files tend to dominate because they contain a mix of code, markdown, and metadata. To exclude Jupyter notebooks from GitHub's language statistics, you can use a `.gitattributes` file.

1. Create a gitattributes file

```Bash
touch .gitattributes
```

2. add the following rule:

```text
*.ipynb linguist-detectable=false
```

3. push the change

```Bash
git add .gitattributes
git commit -m "Exclude Jupyter notebooks from language stats"
git push
```

