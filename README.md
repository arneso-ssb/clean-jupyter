# clean-jupyter
Repo for testing automatically cleaning of Jupyter notebooks.

## Using .gitattributes and git config
### nbconvert
Based on https://zhauniarovich.com/post/2020/2020-10-clearing-jupyter-output-p3/

git config:
```
[filter "jupyternotebook"]
	clean = jupyter nbconvert --to=notebook --ClearOutputPreprocessor.enabled=True --stdout %f
	required

```

.gitattributes:
```
* text eol=lf
*.ipynb filter=jupyternotebook
```

#### Results
Works, but `git difftool` on `*.ipynb`-files on Windows gives this error:
```
[NbConvertApp] Converting notebook clean_test.ipynb to notebook
fatal: clean_test.ipynb: smudge filter jupyternotebook failed
```
Problem fixed by setting `* text eol=lf` in `.gitattributes`.

Does not strip metadata like:
```
   "metadata": {
    "pycharm": {
     "name": "#%%\n"
    }
```


### nbstripout
Git repo: https://github.com/kynan/nbstripout

Problem with installation path: The nbstripout executable needs to be available
for the git filter commands. That means that nbstripout should be installed globally
on the machine, and not in one of the virtual environments. The virtual environments
can change and be deleted.

On Dapla and Jupyter this is not a problem, since we can install it in a common place
in the docker image. But what about other shared Linux machines and Windows? Standardize
installation path?



## Using pre-commit hook
