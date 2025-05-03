
Create requirements based on the used libs in the project directory- [[Github]] [[conda cheat sheet|conda cheat sheet]]

```bash
pip freeze -q -r requirements.txt | sed '/freeze/,$ d' > requirements-froze.txt
```

Count number of directories in a dir

```bash
find . -mindepth 1 -maxdepth 1 -type d | wc -l
```