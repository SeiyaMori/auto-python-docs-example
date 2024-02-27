# Auto generate and deploy Python documentation to Github Pages using Sphinx

## Credit to [ ]

## Pre-requisites

- Windows OS assumed.

## Step-by-step guide

### Task 1 - Set up new Github repo

1. Create a new Github repo and remember to add a protection rule for the `main` branch.
2. Create a new branch called `gh-pages`.
3. Create a new Github Pages website via Settings and select *Deploy from branch* and choose the `gh-pages` branch and its `/(root)` folder as the website's source.
4. Create a new development branch and create two folders:
```
├── docs
├── my_module
    ├── __init__.py
    ├── file_one.py
    ├── file_two.py
```

**docs** - An empty folder that will be populated by Sphinx when it is run. The folder will contain configuration files and importantly, the auto generated documentation files.

**my_module** - The python module for which you want to generate the documentation - please change the name to your python module name. The tree structure above illustrates a typical python module, including the__init__.py file to signify that this folder is a Python module.

### Task 2 - Install Sphinx

1. Clone the development branch to your local machine and open it in your preferred editor.
2. Run `pip install sphinx` to install Sphinx on your machine.
3. Run `pip install sphinx sphinx-rtd-theme` to install the Read the Docs documentation style.

### Task 3 - Run Sphinx and configure project files

This will generate key files and folders in the **docs** folder that are required for the auto doc generation. 

Inside a terminal:
1. Navigate to the **docs** folder and run`sphinx-quickstart`. Enter the following information when prompted:

| Prompt | Your input |
|-----------------|----------------|
| Separate source and directories | n |
| Project name | [your project name] |
| Author name | [author's name] |
| Project release | [e.g. 0.0.1] |
| Project language | en |

Inside your editor:

2. In the **docs** folder, open the `conf.py` file and add the following lines of code near the top.

**[Ref A]**

```python
import os
import sys
sys.path.insert(0, os.path.abspath('..'))
```

> **Important:** When you try to generate the documentation, you may get the following error: `No module named [your desired python file name]`.
> One of the main reasons for this is to do with how you have structured your python module. For modules where there is a \_\_init__.py, please use the **[Ref A]** code snippet. If your module does not have a \_\_init__.py file, you must specify the folder in which your python file(s) exist, as shown in code snippet **[Ref B]** `...abspath('../my_module')`.

Python module structure without __init__.py file.

```
├── my_module
    ├── file_one.py
    ├── file_two.py
```

**[Ref B]**

```python
...
sys.path.insert(0, os.path.abspath('../my_module'))
```

4. Add the following extensions in the extensions list.

```python
extensions = [
    'sphinx.ext.autodoc',
    'sphinx.ext.viewcode',
    'sphinx.ext.napoleon'
]
```

5. Change the html theme.

```python
html_theme = 'sphinx_rtd_theme'
```

6. Save the `conf.py` file.

Inside a terminal:

7. Navigate to the root level of your repo and run `sphinx-apidoc -o docs [my module name]/`. This will generate some files in the **docs** folder related to the python scripts in your source folder.

Inside your editor:

8. In the `index.rst` file, add the word `modules` and save. The `index.rst` file should look similar to below:

```
Welcome to test's documentation!
================================
.. toctree::
   :maxdepth:  2
   :caption: Contents:

   modules

Indices and tables
==================
* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
```

9. In the `modules.rst` file, add the names of the python files you want to include the documentation (do not include the `.py` extension). Example below:

```
my_module
===========
.. toctree::
   :maxdepth:  4

   file_one
   file_two
```

Inside a terminal: 

11. Navigate to the **docs** and run `.\make.bat html` (this command is for Windows OS). This will generate your documentation which can be found in the **docs/_build** folder.
12. Commit and push your changes to the remote repo.

### Task 4 - Set up Github Workflow

In editor:
1. Create new folders called `.github/workflows`.
2. Inside the `workflows`  folder, create a new file called `build-sphinx-docs.yml`.
3. Copy and paste the following code into the new file. Save the file.

```yml
# This is a basic workflow to help you get started with Actions

name: build-sphinx-docs

# Controls when the workflow will run
on:
  # Triggers the workflow on push or pull request events but only for the main branch
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs: 
  # This workflow contains a single job called "build"
  build: 
    # The type of runner that the job will run on
    runs-on: ubuntu-latest

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
      - uses: actions/checkout@v2

      - name: Set up Python 3.10
        uses: actions/setup-python@v2
        with:
           python-version: "3.10"
      # Runs a single command using the runners shell
      - name: Run a one-line script
        run: echo Hello, world!
        
      - name: Install dependencies
        run: | 
          python -m pip install --upgrade pip
          pip install -r requirements.txt
      - name: Build HTML
        run: | 
          cd docs/
          make html
      - name: Run ghp-import
        run: | 
          ghp-import -n -p -f docs/_build/html

      # Runs a set of commands using the runners shell
      - name: Run a multi-line script
        run: |
          echo Add other actions to build,
          echo test, and deploy your project.
```

4. At the root of the branch, create a new file called `requirements.txt` and paste the following into that file:

```
sphinx
sphinx_rtd_theme
ghp-import
pytest
```

5. Commit and push changes to the remote repo.

In Github:

6. Make sure your permission settings allow the Github Actions to read and write. Go to Settings > Actions > General > Workflow permissions and enable **Read and write permissions**. Save these settings.

7. Merge the development branch into the `main` branch (only then will you see the new Workflow in the Actions tab in Github).

> **Note:** When you check the Actions tab, you may see that the `build-sphinx-docs` workflow may have failed. This may occur if you pulled the main branch into your development branch before you created your pull request (as per common practice). By pulling the main branch, you will have automatically triggered the workflow. You can ignore this.