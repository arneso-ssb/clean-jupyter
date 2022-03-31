# clean-jupyter
Repo for testing automatically cleaning of Jupyter notebooks.

## Using .gitattributes and git config
Based on https://zhauniarovich.com/post/2020/2020-10-clearing-jupyter-output-p3/

git config:
```
[filter "jupyternotebook"]
	clean = jupyter nbconvert --to=notebook --ClearOutputPreprocessor.enabled=True --stdout %f
	required

```

.gitattributes:
```
* text=auto
*.ipynb filter=jupyternotebook
```

## Using pre-commit hook