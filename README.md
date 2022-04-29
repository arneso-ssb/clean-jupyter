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
in the docker image. The same with the Citrix image. But what about other shared Linux
machines and Windows? Standardize installation path?

On Linux: Install with system python3: `pip install nbstripout` (needs sudo
permissions). Then invoke with `/usr/bin/python3 -m nbstripout` 

On Windows (local PC): Install with the `--user` option. That is:
`pip install --user nbstripout`

The gitconfig line on Windows is then something like this:
```
filter.nbstripout.clean=C:/Users/aei/AppData/Local/Programs/Python/Python39/python.exe -m nbstripout
```

The path to the python executable used in the gitconfig can be found like this:
```
python -c "import sys; print(sys.executable)"
```

Try to remove extra metadata by using:
```
git config filter.nbstripout.extrakeys 'cell.metadata.pycharm metadata.kernelspec'
```

git config:
```
[filter "nbstripout"]
	clean = "C:/Users/aei/AppData/Local/Programs/Python/Python39/python.exe" -m nbstripout
	smudge = cat
	required = true
	extrakeys = cell.metadata.pycharm metadata.kernelspec
[diff "ipynb"]
	textconv = "C:/Users/aei/AppData/Local/Programs/Python/Python39/python.exe" -m nbstripout -t
```

.gitattributes:
```
* text eol=lf
*.ipynb filter=nbstripout
*.ipynb diff=ipynb
```

#### Results
Works, and removes the pycharm metadata. It also has a useful `--strip-empty-cells`
option.

There are some known problems with nbstripout and git pull, see:
https://github.com/kynan/nbstripout/issues/108

## Using pre-commit hook
You need to add the file twice. And seems more complicated for a novice end user.

# Preliminary conclusion
Use .gitattributes and .gitconfig with nbstripout as described above.
