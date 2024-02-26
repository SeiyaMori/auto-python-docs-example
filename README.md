# Auto generate and deploy Python documentation to Github Pages using Sphinx

## Step-by-step guide

### Task 1 - Set up new Github repo

1. Create a new Github rep. Remember to add a protection rule for the main branch.
2. Create a new branch called `gh-pages`.
3. Create a new Github Pages website via Settings. Select source as "Deploy from branch" and choose the "gh-pages" branch and its "/(root)" folder.
4. Create a new dev branch and clone to local. Open local dev branch in IDE.
5. Create two new folders. One is the source folder containing all your python files (name as you like). Add your python files here if you have any. The other new folder is for documentation and should be named "docs".

### Task 2 - Install Sphinx

1. Open a new terminal and execute "pip install sphinx".
2. Execute "pip install sphinx sphinx-rtd-theme" to get the Read the Docs style.

### Task 3 - Run Sphinx and configure project files

1. Execute "cd docs", then execute "sphinx-quickstart". This will generate a bunch files in the "docs" folder that are required for the auto doc generation. You will be presented with a bunch of setup questions. Typically, separate source and build directories = n, project name = [your project name], author name = [author's name], project release = [e.g. 0.0.1], project language = en.
2. In the "docs" folder, open the "conf.py" file and add the following lines of code near the top.

```css
import os
import sys
sys.path.insert(0, os.path.abspath('..'))
```

4. Add the following extensions in the extensions list.

```css
extensions = [
    'sphinx.ext.autodoc',
    'sphinx.ext.viewcode',
    'sphinx.ext.napoleon'
]
```

5. Change the html theme to:

```css
html_theme = 'sphinx_rtd_theme'
```

6. Save the "conf.py" file.

7. Go back to the root level of the repo (exit the "docs" folder by executing "cd ..") then execute "sphinx-apidoc -o docs [your source folder name]/". This will generate some files in the "docs" folder related to the python scripts in your source folder.
8. In the "index.rst" file, add the word "modules" and save.
9. In the "modules.rst" file, add the names of the python files you want to include the documentation (do not include the ".py" extension).
10. Using the terminal, navigate to the "docs" folder and execute ".\make.bat html". This will generate your documentation which can be found in the "_build" folder.