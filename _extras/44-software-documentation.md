---
title: "Software Documentation using Sphinx"
teaching: 15
exercises: 10
questions:
- "What tools can we use to document our software package?"
- "How to make software documentation public?"
---

## What options do we have for software documentation

## Using Sphinx

### Installing Sphinx


[Sphinx installation instructions](https://docs.readthedocs.io/en/stable/intro/getting-started-with-sphinx.html).

Install Sphinx using `pip`:

~~~
$ source venv/bin/activate
$ pip install sphinx
$ pip install sphinx_rtd_theme
$ pip install myst-parser
$ pip freeze > requirements.txt
~~~
{: .language-bash}

Two other installations, `sphinx_rtd_theme` and `myst-parser` are packages for changing the appearance of the
documentation site with the 'Read the Docs' theme and for using markdown formatting in the documentation.

Sphinx requires a specific folder structure. Create documents folder, go there and launch `sphinx-quickstart` wizaard

~~~
$ mkdir docs
$ cd docs
$ sphinx-quickstart --quiet --project=LCAnalyzer --author=alex -v 0.1
~~~
{: .language-bash}

~~~
Finished: An initial directory structure has been created.

You should now populate your master file /mnt/Data/Work/GitHub/InterPython_Workshop_Example/docs/index.rst and create other documentation
source files. Use the Makefile to build the docs, like so:
   make builder
where "builder" is one of the supported builders, e.g. html, latex or linkcheck.
~~~
{: .output}

Launching `sphinx` this way we are telling it to skip most of the wizard questions (`--quiet` flag; in the most cases 
the default answers are the most suitable ones), and giving it only the bare minimum of information that Sphinx requires: the project name
`--project=LCAnalyzer`, the name of the author (`--author=alex`) and the version of our software (`-v 0.1`).

As we execute this command, Sphinx creates a number of new files in the `docs` folder.
~~~
$ ls -l
~~~
{: .language-bash}

~~~
total 10
drwxrwxrwx 1 root root   0 Jul  3 18:27 _build
-rwxrwxrwx 1 root root 965 Jul  3 18:27 conf.py
-rwxrwxrwx 1 root root 446 Jul  3 18:27 index.rst
-rwxrwxrwx 1 root root 800 Jul  3 18:27 make.bat
-rwxrwxrwx 1 root root 634 Jul  3 18:27 Makefile
drwxrwxrwx 1 root root   0 Jul  3 18:27 _static
drwxrwxrwx 1 root root   0 Jul  3 18:27 _templates
~~~
{: .output}

The files that we are going to edit are `conf.py` and `index.rst`. Let's start with the `conf.py`:
open it with your favorite text editor and have a look.

~~~
$ gedit conf.py
~~~
{: .language-bash}

On the top of the file you will see the information you provided when creating the docs: the project name,
author's name and release version. If you made an error or want to change something, you can always do it here.
~~~
project = 'LCAnalyzer'
copyright = '2024, alex'
author = 'alex'

version = '0.1'
release = '0.1'
~~~
{: .language-python}
Below you will see an empty list called `extensions`. Extensions allow you to make more out of
your documentation, some of them are [built in](https://www.sphinx-doc.org/en/master/usage/extensions/index.html#built-in-extensions),
while others are developed by third parties. 

We will use the following extensions:
- `sphinx.ext.autodoc` and `sphinx.ext.autosummary` for turning docstrings in your code
as part of documentation;
- `sphinx.ext.napoleon` for supporting different styles of docstrings;
- `sphinx.ext.viewcode` for including highlighed source code in your docs;
- and external 'sphinx_rtd_theme' and 'myst_parser' extensions for using a popular Read The Docs html theme.

The resulting `extensions` list will look like this:
~~~
...
extensions = ['sphinx_rtd_theme','myst_parser',
		'sphinx.ext.autodoc',
		'sphinx.ext.autosummary',
		'sphinx.ext.napoleon',
		'sphinx.ext.viewcode']
...
~~~
{: .language-python}

Finally, in `conf.py` we can change the appearance of our documentation site using themes. 
In `conf.py` you will find a line that states `html_theme = 'alabaster'`.
You can change this parameter to any of the Sphinx built-in themes, listed
[here](https://www.sphinx-doc.org/en/master/usage/theming.html#builtin-themes), however, 
we will use the common design theme is the one developed by 'Read the Docs', which is
also a popular software documentation hosting:
~~~
...
html_theme = "sphinx_rtd_theme"
~~~
{: .language-python}

At this point we can save the file and move to the documentation itself.


### Creating documentation

> ## Optional Exercise: Enhancing our Package Metadata
>
> An exercise
{: .challenge}

{% include links.md %}
