
## Remove Jupyter Notebook from Language Analysis

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


## Git LFS

Git Large File Storage (LFS) replaces large files such as audio samples, videos, datasets, and graphics with text pointers inside Git, while storing the file contents on a remote server like GitHub.com or GitHub Enterprise.

1.  and install the Git command line extension. Once downloaded and installed, set up Git LFS for your user account by running:
```bash
git lfs install
```

2. In each Git repository where you want to use Git LFS, select the file types you'd like Git LFS to manage (or directly edit your .gitattributes). You can configure additional file extensions at anytime.

```bash
git lfs track "*TCI_20m.jp2"
```

```bash
git add .gitattributes
```

3. There is no step three. Just commit and push as you normally would; for instance, if your current branch is named `main`:
```bash
git add src/data/sentinel2/T32TLR_20241030T103151_TCI_20m.jp2
git commit -m "Sentinel 2 L2A product for basic notebook demo"
git push
```